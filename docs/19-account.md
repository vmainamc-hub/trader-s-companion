# 19 — Account and Settings Specification

**Routes.** `/settings` with tabs — `/settings/account`, `/settings/trading`,
`/settings/appearance`, `/settings/notifications`, `/settings/data`,
`/settings/connection`

```
┌ SETTINGS ─ [Account] [Trading] [Appearance] [Notifications] [Data] [Conn]┐
│ ACCOUNT                                                                  │
│  Deriv accounts        ● VRTC1234567 Demo USD    ○ CR1234567 Real USD    │
│  Active account        Demo · switching reloads all account-scoped data  │
│  Scopes granted        read, trade            [Review scopes at Deriv ↗] │
│  Session               connected 08:02 · token expires in 6d             │
│  [Disconnect Deriv]  removes tokens from this device                     │
├ TRADING ─────────────────────────────────────────────────────────────────┤
│  Default stake         10.00 USD      Default duration   5 ticks         │
│  Confirm real trades   ● always  ○ above [50.00]        (demo: never)    │
│  Daily loss guard      [ on ]  stop offering trades after -100.00        │
└──────────────────────────────────────────────────────────────────────────┘
```

## Account tab

Lists every account returned by the OAuth callback and `authorize`, with type,
currency, and balance. Switching account is a first-class, deliberate action:
it re-authorises with that account's token, invalidates all account-scoped
queries, closes account-scoped subscriptions, stops nothing silently — running
bots must be stopped first and the switch says so. Real accounts are visually
distinct here and everywhere (doc 05).

Scopes granted are displayed as returned by the API, with a link to manage them
at Deriv. A missing `trade` scope is reported here and reflected as the reason
trading controls are disabled elsewhere, so the explanation lives in one place.

## Trading tab

Defaults for stake, duration, and contract type per symbol class; real-money
confirmation policy (always, or above a threshold — never "off" for real); a
daily loss guard that blocks new trade submission after a configured loss and
requires a deliberate reset; and default bot risk envelope values applied to
new bots.

## Appearance tab

Theme is dark by default and light is available; density (compact/comfortable);
chart preferences (candle/line/area, grid, crosshair, price scale side); number
formatting; and the ability to reduce motion, honoured together with
`prefers-reduced-motion`.

## Notifications tab

Per-category channels for the alert types in doc 17, quiet hours, browser
permission state with a direct request control, and a test notification.

## Data tab

Export of settings, bots, and alerts as a single JSON document, and import with
schema validation and a diff preview before applying. Clear local caches. Delete
all local application data, stated as local-only — PrecisionEdge cannot delete
anything held by Deriv, and the copy says so.

## Connection tab

Live socket state, endpoint, app id, current subscription count with a
per-symbol breakdown, reconnect count, last error, round-trip latency, and a
manual reconnect. This is a real diagnostic surface, exposed deliberately
because a trading client that hides its connection state is untrustworthy.

## Persistence

Settings are stored locally per Deriv user id, versioned, and migrated on
schema change. Tokens are handled per doc 20 and are never included in exports.

## States

Not connected → the account tab shows the connect affordance and other tabs
remain usable for local preferences. Token expiring within 24h → a banner with
a re-auth action. Switch attempted with bots running → blocked, listing the
bots and offering to stop them.

## Mobile

Tabs become a vertical list of sections; each opens as its own screen with a
back control, keeping the same content and order.
