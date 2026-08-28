# 27 — Roadmap

Phases are ordered by dependency, not by excitement. Nothing ships with fake
data; a phase is done when its surfaces are honest about what they do and do not
yet have.

## Phase 0 — Audit (done)

Established that the project is an untouched template: no Deriv connectivity, no
routes, no state, no tests. Recorded in doc 00.

## Phase 1 — Blueprint (this phase)

The `/docs` set, research, and design wireframes. No product code.

## Phase 2 — Application shell

Top navigation and grouped More menu, responsive shell, mobile bottom nav,
connection status indicator, account selector, notification centre, command
palette, and the design system implemented in `src/styles.css` per doc 04.
Every route exists and renders an explicit "not yet available" state.

**Done when** navigation matches doc 05 on desktop and mobile, and no screen
shows a number that is not real.

## Phase 3 — Deriv connectivity

OAuth entry and callback per doc 20, the WebSocket manager and subscription
registry per doc 22, the typed adapter per doc 21, account switching, balance,
active symbols, and the connection diagnostics tab.

**Done when** the fake-socket integration suite of doc 25 passes and a real
demo account connects, switches, reconnects, and replays streams.

## Phase 4 — Market data and analysis

Markets list and market detail, charting, tick pipeline and ring buffers, digit
statistics, streaks, and the analysis surfaces of docs 07–09.

## Phase 5 — Manual trading

Trade ticket generated from `contracts_for`, subscribed proposals, duplicate-buy
protection, confirmation policy, positions, settlement, and history (doc 10).
Loss guard and trading defaults from doc 19 land here, not later.

## Phase 6 — Portfolio and performance

Docs 15 and 16: sources of truth, indicative equity labelling, three-column
discipline, sample-size honesty, and per-contract detail linking result to cause.

## Phase 7 — Alerts

Doc 17: alert types, tick-pipeline evaluation inside the subscription cap,
hysteresis, delivery, and the explicit statement that monitoring stops when the
tab does.

## Phase 8 — Bots

Doc 12 then doc 13: bot record and lifecycle, mandatory risk envelope, executor
state machine, append-only log, demo-first rule, then the typed block model and
builder with versioned schema.

## Phase 9 — Backtesting

Doc 14: one executor, two drivers, payout modelling stated honestly, metrics,
comparison, and strict separation of backtest, demo, and live statistics.

## Phase 10 — AI

Doc 11: analyst, chat, and strategy assistant with binding language rules,
deterministic locally-computed inputs, and server-side model calls.

## Phase 11 — Hardening

Doc 25 tiers completed, doc 26 budgets asserted, accessibility pass, doc 20
review checklist executed, and the error-state matrix of doc 24 verified on
every route.

## Deliberately out of scope

Copy trading, social feeds, payments or cashier features, any `admin` or
`payments` scope, signal selling, guaranteed-return language, and any presentation
that implies server-side monitoring or execution while the product is
browser-only. If server-side execution is ever added it becomes its own phase
with its own security document, not an incremental change.

## Sequencing rules

A phase may not begin while an earlier phase has an unimplemented state on a
shipped route. Money-critical code (doc 25, tier 1) is tested in the same phase
that introduces it. Any change to a persisted schema ships with its migration.
