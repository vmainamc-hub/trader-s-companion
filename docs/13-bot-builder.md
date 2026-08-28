# 13 — Bot Builder Specification

**Route.** `/builder` · `/builder/$id`

A visual strategy editor. The design target is that a user who cannot program
can express a complete, safe strategy, and a user who can program is not
insulted by it.

```
┌ BUILDER · Digit Over 4 v3 ── draft ── [Validate] [Backtest] [Save] ──────┐
│ ┌ TOOLBOX ──────┐ ┌ CANVAS ─────────────────────┐ ┌ INSPECTOR ─────────┐│
│ │ ▸ Market      │ │  ┌─ ON TICK ─────────┐      │ │ BLOCK: Buy contract││
│ │   tick, digit │ │  │ symbol R_75       │      │ │ type   DIGITOVER ▾ ││
│ │ ▸ Indicators  │ │  └────────┬──────────┘      │ │ barrier 4          ││
│ │   RSI, MA, BB │ │  ┌────────▼──────────┐      │ │ duration 1 tick    ││
│ │ ▸ Logic       │ │  │ IF digit > 4      │      │ │ stake  = base      ││
│ │   if, and, or │ │  │ AND streak ≥ 2    │      │ ├ RISK (bot-level) ──┤│
│ │ ▸ Trade       │ │  └────────┬──────────┘      │ │ max stake     5.00 ││
│ │   buy, sell   │ │  ┌────────▼──────────┐      │ │ stop-loss    20.00 ││
│ │ ▸ Risk        │ │  │ BUY DIGITOVER 4   │      │ │ take-profit  40.00 ││
│ │ ▸ Variables   │ │  └───────────────────┘      │ │ max trades      100││
│ └───────────────┘ └─────────────────────────────┘ └────────────────────┘│
├ VALIDATION ──────────────────────────────────────────────────────────────┤
│ ✕ No exit path when the condition is false — bot would idle indefinitely │
│ ⚠ Stake is fixed; no recovery rule (this is fine, stating it explicitly) │
└──────────────────────────────────────────────────────────────────────────┘
```

## Block model

Blocks are typed nodes with typed ports; the canvas refuses a connection whose
types disagree and explains why at the moment of the attempted drop. Categories:

- **Market** — on tick, on candle close, last digit, price, spread, session.
- **Indicators** — the same library the analysis workspace uses, identical
  implementations, so a chart reading and a bot reading can never diverge.
- **Logic** — if/else, and/or/not, comparison, wait N ticks, cooldown.
- **Trade** — buy contract (type, duration, barrier, stake expression), sell,
  wait for settlement.
- **Risk** — stop-loss, take-profit, max consecutive losses, max daily loss,
  stake rule (fixed, percentage of balance, step recovery).
- **Variables** — user-named numbers and counters with explicit initial values.

The graph serialises to a versioned JSON schema validated with zod. Import and
export use that schema; there is no proprietary binary format, and a bot the
user built is a file they own.

## Validation

Runs on every change, non-blocking, and again as a gate before save-as-runnable.
Errors block; warnings inform. The rule set includes: unreachable blocks,
missing entry point, a buy with no settlement handling, an unbounded loop, a
stake expression that can grow without limit, missing risk envelope, references
to an undefined variable, and a contract type not offered by `contracts_for`
for the chosen symbol.

## Editing affordances

Undo/redo with a visible history, multi-select, copy/paste of subgraphs,
alignment and auto-layout, zoom to fit, and a minimap once the graph exceeds
the viewport. Keyboard: `Space` pan, `Del` delete, `Cmd/Ctrl+D` duplicate,
`Cmd/Ctrl+Z` undo, `/` to search the toolbox and place a block at the cursor.

## Handoff

From the builder a user goes to Backtest (doc 14) with one action; results
return as a badge on the version. Save creates an immutable version; running
happens from Bots, never directly from the canvas, so starting a bot is always
a deliberate second step on a different screen.

## States

Empty canvas → a prompt with three starting points (blank, template, from the
strategy assistant). Invalid → the failing blocks are outlined and the
validation panel is opened. Unsaved changes → the tab and the save control both
mark the state, and navigation warns.

## Mobile

The builder is not usable at phone width and says so plainly, offering
read-only inspection of a bot's logic as a structured outline instead of the
canvas. This is stated in the navigation so the user is not led into a dead
end.
