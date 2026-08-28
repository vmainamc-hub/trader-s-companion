# 15 — Portfolio Specification

**Route.** `/portfolio` · `/portfolio/contracts/$id`

Portfolio answers "where do I stand", across manual trades and bots, for the
currently selected account only.

```
┌ PORTFOLIO · Demo USD ────────────────────────── as of 09:41:07 ──────────┐
│ balance 10 218.40    equity 10 231.02    open exposure 42.00             │
│ today +18.60 (0.18%)   week +112.40   month +309.15   all time +1 204.02 │
├ OPEN CONTRACTS (4) ──────────────────────────────────────────────────────┤
│ SOURCE   SYMBOL  TYPE       STAKE  ENTRY    NOW      P/L     EXPIRY      │
│ manual   R_75    RISE       10.00  1235.9   1236.1   +4.21   3 ticks     │
│ bot·DO4  R_75    DIGITOVER   1.00  1236.0   —        —       1 tick      │
├ ALLOCATION ──────────────────┬ RESULTS BY SOURCE ────────────────────────┤
│ R_75      68% ████████        │ manual  128 trades  54.7%  +212.10       │
│ frxEURUSD 22% ███             │ bots    486 trades  51.9%  +97.05        │
│ others    10% █               │ (live only — demo and backtest excluded) │
└──────────────────────────────────────────────────────────────────────────┘
```

## Sources of truth

Balance comes from the subscribed `balance` stream. Open contracts come from
`portfolio` on load, then each is tracked by `proposal_open_contract`. Closed
results come from `profit_table` and `statement`. Equity is balance plus the
indicative value of open contracts and is labelled as indicative, because
indicative values move between ticks and are not realisable.

Attribution of a contract to a bot is application-side: the executor records
the contract id it bought against the bot run. Contracts opened outside
PrecisionEdge (on Deriv's own apps) appear as source `external` rather than
being hidden or misattributed.

## Period figures

Today, week, month, and all-time are computed from `profit_table` over the
matching range in the account's own currency, with the account timezone stated.
Where the API's history is shorter than the requested range, the real range is
printed instead of extrapolating.

## Contract detail — `/portfolio/contracts/$id`

Full record for a single contract: parameters, entry and exit spots, barrier,
tick-by-tick path with entry and exit markers, payout, and — if it came from a
bot — the log excerpt around the decision that opened it. This link between a
result and its cause is the point of the page.

## Export

CSV export of open and closed contracts for the selected range with a stable
column set, generated client-side from data already fetched.

## States

Empty account → an honest zero state with a link to the trader, no synthetic
sample rows. Currency without any trades in range → "no trades in this period".
Disconnected → last-known values with the timestamp and a stale marker on every
number; open-contract P/L is greyed rather than frozen-looking-live.

## Mobile

Summary cards, then open contracts as cards with the sell action, then the
period figures; allocation and source breakdowns collapse into an accordion.

## API dependencies

`balance` (subscribe), `portfolio`, `proposal_open_contract` (subscribe),
`profit_table`, `statement`, `active_symbols` for display names.
