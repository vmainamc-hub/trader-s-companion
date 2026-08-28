# 00 — Existing Codebase Audit (Phase 0)

Date of audit: 2026-08-28. Method: full filesystem walk of `src/`, dependency
review of `package.json`, and a case-insensitive content search for
`deriv|websocket|oauth` across `src/` and `public/`.

## Headline finding

**The Deriv integration does not exist in this project.** The brief states that
"Deriv authentication/account connection and core integration already exist".
That is not true of this codebase. The repository is an unmodified TanStack
Start starter template.

This is a material change to Phase 0's premise:

- There is no working functionality to preserve. The instruction "do not destroy
  working functionality" is satisfied trivially.
- Deriv OAuth, the WebSocket layer, account switching, and market data are
  **new build work**, not inherited assets. They move from "audit and respect"
  into the implementation roadmap (Phase 2/2.5).

## Inventory

| Area | Present? | Detail |
| --- | --- | --- |
| Routes | Template only | `src/routes/__root.tsx`, `src/routes/index.tsx` (blank-page placeholder), `src/routes/README.md` |
| Generated route tree | Yes | `src/routeTree.gen.ts` (tool-owned, never hand-edited) |
| Components | Primitives only | `src/components/ui/*` — stock shadcn/ui, unmodified |
| Hooks | One | `src/hooks/use-mobile.tsx` |
| Lib | Template only | `utils.ts`, `error-capture.ts`, `error-page.ts`, `lovable-error-reporting.ts` |
| Deriv OAuth flow | **Absent** | zero matches for `deriv` / `oauth` in source |
| WebSocket architecture | **Absent** | zero matches for `websocket` in source |
| Account switching | **Absent** | — |
| Market-data subscriptions | **Absent** | — |
| Trading functions | **Absent** | — |
| Bot logic | **Absent** | — |
| State management | **Absent** | No store library; TanStack Query is installed but unused |
| API services | **Absent** | No `src/services`, no `*.functions.ts`, no `src/routes/api` |
| Database / storage | **Absent** | No backend enabled, no `src/integrations` |
| Environment variables | **Absent** | No `.env`, no configured secrets |
| Error handling | Template only | Root `errorComponent` + `notFoundComponent` in `__root.tsx`; Lovable error reporting shim |
| Chart implementation | **Absent** | `recharts` installed; `src/components/ui/chart.tsx` wrapper present, unused |
| Tests | **Absent** | No test runner configured, no test files |
| Design system | Template default | `src/styles.css` still holds the stock shadcn oklch slate palette |

## Dependencies (relevant)

React 19.2, TanStack Start 1.168 / Router 1.170, TanStack Query 5.101,
Tailwind CSS 4.2, Radix UI primitives, `recharts` 2.15, `cmdk` (command
palette — useful for Ctrl/Cmd+K), `sonner` (toasts), `zod` 3.24,
`react-hook-form`, `date-fns`, `react-resizable-panels` (useful for the
analysis and bot-builder split layouts), `lucide-react`.

Notably **absent** and required later: a Deriv API client, a charting library
suited to financial candles at tick density, a state store for real-time data,
and a test runner.

## Technical debt list

None inherited. The only pre-existing item is cosmetic:

1. `src/routes/index.tsx` still renders the template placeholder and must be
   replaced by the real product entry point in Phase 2/3.
2. `src/styles.css` carries the default palette; it is replaced wholesale by the
   PrecisionEdge design system (see `04-design-system.md`).
3. Root `head()` still advertises "Lovable App" / "Lovable Generated Project".

## Risk list (forward-looking)

| # | Risk | Impact | Mitigation |
| --- | --- | --- | --- |
| R1 | Deriv OAuth must be built from scratch, including token custody | High | Treat as its own phase with a written security spec before code (`20-security.md`) |
| R2 | Real-money trading against a live account | Severe | Demo-first default, explicit confirmation gates, `39` account-safety rules |
| R3 | WebSocket subscription leaks under heavy tick load | High | Single managed connection + refcounted registry (`22-websocket-architecture.md`) |
| R4 | Tick throughput re-rendering the React tree | High | Out-of-React tick pipeline with throttled snapshots (`25-performance.md`) |
| R5 | Deriv API rate limits / connection caps | Medium | Central adapter is the only caller; queue + backoff |
| R6 | Bot execution running client-side dies when the tab closes | High | Explicit product statement; cloud execution is a future roadmap item |
| R7 | Backtest results mistaken for live expectations | Medium | Hard separation of backtest/demo/live stats; mandatory disclaimers |
| R8 | Scope. The brief describes a multi-quarter product | High | Phased roadmap; each phase independently shippable |
| R9 | Trade-dress infringement on Deriv | Legal | Original design language; `docs/research/02-competitor-analysis.md` records what must not be copied |
| R10 | AI output being read as financial advice | Legal/ethical | Constrained vocabulary, evidence-linked output, persistent disclaimers |
