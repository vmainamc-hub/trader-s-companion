# 23 — State Management Specification

State is separated by lifetime and ownership, not by convenience. Four
categories, each with exactly one home.

```
 Server state      TanStack Query        anything Deriv is the source of truth for
 Stream state      non-React stores      ticks, digit stats, throttled snapshots
 Session state     React context         connection, active account, mode
 Local state       persisted stores      settings, bots, alerts, layout, drafts
```

## Server state — TanStack Query

Balance, active symbols, contracts for symbol, portfolio, profit table,
statement, and contract details are queries. Key convention:

```
["deriv", loginid, "portfolio"]
["deriv", loginid, "contracts_for", symbol]
["deriv", loginid, "profit_table", { from, to, limit }]
```

Every account-scoped key includes the `loginid`, so switching account cannot
show the previous account's numbers even for one frame — the cache misses by
construction rather than by an invalidation call that might be forgotten.
Switching additionally cancels in-flight queries and closes account-scoped
subscriptions.

Streamed data is not stored in Query. Where a query and a stream describe the
same thing — portfolio versus `proposal_open_contract` — the stream updates the
query cache through `setQueryData` so there is still a single read path for
components. Staleness is explicit per query: reference data long, balance short,
history medium. No global default that silently refetches on every focus.

## Stream state — outside React

Per-symbol ring buffers, derived digit distributions, streak and volatility
derivations, and the throttled snapshots described in doc 22 live in plain
observable stores. They are subscribed to with `useSyncExternalStore` so React
sees a stable snapshot per render and concurrent rendering cannot tear.

Selectors are memoised and granular: a component that needs the last digit
subscribes to the last digit, not to the buffer. Nothing in this category is
serialised or persisted; it is rebuilt from the socket on reconnect.

## Session state — context

Connection status, active account and its scopes, demo/real mode, and the
notification centre. Small, frequently read, and provided as separate contexts
so a connection status change does not re-render every consumer of the account.
Demo/real mode is derived from the active account, never stored independently —
two sources of truth for that flag is how real money gets traded by accident.

## Local state — persisted

Settings, bot definitions and versions, alert definitions, backtest results,
dashboard layout, and unsent form drafts. Stored under a versioned envelope:

```
{ version: 7, userId: "...", data: { ... } }
```

Each store declares a zod schema and an ordered list of migrations. Loading runs
migrations to the current version, then validates; a validation failure quarantines
the record under a `.corrupt` key and starts from defaults rather than crashing
or half-loading. Bot definitions carry their own schema version (doc 13) in
addition to the store version.

Large collections — backtest tick sets, long histories — go to IndexedDB;
everything else to `localStorage`. Writes are debounced and batched.

## Derived data

Derivations are pure functions in the domain layer, not stored state. Portfolio
totals, performance metrics, and analysis outputs are computed from their
inputs. Memoisation is an optimisation applied where profiling justifies it, and
never a second copy that can drift from the first.

## Rules

- No component reads from the WebSocket manager directly; it reads a hook.
- No store imports a React module; stores are testable without a renderer.
- No `useEffect` fetching where a query or a loader belongs.
- Optimistic updates only where a failure is cheaply reversible — never for
  `buy`. A trade shows as pending until the API confirms it.
- Every persisted shape has a schema; unvalidated JSON never enters a store.
- Account-scoped anything carries the `loginid`.
