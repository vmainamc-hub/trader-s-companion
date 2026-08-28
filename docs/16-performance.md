# 16 — Performance Analytics Specification

**Route.** `/performance` (account-wide) · `/bots/$id` (per bot)

Performance is the IMPROVE step made measurable. It reports what happened; it
does not congratulate, gamify, or project forward.

```
┌ PERFORMANCE · live · last 30 days ── [account] [by bot] [by symbol] ─────┐
│ trades 614   win rate 52.6%   net +309.15   expectancy +0.50/trade       │
│ profit factor 1.12   max drawdown -84.20 (2.1%)   avg hold 4.2 ticks     │
│ ┌ EQUITY ────────────────────────┐ ┌ P/L DISTRIBUTION ─────────────────┐│
│ │        cumulative, drawdown    │ │ histogram of per-trade outcomes   ││
│ └────────────────────────────────┘ └───────────────────────────────────┘│
│ ┌ BY HOUR (UTC) ─────────────────┐ ┌ STREAKS ──────────────────────────┐│
│ │ 00 ▁ 04 ▃ 08 ▇ 12 ▅ 16 ▂ 20 ▁ │ │ longest win 9 · longest loss 7    ││
│ └────────────────────────────────┘ └───────────────────────────────────┘│
├ BOT COMPARISON ──────────────────────────────────────────────────────────┤
│ BOT          BACKTEST      DEMO          LIVE                            │
│ Digit Over 4 +18.60 53.2%  +9.10 51.8%   +12.40 52.6%                    │
│ Trend Follow +44.10 57.0%  -3.20 47.1%   not run                         │
└──────────────────────────────────────────────────────────────────────────┘
```

## Three-column discipline

Backtest, demo, and live are always three separate columns. A bot with no live
history shows "not run", never a backtest number promoted into the live slot.
The divergence between columns is itself surfaced: when demo and live win rates
differ by more than the sample's noise band, the row is annotated with that
fact and the sample sizes.

## Statistical honesty

Every rate is shown with its sample size, and rates from samples below a
threshold (default 30 trades) are rendered as "n too small" with the raw counts
instead of a percentage. Where a confidence interval is meaningful it is shown
as a range, not a point. No metric is extrapolated to a period longer than the
data covers, and the phrase "expected monthly return" does not appear.

## Breakdowns

By bot, by symbol, by contract type, by hour of day, by day of week, and by
stake band. Each breakdown is a table plus a chart on a shared scale, sortable,
and each row links to the filtered trade list that produced it.

## Per-bot page — `/bots/$id`

Run history (one row per run with start, stop reason, trades, P/L), the metrics
above scoped to that bot, the version timeline showing which version produced
which results, and the run log. Changing a bot's version starts a new results
series rather than continuing the old one.

## Data

Derived entirely from `profit_table`/`statement` for live and demo, and from
stored simulation results for backtests. Computation runs in a worker for large
ranges and caches by (account, range, filter) with explicit invalidation on new
settlements.

## States

Insufficient data → counts with the "n too small" treatment rather than empty
charts. Range with no trades → stated plainly. Mixed currencies across accounts
→ never summed; the account selector scopes the whole page.

## Mobile

Headline metrics, equity curve, then breakdowns as an accordion of tables. The
three-column comparison becomes three stacked labelled rows per bot so the
separation survives the narrow layout.
