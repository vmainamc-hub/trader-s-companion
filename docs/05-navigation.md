# 05 — Navigation Architecture

## Desktop shell

```
┌──────────────────────────────────────────────────────────────────────────────┐
│ ◆ PRECISIONEDGE   Dashboard Markets Analysis Trade Digits AI Bots Builder    │
│                   Portfolio  More ▾        [DEMO ▾] 10,000.00 USD  ⌘K ● ⚑ ? ◉│
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  PAGE CONTENT (full-bleed workspace)                                         │
│                                                                              │
├──────────────────────────────────────────────────────────────────────────────┤
│ ● Connected · ticks 3 subs · latency 41ms          Markets open · 09:41 UTC  │
└──────────────────────────────────────────────────────────────────────────────┘
```

Header height 52px. Status bar 24px, always visible, never decorative.

## Top-right cluster (order is fixed)

1. **Account selector** — `DEMO` / `REAL` pill, coloured by `--demo` / `--real`,
   with account ID and currency in the dropdown.
2. **Balance** — monospace, live.
3. **Command palette** — ⌘K.
4. **Connection status** — dot + label; click opens the connection panel.
5. **Notifications** — bell with unread count.
6. **Help** — contextual help for the current screen.
7. **Profile** — avatar menu: Profile, Settings, Security, Sessions, Sign out.

## More menu (grouped)

```
TRADING            ANALYSIS            ACCOUNT
Trade History      Strategies          Settings
Performance        Watchlists          API / Connection
                   Alerts              Help
LEARN                                  About
Tutorials
```

## Mobile

Bottom navigation, five slots: **Home · Markets · Trade · Bots · More**.
The header collapses to logo + account pill + balance + bell. Everything else
moves into a full-screen More sheet. Mobile is redesigned, not shrunk:
- Trade uses a full-screen ticket with a sticky confirm bar.
- Analysis defaults to chart-only with indicators in a bottom sheet.
- Bot Builder is read/monitor-first; editing shows a "best on desktop" notice
  with a limited list-based editor.

## Active state and breadcrumbs

Primary nav items use `activeProps` (accent underline + full-opacity text).
Workspaces with a symbol show a symbol chip next to the module name rather than
a breadcrumb trail: `Analysis · R_75 ▾`.

## Keyboard

| Key | Action |
| --- | --- |
| ⌘K / Ctrl+K | Command palette |
| G then D/M/A/T/B | Go to Dashboard/Markets/Analysis/Trade/Bots |
| B / S | Focus buy / sell in the trade ticket |
| Esc | Close overlay, cancel proposal review |
| ⌘⇧X | Emergency stop all bots (with confirm) |
