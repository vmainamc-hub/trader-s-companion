# Trader's Companion

I'll check the current state and the uploaded archive first.

Here's where things stand after resuming.

Restored: your existing `/docs/00-audit.md` through `09-digits.md` from the uploaded archive, plus the approved Phase 0/1 plan into `.lovable/plan/`. No `.git` metadata was copied.

Written this session:

- `10-trader.md` — ticket generated from `contracts_for`, subscribed proposal lifecycle, duplicate-buy protection, positions, history

- `11-ai.md` — analyst / chat / strategy assistant, binding language rules, deterministic locally-computed inputs, server-side model calls

- `12-bots.md` — bot record and lifecycle, mandatory risk envelope, executor state machine, append-only log, demo-first rule

- `13-bot-builder.md` — typed block model, validation rule set, versioned zod schema, editing affordances, mobile read-only stance

- `14-backtesting.md` — one executor two drivers, payout modelling honesty, metrics, comparison, backtest/demo/live separation

- `15-portfolio.md` — sources of truth, bot attribution, indicative equity labelling, contract detail linking result to cause

- `16-performance.md` — three-column discipline, sample-size honesty, breakdowns, per-bot run and version history

- `17-alerts.md` — alert types, tick-pipeline evaluation with subscription cap, hysteresis, delivery, no server-side monitoring claim

- `18-onboarding.md` — welcome → OAuth → account choice → orientation, read-only mode, risk acknowledgement

- `19-account.md` — account switching rules, trading defaults and loss guard, data export/import, connection diagnostics tab

Remaining: docs `20-security`, `21-deriv-api`, `22-websocket`, `23-state`, `24-error-handling`, `25-testing`, `26-performance-engineering`, `27-roadmap`; the `/docs/research/` set; and `/docs/design/` wireframes and journeys.

The two research sub-agents (Deriv API surface, and competitor/UX analysis) both finished, but credits ran out before I could read their output — so `21-deriv-api.md` and the research docs are still blocked on fetching those two results, which is the first thing to do on resume.

This project was built with [Lovable](https://lovable.dev).

## Build with Lovable

Continue developing this project in the [Lovable editor](https://lovable.dev/projects/c590d8e7-4874-4767-9955-ac8d91f4928a).

- **Ship faster**: describe what you want to build and Lovable handles the code.
- **Stay in sync**: every change made in Lovable is committed straight to this repository.
- **Full ownership**: this code is yours. Push to `main` on GitHub and your changes sync back into Lovable, ready for your next prompt.

## Development

Prefer working locally? You need Node.js and npm — [install with nvm](https://github.com/nvm-sh/nvm#installing-and-updating).

```sh
git clone <this-repository-url>
cd <repository-name>
npm i
npm run dev
```
