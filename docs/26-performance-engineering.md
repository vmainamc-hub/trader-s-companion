# 26 — Performance Engineering

A tick-driven interface fails in two distinct ways: it renders too often, and it
holds on to too much. Both are addressed structurally rather than by scattering
memo calls after the fact.

## Budgets

| Metric | Target | Hard ceiling |
| --- | --- | --- |
| Tick-to-pixel latency | < 120 ms | 250 ms |
| Frame budget while streaming | 16 ms | 33 ms |
| Route JS (initial, gz) | < 180 KB | 250 KB |
| Time to interactive, dashboard | < 2.0 s | 3.5 s |
| Steady-state heap, 1 h session | < 150 MB | 250 MB |
| Concurrent subscriptions | within documented cap | cap |

Budgets are asserted in CI on bundle size and measured manually with the React
profiler and a heap snapshot before each release.

## Rendering

Ticks never drive React directly. The non-React layer of doc 22 coalesces them
into throttled snapshots at a configurable cadence, default 250 ms, and
components subscribe through `useSyncExternalStore` with granular selectors. A
price cell subscribes to a price, not to a buffer. The digit grid subscribes to
a distribution object recomputed incrementally — a new tick updates counts by
±1, it does not re-scan the window.

Charts are the largest cost. They receive already-aggregated series, animate
only on user interaction, cap visible points to the plot width, and update the
last candle in place rather than re-projecting the whole series. Off-screen
charts unsubscribe entirely via an intersection observer.

Lists that can grow without bound — trade history, bot logs, statement — are
virtualised from the first row, not once they become slow.

## Memory

Ring buffers are fixed length per symbol and allocated once. Nothing accumulates
per tick: no growing arrays, no per-tick object retained beyond the buffer, no
closures captured in long-lived listeners. Bot logs are append-only with a
bounded in-memory tail and the remainder in IndexedDB. Backtest tick sets are
loaded, consumed, and released; results are stored, inputs are not.

Every subscription has an owner and a teardown (doc 22); the leak class this
prevents is the dominant one in this kind of application.

## Loading

Routes code-split by default. The bot builder, backtester, and charting library
are lazy — a user who only checks a balance should not download a strategy
editor. Heavy derivations that would block input move to a worker; the boundary
is data-in/data-out so the same pure function is testable synchronously.

Reference data — active symbols, contracts for symbol — is cached with long
staleness because it changes rarely. History requests are paginated and
deduplicated across components by the query key.

## Network

The subscription registry deduplicates streams so N components watching one
symbol produce one subscription. Requests are batched where the API allows,
debounced where user input drives them (proposal on stake change), and cancelled
on navigation. Backoff and cap handling follow doc 24's degradation ladder, so
performance pressure becomes a visible reduced mode rather than a stall.

## Mobile

Lower throttle cadence, fewer visible series, smaller ring buffers, and
`prefers-reduced-motion` honoured. The mobile shell loads its own nav and
avoids the desktop chart bundle unless a chart is opened.

## Measurement

Instrument three things and only three: tick-to-pixel latency, dropped-frame
count while streaming, and subscription count. They are visible in the
connection diagnostics tab of doc 19, which means regressions are noticed by
users of the app, including us, rather than discovered in an audit.
