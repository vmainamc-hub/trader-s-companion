# 10 — Manual Trader Specification

**Routes.** `/trade` (symbol picker) · `/trade/$symbol` (ticket + chart) ·
`/trade/positions` (open contracts) · `/history` (settled contracts)

The trader is the EXECUTE step of the loop. Everything else in the product
feeds it: Markets hands over a symbol, Analysis hands over a symbol plus a
direction, Digits hands over a contract type and barrier, the AI analyst hands
over a proposed setup. The ticket must therefore accept a fully or partially
prefilled configuration from a URL and never silently discard it.

```
┌ TRADE · R_75  Volatility 75 Index ── 1236.107 ▲0.42% ─── DEMO ───────────┐
│ ┌ CHART (shared workspace component, read-only toolbar) ──┐ ┌ TICKET ───┐│
│ │                                                          │ │ TYPE     ││
│ │        price series + entry/exit markers                 │ │ [Rise/Fall▾]│
│ │        open position lines, barrier lines                │ │ DURATION ││
│ │                                                          │ │ [5][ticks▾]│
│ │                                                          │ │ STAKE    ││
│ │                                                          │ │ [10 USD] ││
│ └──────────────────────────────────────────────────────────┘ │ BARRIER  ││
│ ┌ PROPOSAL ────────────────────────────────────────────────┐ │ [—]      ││
│ │ payout 19.42 USD   return 94.2%   ask 10.00   id …a71f   │ │──────────││
│ │ recomputed on every parameter change and every tick      │ │ RISE  ▲  ││
│ └──────────────────────────────────────────────────────────┘ │ FALL  ▼  ││
├ OPEN POSITIONS (3) ──────────────────────────────────────────┴──────────┤
│ SYMBOL  TYPE      STAKE  ENTRY     CURRENT   P/L      REMAINING  [SELL]  │
│ R_75    RISE      10.00  1235.9    1236.1    +4.21    3 ticks    [SELL]  │
└──────────────────────────────────────────────────────────────────────────┘
```

## Contract configuration

Available types come from `contracts_for` for the selected symbol — never from
a hardcoded list, because availability varies by symbol and by landing company.
The form is generated from the returned contract descriptor: duration units and
their min/max, barrier requirements (none, one, two, relative or absolute),
stake min/max, and payout limits. An unsupported combination is disabled with
the API's own reason shown, not hidden.

## Proposal lifecycle

`proposal` is subscribed, not polled. The subscription is replaced (old one
forgotten first) whenever any parameter changes, debounced at 300ms so a user
dragging a stake slider does not open dozens of streams. The displayed payout
is always tied to the `id` that would be sent to `buy`; if the id changes
between render and click, the purchase is re-validated before submission.

## Purchase safety

`buy` carries the `price` the user actually saw as the maximum acceptable ask.
On a real account the purchase requires an explicit confirmation step showing
account name, stake, payout, and the words "real money". On demo it executes on
first click. The demo/real state is shown inside the confirm control itself, so
the affordance changes shape between modes rather than only changing colour.

Duplicate protection: the buy button locks from click until the response
arrives; a timed-out request is never retried automatically, because a retry
can double a position. The user is shown "outcome unknown — checking portfolio"
and the client reconciles against `portfolio`.

## Open positions

Each open contract has its own `proposal_open_contract` subscription providing
current spot, current P/L, and expiry. Positions are sellable when the contract
descriptor allows it; the sell control shows the indicative sell price and the
same confirm discipline on real accounts. On settlement the row animates once
into its result colour and moves to history after a short delay.

## Trade history

`profit_table` paginated, with client-side filters on symbol, contract type,
result, and date range, and a link from each row back to the contract detail.
Statistics on the history page are computed from the returned rows only, with
the sample period stated. Demo and real history are never mixed: switching
account switches the entire dataset.

## States

No symbol selected → picker with favourites and recents. `contracts_for`
failed → the ticket is disabled with a retry, chart still streams. Proposal
error (stake below minimum, market closed) → inline message on the offending
field. Disconnected → ticket disabled, positions frozen with a stale timestamp
and a `RECONNECTING` ribbon; nothing is sellable while the socket is down.
Market closed → ticket replaced by the next open time from `active_symbols`.

## Mobile

Chart collapses to a compact sparkline with a "expand chart" control. The
ticket is a bottom sheet with a persistent summary bar (type, stake, payout)
and the two direction buttons pinned above the safe area. Positions are cards,
not a table.

## API dependencies

`active_symbols`, `contracts_for`, `ticks`, `ticks_history`, `proposal`
(subscribe), `buy`, `sell`, `portfolio`, `proposal_open_contract` (subscribe),
`profit_table`, `balance` (subscribe). Requires the `trade` scope; read-only
sessions render the ticket in a disabled state explaining the missing scope.
