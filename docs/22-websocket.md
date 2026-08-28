# 22 — WebSocket Layer Specification

One connection. One registry. One owner. Every stream in the application is
reference-counted and every subscription has a guaranteed teardown path.

```
┌ CONNECTION MANAGER ──────────────────────────────────────────────────────┐
│  state  connecting → open → authorised → degraded → closing → closed     │
│  heartbeat  ping every 30s · expect pong within 10s · else force reopen  │
│  backoff    1s 2s 4s 8s 15s 30s 30s… ±20% jitter · reset on authorised   │
├ REQUEST ROUTER ──────────────────────────────────────────────────────────┤
│  req_id → pending promise · timeout 20s · error → typed DerivApiError    │
├ SUBSCRIPTION REGISTRY ───────────────────────────────────────────────────┤
│  key "ticks:R_100" → { subscription_id, refCount, listeners[], lastMsg } │
│  refCount 0 → forget(subscription_id) → delete entry                     │
└──────────────────────────────────────────────────────────────────────────┘
```

## Lifecycle

The manager is created once, outside React, and exposed through a context that
provides functions rather than raw state. It opens lazily on first use, not at
module load, so a user who never reaches a data surface never opens a socket.

On open it sends `authorize` with the active account's token before releasing
any queued request. Requests issued while unauthorised are queued, not failed;
requests issued while the socket is closed are queued up to a bounded length and
then rejected with a typed error so callers can render an honest failure.

`degraded` is a real state, not a synonym for closed: the socket is open but the
last N requests timed out or the subscription cap was hit. The UI shows it in
the status indicator and in the connection diagnostics tab of doc 19.

## Reconnection

Backoff is exponential with jitter and a 30-second ceiling. Reconnection is not
silent recovery — after re-authorising, the manager **replays the registry**:
every entry with `refCount > 0` is resubscribed and issued a fresh
`subscription_id`, and the old id is discarded rather than reused. Listeners are
notified of a `resumed` event so charts can mark the gap instead of drawing a
straight line across missing ticks. Running bots receive the same event and
follow the pause rules of doc 12; they never assume continuity across a gap.

Manual reconnect from settings cancels any pending backoff timer and attempts
immediately.

## Subscription registry

The key is `type:params` in a canonical, sorted form so two callers asking for
the same stream share it. Subscribing increments the reference count and returns
an unsubscribe function; React callers get a hook that calls it in cleanup, so
an unmounted component can never leak a stream. When the count reaches zero the
manager sends `forget` with the stored subscription id and removes the entry.

The registry enforces the documented concurrent-stream cap. When a new
subscription would exceed it, the manager evicts the least-recently-used entry
that is not pinned — the active symbol, open positions, and running bots are
pinned and never evicted. Eviction is reported, not hidden: doc 17's alert
system reduces its watch list and says so.

## Message routing

Responses carrying `req_id` resolve the matching pending promise. Responses
carrying `subscription.id` are dispatched to the registry entry's listeners.
Anything with an `error` field becomes a typed error at the boundary; the layer
above never inspects raw payloads. Unknown message types are counted and dropped
rather than thrown, so a Deriv-side addition cannot crash the client.

## Tick processing outside React

Ticks arrive faster than a reasonable render cadence. The manager writes into a
per-symbol ring buffer of fixed length and emits a throttled snapshot — default
every 250 ms, configurable, and coalescing multiple ticks into one update.
Components subscribe to snapshots, never to raw messages. Digit statistics,
streak counters, and analysis derivations are computed in the same non-React
layer from the ring buffer, so a chart re-render never recomputes them.

## Timeouts and cancellation

Every request has a 20-second timeout. Requests are cancellable via
`AbortSignal`; a cancelled request removes its pending entry so a late response
is discarded rather than resolving something the user has navigated away from.

## Test seams

The manager takes a socket factory, so tests inject a fake socket and drive
open, message, error, and close deterministically. The registry, backoff, and
throttle are pure and unit-tested independently of the transport (doc 25).

## What this layer must never do

Format currency, know about routes, hold React state, retry a `buy`
automatically, or log a payload without redaction.
