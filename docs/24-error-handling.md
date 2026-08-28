# 24 — Error Handling Specification

An error surface for a trading client is a product feature, not a fallback. The
governing rule: **never silently swallow, never silently retry a money action,
never invent a value to fill a gap.**

## Taxonomy

| Class | Examples | Recovery |
| --- | --- | --- |
| Connection | socket closed, timeout, degraded | automatic, with visible state |
| Authorisation | InvalidToken, AuthorizationRequired, missing scope | reconnect / re-auth action |
| Rate limit | RateLimit on ticks_history, proposal, buy | backoff, reduce subscriptions, inform |
| Validation | bad stake, unsupported duration, invalid barrier | inline, before submission |
| Trade rejection | ContractCreationFailure, market closed, insufficient balance | explain, keep ticket state |
| Data integrity | unexpected shape, missing field | fail the read, quarantine, report |
| Application | render crash, bad state transition | boundary, report, offer recovery |

Every error crossing the Deriv adapter becomes a typed `DerivApiError` carrying
`code`, the API `message`, the originating request name, and whether it is
retryable. UI code branches on `code`, never on message text.

## Retry policy

Idempotent reads retry with exponential backoff and jitter, capped. Streams are
re-established by the registry replay of doc 22. `buy` and `sell` are **never**
retried automatically: a duplicate purchase is worse than a failed one. On a
`buy` timeout the ticket enters an explicit *unknown* state, the client queries
`portfolio` and `proposal_open_contract` to establish what actually happened,
and tells the user the outcome before allowing another submission.

## Presentation

Choose the smallest surface that can carry the information:

- **Inline field message** — validation the user can fix in place.
- **Panel-level state** — a single card or widget failed; the rest of the page
  stays live, and the card offers retry.
- **Toast** — a transient action outcome; never the only record of a money event.
- **Persistent banner** — a condition affecting everything: disconnected, token
  expiring, loss guard tripped, subscription cap reached.
- **Route error boundary** — the route cannot render.
- **Notification centre entry** — anything a user may want to review later,
  including every trade rejection and every bot stop.

Copy is specific: what happened, what it means for money at risk, and the next
action. "Something went wrong" is not acceptable copy anywhere in this product.
When an open position may be affected, the message says so first.

## Boundaries

A root boundary catches anything unhandled and offers reload and home. Each
route has its own boundary so a failure in one module never blanks the shell.
Widgets that own independent data — a chart, a digit panel, a bot card — have a
local boundary, because one broken derivation should not remove the trade ticket
from the screen. Boundaries report through the shared reporter with redaction
applied (doc 20).

## Empty, loading, and unavailable are distinct

Four different states with four different renderings, never conflated: loading
(skeleton matching final layout), empty (no data exists, with the action that
would create some), unavailable (feature not built or not permitted, stated
plainly), and error (something failed, with retry). Placeholder or fabricated
numbers are prohibited — a missing value renders as `—` with a tooltip saying
why, never as `0`.

## Degradation ladder

1. Full — connected, authorised, all streams within cap.
2. Reduced — cap reached or rate limited; non-essential streams dropped, badge
   shown, watch lists trimmed with an explanation.
3. Read-only — authorised without `trade` scope, or loss guard tripped; trading
   controls disabled with the reason attached to each control.
4. Disconnected — cached values shown with an age stamp and marked stale; no
   trade submission possible.

Bots follow the same ladder: reduced pauses new entries, disconnected stops the
executor and writes the reason to the append-only log.

## Reporting

Errors are reported with class, code, request name, route, connection state, and
a correlation id — never payloads, never tokens, never balances. The user can
see the last error in the connection diagnostics tab, which makes support
conversations concrete instead of speculative.
