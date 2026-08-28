# 03 — Information Architecture

## Rules

1. **Top navigation, never a permanent left sidebar.** Horizontal space belongs
   to charts and tables.
2. **Ten primary items maximum**, with everything else grouped under **More**.
3. **Context travels.** The selected symbol and account mode persist across
   Markets → Analysis → Trade → Digits so the user never re-selects.
4. **One canonical location per concept.** Trade History lives at `/history`;
   Portfolio and Bot Performance link to it with filters rather than
   re-implementing it.
5. **URL is state.** Symbol, timeframe, indicator set, filters, and tab are all
   encoded in the URL so any view is shareable and restorable.

## Navigation levels

- **L0 — Global chrome:** logo, primary nav, account selector, balance,
  connection status, notifications, help, profile. Always present.
- **L1 — Primary nav:** Dashboard, Markets, Analysis, Trade, Digits, AI, Bots,
  Builder, Portfolio, More.
- **L2 — Module tabs:** in-page tabs (e.g. Bots → My Bots / Running / Templates
  / Archived). Encoded as a route segment or search param.
- **L3 — Panels:** resizable regions within a workspace (chart / indicators /
  ticket / positions).

## Grouping rationale

| Primary | Why it is top-level |
| --- | --- |
| Dashboard | The default landing and status answer |
| Markets | Entry point for discovery; everything starts with a symbol |
| Analysis | Flagship module; highest dwell time |
| Trade | The money action; must never be more than one click away |
| Digits | Specialist differentiator against generic platforms |
| AI | Differentiator; needs discoverability |
| Bots | Monitoring surface; users check it constantly |
| Builder | Separated from Bots because it is a mode, not a list |
| Portfolio | The review leg of the loop |
| More | Everything with low daily frequency |

## Search / command palette as an IA layer

Ctrl/Cmd+K is a first-class navigation route, not a convenience. It indexes
markets, bots, strategies, pages, indicators, tutorials, and verbs
("Trade EUR/USD", "Open bot builder"). This is what allows the primary nav to
stay at ten items.

## Content hierarchy per screen

Each screen declares, in this order: (1) where am I + what mode, (2) the primary
data object, (3) the primary action, (4) supporting analytics, (5) history.

## Empty / partial states

Every list has a designed empty state with a single suggested next action.
Unbuilt features render `NOT BUILT — Phase N` with a short description of what
will land there. No greyed-out buttons that silently do nothing.
