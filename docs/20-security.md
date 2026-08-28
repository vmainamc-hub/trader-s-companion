# 20 — Security and Privacy Specification

PrecisionEdge holds credentials that can move real money. This document states
what is stored, where, for how long, and what the application refuses to do.

## Threat model

The realistic adversaries are, in order of likelihood: a malicious or
compromised third-party script running in the same origin; a shared or stolen
device; a shoulder-surfer or screen recording; a phishing page imitating our
OAuth entry point; and a network observer. We are not defending against a
compromised Deriv account or a compromised operating system — both are outside
our reach and the documentation says so plainly rather than implying otherwise.

## Token handling

Deriv OAuth returns account tokens in the redirect query string. Rules:

1. The callback route reads the tokens, then immediately replaces the URL with
   `history.replaceState` so tokens never persist in history, referrer headers,
   bookmarks, or the address bar.
2. Tokens are stored in memory for the session and mirrored into a single
   namespaced `localStorage` record keyed by Deriv user id. `sessionStorage` is
   used when the user declines "keep me signed in", so closing the tab clears
   them.
3. Tokens are never logged, never included in error reports, never sent to any
   origin other than Deriv's WebSocket endpoint, and never included in the data
   export of doc 19.
4. Every stored token record carries the account id, currency, account type,
   scope list, and an issued-at timestamp. Records older than the configured
   maximum age are discarded and the user is asked to reconnect.
5. Disconnecting removes every token record, closes the socket, clears
   account-scoped query caches, and stops all running bots first.

Redaction is enforced centrally: a single `redact()` helper strips anything
matching a token shape from any object before it reaches a logger, a toast, a
console statement, or the error reporter. Ad-hoc `console.log` of API payloads
is prohibited in code review for exactly this reason.

## OAuth entry integrity

The connect action builds the Deriv authorisation URL from a compile-time app id
and a redirect URI that must exactly match the registered one. The callback
verifies a `state` value generated before redirect and stored locally, rejecting
any callback that does not carry it — this is the defence against a crafted link
that plants an attacker's tokens in a victim's browser.

## Scopes

Request `read` and `trade`. Never request `payments` or `admin`; the product has
no feature that needs them, and asking for them would be indefensible. The
granted scope list is displayed in settings, and any capability the user lacks
is disabled with the missing scope named as the reason.

## Real-money guardrails

Security here is as much about mistakes as attackers. Real accounts are visually
distinct everywhere, real-money submissions require explicit confirmation, the
daily loss guard blocks further submission once tripped, bots must run in demo
before they can be armed for real, and every bot carries a mandatory risk
envelope. None of these can be disabled from a URL parameter or a query string.

## Client-side execution boundary

AI model calls run server-side so that no model provider key is ever present in
browser code. The browser sends only the deterministic, locally-computed inputs
described in doc 11 — never tokens, never account identifiers beyond what the
feature needs, never balances unless the feature is explicitly about balance.

## Content security

The application ships no inline event handlers, no `eval`, no `dangerouslySet`
HTML from any remote source, and no third-party analytics that can read storage.
External links open with `rel="noopener noreferrer"`. Charts render from data
already in memory; they never fetch remote code.

## Privacy

The only personal data the application handles is what Deriv returns: account
ids, currency, balance, and trade records. It is processed in the browser and
not transmitted to any PrecisionEdge-controlled store, because there is no such
store in this phase. Local data can be exported and deleted by the user. Copy
must never claim we can delete data held by Deriv.

## Incident behaviour

An authorisation failure invalidates the affected token record and surfaces a
reconnect action rather than a silent retry loop. A repeated rate-limit response
degrades subscription count and tells the user, rather than hammering the
endpoint. An unexpected shape from the API is treated as an error, not coerced.

## Review checklist

Before any release: no token in storage outside the namespaced record; no token
in any network request to a non-Deriv origin; `state` verified on callback; scope
list limited to read and trade; redaction applied at every logging site; real
account styling present on every trade-submitting surface; export contains no
credentials.
