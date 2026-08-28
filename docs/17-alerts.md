# 17 — Alerts and Notifications Specification

**Routes.** `/alerts` · notification centre in the top-right cluster

Alerts close the loop back to ANALYSE: they bring the user back when something
they defined as important happens. They are user-defined only. The product
never sends an unsolicited trading suggestion.

```
┌ ALERTS ── [Active 6] [Triggered] [Muted] ─────────────── [+ New alert] ──┐
│ ┌ R_75 crosses above 1240.00 ─────────────── price · once · active ─────┐│
│ │ created 08:12 · not yet triggered · notify: in-app, browser           ││
│ │ [Edit] [Mute] [Delete]                                                ││
│ └───────────────────────────────────────────────────────────────────────┘│
│ ┌ R_75 RSI(14) crosses below 30 ───────────── indicator · repeat 15m ───┐│
│ │ last triggered 09:02 · 3 times today                                  ││
│ └───────────────────────────────────────────────────────────────────────┘│
│ ┌ Bot "Digit Over 4" stops for any reason ── bot · always ──────────────┐│
│ └───────────────────────────────────────────────────────────────────────┘│
└──────────────────────────────────────────────────────────────────────────┘
```

## Alert types

- **Price** — crosses above/below, enters/leaves a range, moves by a percentage
  within a window.
- **Indicator** — any indicator from the shared library crossing a level or
  another series, evaluated on closed candles to avoid repainting.
- **Digit** — a digit's frequency deviating beyond N standard deviations over a
  stated sample, or a streak of length N. Worded as observation, never as an
  opportunity.
- **Bot** — started, stopped, limit breached, error, consecutive losses ≥ N.
- **Risk** — account drawdown beyond a threshold, exposure beyond a threshold,
  balance below a floor.
- **Contract** — an open contract's indicative P/L crossing a level, expiry
  approaching.

## Evaluation

Alerts are evaluated in the same tick pipeline that feeds charts, outside React,
against the throttled derived snapshot. An alert on a symbol the user is not
viewing keeps a lightweight subscription open through the same reference-counted
registry, and the alerts page states which symbols are being watched and the
cost in open subscriptions. There is a cap on concurrent watched symbols, shown
before it is hit rather than after.

Each alert has a trigger mode: once (auto-mutes after firing), repeat with a
cooldown, or always. Hysteresis is applied to crossing conditions so a value
oscillating around the threshold does not fire repeatedly.

## Delivery

In-app toast plus a persistent entry in the notification centre; optional
browser notification requested only at the moment a user enables it for a
specific alert, never on page load. The notification centre groups by day,
marks read state, and links each entry to the screen that explains it — the
chart at the moment of the cross, the bot log at the stop, the contract detail.

## Persistence

Alerts survive reload and are scoped per account. While the app is closed
nothing is evaluated, and the alert list says so explicitly rather than
implying server-side monitoring the product does not have. If server-side
evaluation is added later it will be an opt-in, clearly-labelled capability
(see doc 27).

## States

None defined → an empty state describing what alerts can watch, with one-click
creation from the current chart. Symbol cap reached → creation is blocked with
the cap and the list of watched symbols. Disconnected → alerts are suspended,
the page shows a suspended banner, and nothing fires retroactively on reconnect;
a summary of the gap is shown instead.

## Mobile

List of cards, creation as a full-screen sheet driven by the same schema, and
the notification centre as a full-screen panel from the bell.
