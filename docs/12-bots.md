# 12 — Bots Specification

**Routes.** `/bots` (my bots) · `/bots/running` · `/bots/templates` ·
`/bots/$id` (performance, see doc 16)

Bots are the AUTOMATE step. The governing principle is transparency: at any
moment the user must be able to answer "what is this bot doing, why did it do
that, and how do I stop it" without reading code.

```
┌ BOTS ─ [My bots] [Running 2] [Templates] [Imported] [Archived] ── [+ New]┐
│ ┌ Digit Over 4 · R_75 ─────────────── RUNNING · demo ─── ●live ────────┐ │
│ │ runtime 01:42:07   trades 38   won 21   lost 17   P/L +12.40 USD      │ │
│ │ stake 1.00 fixed · stop-loss 20 · take-profit 40 · max trades 100     │ │
│ │ last action 09:41:02 · bought DIGITOVER barrier 4 · id 2417…          │ │
│ │ [Pause] [Stop] [Open log] [Performance] [Edit copy]                   │ │
│ └───────────────────────────────────────────────────────────────────────┘ │
│ ┌ Trend Follow v3 · frxEURUSD ────── STOPPED · take-profit reached ─────┐ │
│ │ last run 2h ago · 64 trades · P/L +31.05 · [Start] [Performance]      │ │
│ └───────────────────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────────────────┘
```

## Bot record

A bot is a stored document: name, symbol(s), the block graph (see doc 13), a
risk profile, a version number, and a lifecycle state — `draft`, `validated`,
`backtested`, `running`, `paused`, `stopped`, `archived`. Editing a running bot
is impossible; the UI offers "Edit copy", which forks to a new version. This
guarantees that any recorded result belongs to exactly one immutable version.

## Risk envelope (mandatory, not optional)

No bot may start without: maximum stake per trade, maximum concurrent
contracts, stop-loss, take-profit, and a maximum trade count or run duration.
The runtime enforces these itself rather than relying on the graph, so a
malformed strategy cannot escape them. Breaching a limit stops the bot,
records the reason, and raises an alert. Stake-growth strategies such as
martingale are permitted but require an explicit acknowledgement naming the
worst-case exposure across the configured number of steps.

## Runtime

Each running bot is an isolated executor driven by the shared tick stream —
bots never open their own sockets. The executor is a state machine: `waiting`
→ `condition met` → `proposal` → `buy` → `monitoring` → `settled` → back to
waiting, with a cooldown. Failures at any step are recorded and either retried
with backoff or escalated to a stop. A disconnected socket pauses every bot
immediately and they do not auto-resume; the user is shown what happened and
resumes deliberately.

## Log

Append-only, per run, one line per decision with timestamp, tick value, the
condition that evaluated true or false, the resulting action, and the API
response id. The log is filterable and exportable. This is the primary
debugging surface and is treated as a product feature, not diagnostics.

## Templates

Shipped, read-only starting points with documented logic and honest
descriptions: what the strategy assumes, what market conditions hurt it, and
its historical behaviour in backtests we ran, labelled as backtests. No
template is presented with a promised win rate. Using a template copies it into
the user's bots as a draft.

## Demo-first rule

A bot cannot be started on a real account until it has completed at least one
run on a demo account. The control states this rather than merely disabling
itself.

## States

No bots → an empty state offering templates and the builder, not a fake
example. Running with no trades yet → "waiting for entry condition" plus the
condition text. Stopped by limit → the limit and its value. Errored → the error
and the last successful action. Disconnected → all cards show `PAUSED — no
connection`.

## Mobile

Cards stack; the Stop control is always visible without expanding a card,
because stopping is the one action that must never be buried.

## API dependencies

`proposal`, `buy`, `proposal_open_contract`, `sell`, `portfolio`, `balance`,
`ticks`. Bot definitions and logs are stored application-side, not on Deriv.
