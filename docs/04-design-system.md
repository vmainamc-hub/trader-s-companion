# 04 — Design System

## Design intent

PROFESSIONAL · FAST · PRECISE · TECHNICAL · TRUSTWORTHY · MODERN.

Reference feel: an instrument panel, not a marketing site. Dark-first, because
traders stare at charts for hours and dark reduces glare against candle colour.

Explicitly rejected: purple/indigo gradients on white, oversized rounded cards,
decorative hero sections inside the workspace, Inter/Poppins defaults, emoji
iconography, glassmorphism.

## Colour

All colours are `oklch` tokens in `src/styles.css`. Components never use
literal colour utilities (`text-white`, `bg-[#...]`).

**Surfaces (dark, primary theme)**

| Token | Role | Value |
| --- | --- | --- |
| `--background` | app canvas | `oklch(0.17 0.012 250)` |
| `--surface-1` | panel | `oklch(0.21 0.013 250)` |
| `--surface-2` | raised panel / header | `oklch(0.25 0.014 250)` |
| `--surface-3` | hover / active row | `oklch(0.29 0.015 250)` |
| `--foreground` | primary text | `oklch(0.96 0.005 250)` |
| `--muted-foreground` | secondary text | `oklch(0.70 0.012 250)` |
| `--border` | hairlines | `oklch(1 0 0 / 10%)` |

**Brand**

| Token | Role | Value |
| --- | --- | --- |
| `--primary` | PrecisionEdge accent (cyan-teal, deliberately not Deriv red) | `oklch(0.74 0.14 195)` |
| `--primary-foreground` | on-accent text | `oklch(0.17 0.02 200)` |

**Semantic trading colours** — never reuse for non-trading meaning.

| Token | Role | Value |
| --- | --- | --- |
| `--bull` | up / profit / buy | `oklch(0.75 0.17 155)` |
| `--bear` | down / loss / sell | `oklch(0.66 0.20 25)` |
| `--flat` | unchanged | `--muted-foreground` |
| `--warning` | risk approaching limit | `oklch(0.80 0.15 85)` |
| `--info` | neutral system notice | `oklch(0.72 0.11 240)` |
| `--demo` | demo-account chrome | `oklch(0.72 0.13 240)` |
| `--real` | real-account chrome | `oklch(0.72 0.16 55)` |

Colour is never the sole carrier of meaning: profit shows a sign and an arrow as
well as a hue (colour-blind safety).

## Typography

| Role | Family | Notes |
| --- | --- | --- |
| UI / headings | **Archivo** | tight, technical grotesque; not Inter |
| Body / labels | **IBM Plex Sans** | excellent at small sizes |
| Numerics / code / prices | **IBM Plex Mono** | tabular figures, mandatory for any number in a table or ticker |

Loaded via `<link>` in `src/routes/__root.tsx` — never `@import` in
`styles.css` (Tailwind v4 Lightning CSS resolves imports from the filesystem).

**Scale** (rem): 0.6875 micro · 0.75 caption · 0.8125 body-sm · 0.875 body ·
1 body-lg · 1.125 h4 · 1.375 h3 · 1.75 h2 · 2.25 h1. Workspace screens rarely
exceed h3.

All price/P&L rendering uses `font-variant-numeric: tabular-nums`.

## Spacing & layout

4px base. Scale: 2, 4, 6, 8, 12, 16, 20, 24, 32, 40, 48.
Panel padding 12–16px, not 24–32px. Table row height 32px (compact) / 40px
(comfortable), user-selectable. Global content max-width: none — workspaces are
full-bleed; only marketing/education pages cap at 72ch.

## Radius & elevation

`--radius: 4px`. Cards 6px, buttons 4px, inputs 4px, modals 8px. No pill
buttons except toggles. Elevation is expressed by surface step and a 1px border,
not by large shadows. Only overlays (modal, popover, dropdown, toast) get a real
shadow: `0 8px 24px oklch(0 0 0 / 45%)`.

## Component inventory

Buttons (`primary`, `secondary`, `ghost`, `danger`, `buy`, `sell`, sizes
`xs/sm/md`), inputs, numeric stepper (stake/duration), selects, combobox,
segmented control, tabs (underline, not pill), dropdown menus, modals, drawers
(mobile), tooltips, toasts (sonner), notification list items, data tables
(sticky header, virtualised, sortable, resizable columns), chart containers,
badges/status pills, metric tiles, skeletons, empty states, error states.

## States

| State | Treatment |
| --- | --- |
| Loading | Skeleton matching final layout; never a centred spinner in a data panel |
| Empty | Icon + one line + one primary action |
| Error | Plain-language sentence + retry + "details" disclosure with the technical code |
| Success | Inline confirmation; toast only for background completions |
| Warning | Amber border-left on the affected control |
| Not built | Dashed border panel, `PHASE N` badge, one-line description |
| Disconnected | Panel dims to 60% and shows a "waiting for market data" strip; stale numbers get a stale badge rather than disappearing |

## Trading-specific states

Contract lifecycle pill: `PROPOSAL` → `SUBMITTED` → `OPEN` → `WON` / `LOST` /
`SOLD` / `CANCELLED`. Bot status pill: `IDLE` `VALIDATING` `RUNNING` `PAUSED`
`STOPPED` `ERROR` `LIMIT REACHED`.

## Motion

Purposeful only. 120ms for state changes, 180ms for overlays, ease-out. Price
flashes are a 400ms background tint on the changed cell. No page transitions, no
parallax, no entrance animations on data.

## Accessibility

WCAG AA contrast on all text; focus rings visible on every interactive element;
full keyboard operation of the trade ticket and bot builder; `prefers-reduced-
motion` disables flashes and animations; every icon-only control has an
`aria-label`.
