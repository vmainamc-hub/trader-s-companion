# 14 — Backtesting Specification

**Route.** `/backtest` · `/backtest/$botId`

Backtesting exists to make the IMPROVE step honest. Its second job is to be
explicit about its own limits: a backtest on synthetic indices is a simulation
against recorded ticks with assumed fills, and the product says so on every
result screen.

```
┌ BACKTEST · Digit Over 4 v3 ──────────────────────────────────────────────┐
│ symbol [R_75▾] period [last 10 000 ticks ▾] stake [1.00] [Run backtest]  │
├ RESULT ─ 10 000 ticks · 2026-08-27 11:04 → 2026-08-28 06:58 ────────────┤
│ trades 412   won 219 (53.2%)   lost 193   net +18.60 USD   return 4.5%   │
│ max drawdown -9.80 (5.2%)   longest losing streak 7   avg trade +0.045   │
│ profit factor 1.09   expectancy per trade +0.045 USD                     │
│ ┌ EQUITY CURVE ──────────────────────────────────────────────────────┐   │
│ │        ╱╲    ╱╲╱╲                                                  │   │
│ │   ╱╲╱╲╱  ╲╱╲╱     ╲╱╲    drawdown band shaded beneath              │   │
│ └────────────────────────────────────────────────────────────────────┘   │
├ TRADE LIST (412) ── filter [all ▾] ─────────────────────────────────────┤
│ #   TIME      ENTRY     TYPE        BARRIER RESULT  P/L    BALANCE       │
│ 1   11:04:31  1231.402  DIGITOVER   4       WON    +0.94   100.94        │
└──────────────────────────────────────────────────────────────────────────┘
  Simulated. Assumes fills at the recorded tick with no slippage or rejection.
```

## Data and engine

History comes from `ticks_history` (ticks or candles, depending on what the
strategy consumes) for the selected window, subject to the API's maximum count
per request; longer windows are assembled by paging backwards on `end` and are
labelled with the exact range actually retrieved. The engine replays the same
executor the live runtime uses — one implementation, two drivers — so a
backtest cannot pass with logic the live bot would not run. The only difference
is the trade adapter: live calls `proposal`/`buy`, the simulator resolves the
contract arithmetically.

Payout modelling uses the contract's documented payout formula where it is
deterministic (digit contracts, rise/fall with fixed payout) and otherwise
requires the user to state an assumed return, which is then printed on the
result. We never silently invent a payout.

Execution runs in a worker so the UI stays responsive, reports progress, and is
cancellable.

## Metrics

Trades, wins, losses, win rate, net P/L, return on starting balance, profit
factor, expectancy per trade, maximum drawdown in currency and percent, longest
winning and losing streaks, average and median trade, time in market, and the
distribution of results by hour of day. Every metric is defined in a glossary
reachable from the panel.

## Comparison

Two or more runs of the same bot across versions or parameters sit side by side
with identical scales. Parameter sweeps are permitted over a bounded grid, and
the result table warns explicitly about overfitting when the best cell's edge
is within the noise band of the sample.

## Separation of statistics (binding rule)

Backtest, demo, and live results are stored in separate namespaces and never
aggregated into one number anywhere in the product. Where they appear together
they are labelled and visually distinct, and the bot performance page shows
three columns rather than one blended figure.

## States

No history for the window → the real available range is offered. Long run →
progress with an estimate and cancel. Zero trades → the validation output
explaining which condition never became true. Comparison with mismatched
symbols or periods → blocked, with the mismatch named.

## Mobile

Summary metrics and equity curve only; the trade list is available but paged,
and the sweep tools are desktop-only and labelled as such.
