# 01 — Product Vision

## One sentence

PrecisionEdge is a professional trading workspace for Deriv traders that unifies
analysis, manual execution, automation, and review into one coherent loop.

## The loop

```
ANALYSE → DECIDE → TRADE → AUTOMATE → MONITOR → REVIEW → IMPROVE
             ↑                                              │
             └──────────────────────────────────────────────┘
```

Every module exists to serve one leg of this loop, and every module must offer a
one-click path to the next leg. A market you are analysing can be traded without
re-selecting it. A trade you placed appears in review with the analysis context
that produced it. A pattern you find in review can be turned into a bot.

## What it is

A trading operating system: workstation + analysis platform + automation studio
+ AI research assistant.

## What it is not

- Not another Deriv website. Not a Deriv clone in look, name, or layout.
- Not a signal-selling product. It never promises profit.
- Not a black box. Every number shown must be traceable to data or a documented
  calculation.

## Design principles

1. **Truth over comfort.** If the connection is dead, say so. If a feature is
   unbuilt, say so. Never render a plausible-looking fake.
2. **Density with hierarchy.** Traders scan. Prefer tables, tight spacing, and
   numeric alignment over decorative cards.
3. **The chart is the hero.** Chrome shrinks; data grows.
4. **Safety is a feature.** Real-money actions are deliberate, never accidental.
5. **Explain the machine.** Indicators, AI assessments, and bot decisions all
   show their working.
6. **Fast by construction.** Real-time data is processed outside the render
   tree; heavy modules are lazy-loaded.

## Language rules (binding)

Permitted: "technical condition", "model assessment", "signal", "probability",
"market observation", "historical result".

Forbidden anywhere in UI, docs, or marketing: "guaranteed", "risk-free",
"100% accurate", "certain prediction", "guaranteed signals", "can't lose".

Every backtest surface carries: *historical performance does not guarantee
future results*. Every AI surface carries: *model assessment, not financial
advice*.

## Target users

- **Beginner** — new to Deriv, needs guardrails, education, and demo-first.
- **Manual trader** — lives in chart + ticket + positions; wants speed.
- **Digits specialist** — needs tick-level statistics no generic platform gives.
- **Bot trader** — builds, validates, backtests, deploys, monitors.
- **Advanced/quant** — combines AI research with custom strategies and
  comparative performance analytics.

## Success criteria

A user entering PrecisionEdge can answer, without asking anyone: where am I,
what is the market doing, what can I analyse, what can I trade, what are my bots
doing, how am I performing, and what should I look at next.
