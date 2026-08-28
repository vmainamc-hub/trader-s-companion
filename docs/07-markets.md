# 07 — Markets Specification

**Routes.** `/markets`, `/markets/watchlists`, `/markets/$symbol`

## Market Explorer

Sourced dynamically from `active_symbols` — the category tree, display names,
pip size, and open/closed state all come from the API. Nothing is hardcoded.

```
┌ MARKETS ─────────────────────────────────────────────────────────────────┐
│ [search…]        All | Synthetics | Forex | Indices | Commodities | Crypto│
│ Filters: ▸ Open only  ▸ Tradable  ▸ Favourites   Sort: Name ▾  [Grid|List]│
├──────────────────────────────────────────────────────────────────────────┤
│ ★ SYMBOL          NAME                 PRICE      CHANGE   STATUS   ACTIONS│
│ ★ R_75            Volatility 75 Index  1236.10    +0.42%   OPEN     A T ⋯ │
│ ☆ R_100           Volatility 100 Index 8712.44    -0.18%   OPEN     A T ⋯ │
│ ☆ frxEURUSD       EUR/USD              1.0913     +0.05%   CLOSED   A T ⋯ │
└──────────────────────────────────────────────────────────────────────────┘
```

A = Analyse, T = Trade, ⋯ = add to watchlist / create alert / open AI analysis.

Table is virtualised. Live prices come from a single batched tick subscription
covering only the rows currently in the viewport plus favourites.

## Watchlists

Multiple named lists (My Synthetics, Forex, Digits, Scalping, Favourites).
Each row: market, price, change, status, quick actions. Drag to reorder,
drag between lists. Stored per user.

## Market Detail — `/markets/$symbol`

```
┌ VOLATILITY 75 INDEX · R_75 · SYNTHETIC ─────────── OPEN ─────────────────┐
│ 1236.10  +5.18 (+0.42%)      24h H 1249.02  L 1210.55                     │
│ [ANALYSE] [TRADE] [ADD TO WATCHLIST] [CREATE ALERT] [AI ANALYSIS]         │
├ CHART ────────────────────────────────────┬ STATISTICS ──────────────────┤
│ 1m 5m 15m 1h 4h 1d | candle line area     │ Pip size      0.01           │
│                                            │ Volatility    HIGH           │
│  [chart]                                   │ Trend (1h)    BULLISH        │
│                                            │ Range 24h     38.47          │
├────────────────────────────────────────────┼ AVAILABLE CONTRACTS ─────────┤
│ TICK TAPE  09:41:02 1236.10 ▲              │ Rise/Fall, Higher/Lower,     │
│            09:41:00 1235.98 ▼              │ Digits, Touch, Multipliers   │
└────────────────────────────────────────────┴──────────────────────────────┘
```

Available contracts come from `contracts_for` for the symbol — the platform
never offers a contract type the API does not support for that market.

## States

Loading: skeleton table / skeleton chart. Empty search: "No markets match" +
clear filters. Market closed: price frozen with a `CLOSED` badge, TRADE disabled
with a tooltip giving the next open time from `trading_times`. Disconnected:
prices dim + `STALE`.

## Mobile

Explorer becomes a list with a sticky search and a category chip row. Market
detail stacks: header, actions, chart, stats, tick tape.
