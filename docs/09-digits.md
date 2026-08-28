# 09 — Digits Specification

**Route.** `/digits/$symbol`

A specialist tool that generic platforms do not offer. This is a differentiator.

```
┌ DIGITS · R_75 ▾ ────────────────── sample: [10 25 50 100 250 500 1000] ──┐
│ LAST DIGIT  ●7   Last 20: 7 3 9 1 0 4 4 8 2 5 6 1 9 9 3 0 7 2 4 7        │
├ DIGIT DISTRIBUTION (last 1000 ticks) ────────────────────────────────────┤
│ 0 ████████████ 10.4%  104   Δ +0.4   streak 0                            │
│ 1 ██████████   9.1%    91   Δ -0.9   streak 0                            │
│ …                                                                        │
│ 9 █████████████ 11.2% 112   Δ +1.2   streak 2                            │
├ OVER / UNDER ──────────┬ EVEN / ODD ─────────┬ MATCHES / DIFFERS ────────┤
│ barrier [5 ▾]          │ EVEN 49.6%  496     │ target [7 ▾]              │
│ OVER  44.1%   441      │ ODD  50.4%  504     │ MATCH  10.2%  102         │
│ UNDER 45.5%   455      │ current streak ODD×3│ DIFFER 89.8%  898         │
├ TICK TAPE ─────────────┴─────────────────────┴───────────────────────────┤
│ TIME      PRICE      DIGIT  CHANGE                     [pause][clear]    │
│ 09:41:02  1236.107   7      ▲ +0.012                                     │
└──────────────────────────────────────────────────────────────────────────┘
  Statistical note: observed frequencies describe past ticks only. Each tick is
  independent; frequency does not predict the next digit.
```

## Data

Last digit is the final digit of the quote rendered at the symbol's configured
decimal precision (from `active_symbols` `pip`). History comes from
`ticks_history` (`count` up to the sample size, `style: ticks`), then the live
`ticks` subscription appends and evicts from a fixed-length ring buffer.

Sample sizes 10/25/50/100/250/500/1000 — a size is disabled if the API returned
fewer ticks than requested, with the actual count shown.

## Computation

A ring buffer plus incremental counters: on each tick, decrement the evicted
digit's count and increment the new one. O(1) per tick, no recomputation. Runs
outside React; the UI subscribes to a snapshot throttled to ~10Hz.

Derived per digit: count, percentage, deviation from the 10% baseline (absolute
and in standard deviations, `σ = √(np(1-p))`), current streak, last-seen index.

## Disclaimers (mandatory, always visible)

Displayed under every distribution: *observed frequencies describe past ticks
only; each tick is independent and frequency does not predict the next digit.*
Deviation is labelled "deviation from baseline", never "due" or "overdue".

## Actions

Each distribution row offers "Trade this" which opens the trade ticket
pre-configured with the matching contract type (DIGITOVER, DIGITEVEN,
DIGITMATCH…) — subject to `contracts_for` confirming availability.

## States

Loading → skeleton bars with the requested count. Fewer ticks than requested →
banner naming the real count. Paused tape → the subscription stays live and the
statistics keep updating; only the visual scroll freezes. Disconnected →
`LIVE TICKS LOST` ribbon and stats freeze with a timestamp.

## Mobile

Distribution first, then a segmented control for Over-Under / Even-Odd /
Matches-Differs, then the tape.
