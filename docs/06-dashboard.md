# 06 — Dashboard Specification

**Purpose.** Answer "what is happening with my trading account right now?" in
under five seconds.
**User.** All. **Primary action.** Resume whatever was in flight.
**Route.** `/`

## Layout (desktop, 12-column)

```
┌ ACCOUNT ─────────────────┬ TODAY ────────┬ QUICK ACTIONS ─────────────────┐
│ Balance   10,000.00 USD  │ P/L   +42.10  │ [Trade] [Analyse] [Digits]     │
│ Equity    10,038.40      │ Trades  14    │ [AI Analysis] [Bots] [Build]   │
│ Open P/L  +38.40         │ Win rate 57%  │                                 │
├ OPEN POSITIONS ──────────────────────────┴─────────────────────────────────┤
│ MARKET  CONTRACT  ENTRY   CURRENT  STAKE  P/L    REMAINING  STATUS   [ ⋯ ] │
│ R_75    RISE      1234.5  1236.1   10.00  +2.40  00:23      OPEN     Sell  │
├ BOT ACTIVITY ─────────────────────┬ MARKET OVERVIEW ──────────────────────┤
│ BOT      MKT  STATE   TRADES  P/L │ ★ R_75   1236.10  +0.42%  ▲          │
│ Mean-Rev R_50 RUNNING 22     -3.1 │ ★ R_100  8712.44  -0.18%  ▼          │
├ ALERTS ───────────────────────────┼ RECENT ACTIVITY ──────────────────────┤
│ ⚑ R_75 crossed 1236.00            │ 09:41 BUY  R_75 RISE 10.00           │
│ ⚑ Bot Mean-Rev hit daily loss cap │ 09:39 BOT  Mean-Rev stopped          │
└───────────────────────────────────┴───────────────────────────────────────┘
```

## Widgets

| Widget | Data source | Empty state |
| --- | --- | --- |
| Account summary | `balance` (subscribed), `portfolio` | — (always available once authorised) |
| Today / week P/L | `profit_table` aggregated client-side | "No trades yet today" |
| Open positions | `portfolio` + `proposal_open_contract` per contract | "No open positions · Place a trade" |
| Bot activity | Local bot runtime store | "No bots yet · Browse templates" |
| Market overview | `active_symbols` + `ticks` for favourites | "No favourites · Browse markets" |
| Alerts | Local alert engine | "No alerts · Create one from any chart" |
| Recent activity | Merged trade + bot + account event log | "Nothing yet" |

## Behaviour

- Widgets are modular and configurable: show/hide and reorder, persisted per
  user. Layout config is a serialisable array of widget IDs.
- Every widget loads independently with its own skeleton; one failing widget
  shows an inline error and never blanks the page.
- Market overview subscribes only to visible favourites and unsubscribes on
  unmount.

## States

Loading → per-widget skeletons. Disconnected → widgets keep last value with a
`STALE` badge and the status bar turns amber. Unauthenticated → the dashboard is
replaced by the Connect Deriv screen.

## Mobile

Single column, fixed order: account summary, open positions, quick actions, bot
activity, alerts, recent activity. Market overview becomes a horizontal scroller.

## API dependencies

`authorize`, `balance` (subscribe), `portfolio`, `proposal_open_contract`
(subscribe per contract), `profit_table`, `active_symbols`, `ticks` (subscribe
per favourite).

## Security

Balance and account ID are visible; account tokens never are. A "hide balances"
toggle blurs monetary values for screen-sharing.
