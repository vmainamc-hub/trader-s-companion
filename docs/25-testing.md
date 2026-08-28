# 25 — Testing Strategy

Testing effort is allocated by consequence. Code that can lose a user money is
tested exhaustively; code that arranges pixels is tested lightly.

```
 Tier 1  money-critical   exhaustive unit + property tests, no exceptions
 Tier 2  data correctness unit tests with fixtures from real API shapes
 Tier 3  integration      fake socket driving real stores and hooks
 Tier 4  UI flows         a small number of end-to-end journeys
 Tier 5  presentation     smoke render + accessibility checks
```

## Tier 1 — money-critical

Risk envelope evaluation, daily loss guard, duplicate-buy protection, bot
executor state machine, position sizing, stake and payout arithmetic, demo/real
mode derivation, and the account-switch invalidation path.

These get table-driven unit tests covering every branch, plus property tests for
the arithmetic: a stake can never be negative, a loss guard can never permit a
submission after tripping, an executor can never enter `running` without a valid
envelope, and two identical buy intents within the debounce window can never
produce two contracts. Any bug found here is closed with a regression test in
the same change.

## Tier 2 — data correctness

Digit distribution and streak derivations, candle aggregation from ticks,
indicator maths, backtest metric calculation, performance breakdowns, and every
zod schema with its migrations.

Fixtures are captured from real Deriv responses and committed, so the tests
encode the actual field names and edge cases — empty history, a single tick, a
gap, a symbol with no contracts. Metric functions are checked against
hand-computed expected values, not against their own output.

## Tier 3 — integration

The WebSocket manager is tested through an injected fake socket that can be
driven message by message: authorise, subscribe, receive, drop, reconnect,
replay, hit the cap, rate-limit. Assertions cover reference counting to zero
producing exactly one `forget`, replay after reconnect issuing fresh
subscription ids, queued requests releasing only after authorisation, and a
cancelled request never resolving.

Store and hook integration runs in the same harness: a component subscribes,
unmounts, and the registry entry must be gone.

## Tier 4 — end-to-end

A deliberately small set, run against the fake adapter rather than live Deriv:

1. Connect → choose demo account → land on dashboard.
2. Open trader → build a ticket → see the proposal → buy → position appears →
   position settles into history.
3. Attempt a real-money buy → confirmation required → cancel leaves no contract.
4. Create a bot → validation blocks an invalid block graph → backtest runs →
   arming for real is refused before a demo run exists.
5. Trip the daily loss guard → trading controls disable with the stated reason.
6. Lose the connection mid-session → banner appears → reconnect → streams resume
   and the chart marks the gap.

## Tier 5 — presentation

Every route renders without crashing in loading, empty, unavailable, and error
states. Accessibility checks assert keyboard reachability of all trade controls,
labelled form fields, focus-visible styling, and that real-account distinction
is not conveyed by colour alone.

## What is not tested

Exact pixel output, chart rendering internals, and third-party library
behaviour. Snapshot tests of large DOM trees are avoided; they fail on every
legitimate change and teach the team to ignore failures.

## Discipline

No live-account trade is ever executed by a test. Tests never use real tokens;
the fake adapter is the only transport in CI. A failing Tier 1 test blocks
release outright. Coverage is reported but is not the target — untested branches
in Tier 1 are the target, and they are reviewed by name.
