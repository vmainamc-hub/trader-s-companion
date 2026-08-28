# 08 — Analysis Centre Specification

**Route.** `/analysis/$symbol` (state in search params: `tf`, `type`,
`indicators`, `drawings`)

Flagship module. This is where users spend the most time.

```
┌ ANALYSIS · R_75 ▾ ──────────────────────────────────────────────────────┐
│ 1m 5m 15m 30m 1h 4h 1d │ Candle ▾ │ + Indicator │ ✎ Draw │ ⤢ │ [TRADE]   │
├──────────────────────────────────────────────┬──────────────────────────┤
│                                              │ TECHNICAL SUMMARY        │
│              PRICE PANE                      │ Trend      BULLISH       │
│         candles + EMA(20) + BB(20,2)         │ Momentum   MODERATE      │
│                                              │ Volatility HIGH          │
│                                              │ ─────────────────────────│
├──────────────────────────────────────────────┤ KEY LEVELS               │
│  RSI(14)                             62.4    │ R2 1252.10               │
├──────────────────────────────────────────────┤ R1 1244.80               │
│  MACD(12,26,9)                       +1.24   │ S1 1228.40               │
├──────────────────────────────────────────────┤ ─────────────────────────│
│                                              │ INDICATOR READINGS       │
│                                              │ RSI 62.4    neutral-bull │
│                                              │ MACD +1.24  bullish      │
│                                              │ EMA20>EMA50 bullish      │
│                                              │ ─────────────────────────│
│                                              │ CONDITIONS DETECTED      │
│                                              │ · EMA golden cross (4 bars)│
│                                              │ · Price above upper BB    │
│                                              │ ─────────────────────────│
│                                              │ [OPEN AI ANALYSIS]        │
│                                              │ [CREATE ALERT]            │
└──────────────────────────────────────────────┴──────────────────────────┘
```

## Chart

Requirements: candle / line / area, multiple timeframes, zoom, pan, crosshair
with synced OHLC readout, log/linear, autoscale, stacked sub-panes for
oscillators, drawing tools (trendline, horizontal level, ray, rectangle,
Fibonacci retracement), and 60fps updates under live ticks.

Data: `ticks_history` with `style: candles` and a `granularity` matching the
timeframe for history; `ticks`/`candles` subscription for the live bar. The last
bar is mutated in place, never re-fetched.

## Indicator system

Each indicator is a pure module:

```ts
interface Indicator<P> {
  id: string; name: string; group: 'trend'|'momentum'|'volatility'|'volume'|'level';
  defaults: P;
  inputs: InputSpec[];          // renders the settings form
  pane: 'price' | 'separate';
  compute(candles: Candle[], params: P): Series[];
  interpret(series: Series[], candles: Candle[]): Reading; // for the summary panel
  requires: ('open'|'high'|'low'|'close'|'volume')[];
}
```

v1 library: SMA, EMA, WMA, RSI, MACD, Bollinger Bands, Stochastic, ATR, ADX,
CCI, Williams %R, Momentum, ROC, Pivot Points, Support/Resistance detection.
**VWAP is included only where volume is available** — for synthetic indices the
API supplies no volume, so VWAP is hidden rather than computed from a fake
input. Same rule for any volume-dependent indicator.

Computation runs in a Web Worker over the candle array and is memoised on
`(symbol, timeframe, indicatorId, params, lastCandleTime)`.

## Analysis panel semantics

- **Trend** from EMA alignment + ADX: `BULLISH` / `BEARISH` / `NEUTRAL`.
- **Momentum** from RSI slope + MACD histogram: `STRONG` / `MODERATE` / `WEAK`.
- **Volatility** from ATR percentile over the lookback: `HIGH` / `MEDIUM` / `LOW`.
- **Levels** from swing pivots clustered by proximity.
- Each row is hoverable to reveal the exact inputs and formula used.

Language is constrained per `01-product-vision.md`. Nothing in this panel is a
prediction; every string is an observation of current data.

## States

Loading history → skeleton chart with the timeframe bar live. Insufficient
history for an indicator → the indicator renders `n/a — needs 26 bars, have 14`.
Disconnected → chart freezes with a `LIVE DATA PAUSED` ribbon.

## Mobile

Chart-only by default; the technical summary is a bottom sheet; indicators are
added through a full-screen picker; drawing tools are reduced to horizontal
level and trendline.
