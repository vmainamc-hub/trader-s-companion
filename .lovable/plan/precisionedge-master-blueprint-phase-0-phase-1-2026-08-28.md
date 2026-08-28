# PrecisionEdge — Master Blueprint (Phase 0 + Phase 1)

## Audit result (Phase 0, done)

The project is an untouched TanStack Start template. Concretely:

- Routes: only `__root.tsx` and the placeholder `index.tsx`. No app routes.
- Components: only stock shadcn/ui primitives. No product components.
- No Deriv OAuth, no WebSocket layer, no account switching, no market data, no trading logic, no bots, no state stores, no services, no tests, no `docs/`.
- No backend enabled. No environment variables or secrets configured.
- Stack: React 19, TanStack Start/Router, TanStack Query, Tailwind v4, recharts, zod, sonner.

Consequence: the stated premise ("Deriv authentication and core integration already exist") does not hold here. There is no working functionality at risk, and Deriv connectivity must be built rather than preserved. Technical debt list is empty; the risk list is entirely forward-looking.

## What this phase delivers

Documentation only — no product screens, no redesign of anything. Deliverables:

**`/docs/`** — the master blueprint, one file per area, matching the numbered structure requested: product vision, product map, information architecture, navigation, design system, per-module specs (dashboard, markets, analysis, digits, trader, AI, bots, bot builder, backtesting, portfolio, performance, alerts, onboarding, account), then the engineering architecture set (security, Deriv API, WebSocket, state, error handling, testing, performance) and the future roadmap.

**`/docs/research/`** — market research, competitor analysis (Deriv's own products plus third-party Deriv platforms and TradingView/MetaTrader/cTrader/Binance/IBKR), Deriv API capabilities sourced from developers.deriv.com, recurring UX patterns, and product opportunities. Every entry records: does well / does poorly / what we learn / what we improve / what we must not copy.

**`/docs/design/`** — original ASCII/diagram wireframes for dashboard, markets, market detail, analysis, trader, digits, AI analyst, AI chat, bots hub, bot builder, backtester, bot performance, portfolio, trade history, settings, and the three mobile screens. Plus user-journey flows for beginner / manual trader / bot trader / advanced, and layered architecture diagrams.

## Key architectural positions the blueprint will lock in

- Layering: UI → state → domain services (Market, Trading, Account, Portfolio, Bot, Analysis, AI, Alert) → a single Deriv adapter → Deriv. No raw socket calls in components.
- One managed WebSocket connection with a subscription registry, reference counting, heartbeat, backoff reconnect, and guaranteed teardown.
- Tick processing lives outside React; components subscribe to throttled derived snapshots.
- Top navigation, no permanent sidebar; grouped "More" menu; mobile gets its own bottom-nav structure rather than a shrunken desktop.
- Demo/Real mode is a persistent, unmissable global state; real-money actions require explicit confirmation.
- No fake data anywhere: unbuilt features render explicit "not yet available" states.
- Backtest / demo / live statistics are stored and displayed separately, never merged.

## Research method

Live web research against Deriv's official developer documentation and current third-party Deriv platforms, plus the professional trading products listed. Findings are recorded with sources; patterns are separated into "good pattern", "brand-specific implementation", and "PrecisionEdge original".

## After approval

Phase 2 (application shell) is proposed as the next unit of work — top nav, responsive shell, account selector, connection status, notification centre, command palette, and the design system implemented in `src/styles.css`. It is not part of this phase and will be planned separately once you have reviewed the blueprint.
