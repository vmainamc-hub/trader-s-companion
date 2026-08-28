# Design — Desktop Wireframes

Reference resolution 1440×900. All frames assume the L0 chrome of doc 05
(52px header, 24px status bar) and omit it below the first frame. Boxes marked
`NOT BUILT — Phase N` are drawn deliberately: the shell ships with honest
placeholders rather than dead controls.

Legend: `▾` dropdown · `●` live indicator · `▸` disclosure · `▣` primary action ·
`░` skeleton/loading region · `⚑` warning affordance.

---

## Dashboard — `/`

```
┌ HEADER ──────────────────────────────────────────────────────────────────────┐
│ ◆ PRECISIONEDGE  Dashboard Markets Analysis Trade Digits AI Bots Builder     │
│                  Portfolio More ▾         [DEMO ▾] 10,000.00 USD ⌘K ● ⚑ ? ◉ │
├──────────────────────────────────────────────────────────────────────────────┤
│ ┌ STATUS STRIP ────────────────────────────────────────────────────────────┐ │
│ │ DEMO ACCOUNT VRTC1234567 · practice funds · no real money at risk        │ │
│ └──────────────────────────────────────────────────────────────────────────┘ │
│                                                                              │
│ ┌ EQUITY ─────────────┬ TODAY ──────────┬ OPEN ─────────┬ BOTS ────────────┐ │
│ │ 10,412.60 USD       │ +142.60  +1.39% │ 3 positions   │ 2 running        │ │
│ │ balance 10,270.00   │ 12 trades       │ stake 45.00   │ 1 paused ⚑       │ │
│ │ + indicative 142.60 │ win rate 58%    │ worst −18.00  │ 4 idle           │ │
│ │ ⓘ indicative        │ n=12 · low      │ ▸ Portfolio   │ ▸ Bots           │ │
│ └─────────────────────┴─────────────────┴───────────────┴──────────────────┘ │
│                                                                              │
│ ┌ EQUITY CURVE (session) ─────────────────────┬ WATCHLIST ─────────────────┐ │
│ │                                    ╭──╮     │ R_100   1234.56  +0.12% ● │ │
│ │                        ╭───╮   ╭───╯  ╰─    │ R_75     892.31  −0.04% ● │ │
│ │      ╭──╮   ╭─────────╯   ╰───╯             │ R_50     441.09  +0.31% ● │ │
│ │  ╭───╯  ╰───╯                               │ 1HZ100V 5012.7   +0.02% ● │ │
│ │──┴──────────────────────────────────────────│ + Add symbol               │ │
│ │ 09:00        11:00        13:00      15:00  │ ▸ Markets                  │ │
│ └─────────────────────────────────────────────┴────────────────────────────┘ │
│                                                                              │
│ ┌ RUNNING BOTS ───────────────────────────────┬ RECENT ACTIVITY ───────────┐ │
│ │ ● Even/Odd Guard   R_100  +32.40  18 trades │ 15:41 BUY  R_100 DIGITEVEN │ │
│ │   next: waiting for streak ≥ 4              │ 15:40 SELL R_75  won +9.20 │ │
│ │ ● Rise Momentum    R_75   −11.00   6 trades │ 15:38 BOT  paused: max     │ │
│ │   next: cooldown 00:42                      │            consecutive     │ │
│ │ ⚑ Digit Over 5     R_50   paused            │            losses reached  │ │
│ │   reason: max consecutive losses (3/3)      │ 15:31 BUY  R_50  DIGITOVER │ │
│ │ [ Stop all ⌘⇧X ]                            │ ▸ Trade history            │ │
│ └─────────────────────────────────────────────┴────────────────────────────┘ │
├──────────────────────────────────────────────────────────────────────────────┤
│ ● Connected · ticks 4 subs · latency 41ms           Markets open · 15:41 UTC │
└──────────────────────────────────────────────────────────────────────────────┘
```

The dashboard answers four questions in one screen: what mode am I in, what is
my money doing, what are my bots doing, and what just happened. Nothing on it
is a vanity metric; every tile links to the canonical surface for its concept.

---

## Markets — `/markets`

```
┌ MARKETS ─────────────────────────────────────────────────────────────────────┐
│ [ Search symbols…            ] [All ▾][Synthetics ▾][Open only ☑]  ⊞ ☰       │
├──────────────────────────────────────────────────────────────────────────────┤
│ SYMBOL        NAME                   LAST      CHG%    TICK  SPARK      ACT  │
│ R_10          Volatility 10 Index    6412.31  +0.08%   2s   ╭╮╭─╮╭    ☆ ▸   │
│ R_25          Volatility 25 Index    2871.44  −0.21%   2s   ╰╮ ╰╯╰    ☆ ▸   │
│ R_50          Volatility 50 Index     441.09  +0.31%   2s   ╭─╯╭╮      ★ ▸   │
│ R_75          Volatility 75 Index     892.31  −0.04%   2s   ╮╭╯╰╮      ★ ▸   │
│ R_100         Volatility 100 Index   1234.56  +0.12%   2s   ╭╯  ╰╮     ★ ▸   │
│ 1HZ100V       Vol 100 (1s) Index     5012.70  +0.02%   1s   ╱╲╱╲╱     ☆ ▸   │
│ ────────────────────────────────────────────────────────────────────────────│
│ FOREX · CLOSED                                                     ⚑ closed  │
│ frxEURUSD     EUR/USD                 1.0842   −0.02%   —    ──────    ☆ ▸   │
├──────────────────────────────────────────────────────────────────────────────┤
│ Row ▸ opens the symbol drawer: last 200 ticks, contract types available for  │
│ this symbol (from contracts_for), quick links to Analysis / Digits / Trade.  │
└──────────────────────────────────────────────────────────────────────────────┘
```

Symbols are grouped by market with closed markets shown, not hidden — a trader
needs to know a market exists and is shut. Availability of contract types is
read from the API per symbol, never hardcoded into the table.

---

## Analysis — `/analysis?symbol=R_100&tf=1m`

```
┌ Analysis · R_100 ▾   [1t 1m 5m 15m 1h 1d]  [Candles ▾] [Indicators (3) ▾]   │
├───────────────────────────────────────────────────────────────┬──────────────┤
│                                                        ╷      │ INDICATORS   │
│                                          ╷    ╻  ╷ ╻   ┃      │ ─────────── │
│                            ╻   ╷    ╻  ╻ ┃  ╻ ┃  ┃ ┃   ╹      │ ☑ EMA 20    │
│                     ╷  ╻ ╻ ┃ ╻ ┃  ╻ ┃  ┃ ┃  ┃ ╹  ╹ ╹          │ ☑ EMA 50    │
│              ╻ ╷  ╻ ┃  ┃ ┃ ╹ ╹ ╹  ┃ ╹  ╹ ╹                    │ ☑ RSI 14    │
│         ╻  ╻ ┃ ┃  ┃ ╹  ╹ ╹                                    │ + Add       │
│  ───────┃──┃─╹─╹──╹────────────────────────────── EMA20 ──────│              │
│                                                                │ SIGNALS     │
├────────────────────────────────────────────────────────────────│ ─────────── │
│ RSI 14                                                         │ EMA cross ↑ │
│  70 ─────────────────────────────────────────────────────────  │  2 bars ago │
│           ╭─╮        ╭──╮      ╭───╮                           │ RSI 61 · no │
│  ────╭────╯ ╰────────╯  ╰──────╯   ╰────  58.2                 │  extreme    │
│  30 ─────────────────────────────────────────────────────────  │ ⓘ signals   │
├────────────────────────────────────────────────────────────────│  describe,  │
│ 15:41:02  1234.56 ▲   15:41:00  1234.44 ▼   15:40:58  1234.51 ▲│  they do    │
│ tick tape (last 40) ─────────────────────────────────────────  │  not advise │
└────────────────────────────────────────────────────────────────┴──────────────┘
```

Indicators are computed locally from `ticks_history`, and the panel states the
sample the computation used. The signals column never renders an imperative
("BUY NOW"); it renders an observation plus the condition that produced it.

---

## Digits — `/digits?symbol=R_100`

```
┌ Digits · R_100 ▾    sample [100 ▾] ticks · updated 15:41:02 · n=100          │
├──────────────────────────────────────────────────────────────────────────────┤
│ DIGIT DISTRIBUTION                                                           │
│  0 ████████████ 12.0%   5 █████████ 9.0%                                     │
│  1 █████████ 9.0%       6 ██████████ 10.0%                                   │
│  2 ███████████ 11.0%    7 ████████ 8.0%                                      │
│  3 ██████████ 10.0%     8 ███████████ 11.0%                                  │
│  4 █████████ 9.0%       9 ███████████ 11.0%                                  │
│  expected 10.0% each · ±2σ band 4.0–16.0% at n=100 · nothing is outside it   │
├────────────────────────────────┬─────────────────────────────────────────────┤
│ EVEN / ODD                     │ OVER / UNDER (barrier 5 ▾)                  │
│ even 51%  ███████████████      │ over  44%  █████████████                    │
│ odd  49%  ██████████████       │ under 51%  ███████████████  equal 5%        │
│ current streak: odd ×3         │ current streak: under ×2                    │
├────────────────────────────────┴─────────────────────────────────────────────┤
│ LAST 30 DIGITS                                                               │
│ 7 3 9 1 4 8 2 0 5 7 3 3 9 6 1 8 4 0 2 7 5 9 1 3 8 6 0 4 2 7                  │
│ ⚑ Each tick is independent. Streaks and gaps are not predictive; this panel  │
│   reports what happened, and the payout still assumes a fair 10% per digit.  │
├──────────────────────────────────────────────────────────────────────────────┤
│ [ Trade this view ▸ ]  prefills the ticket with DIGITUNDER 5 · you confirm   │
└──────────────────────────────────────────────────────────────────────────────┘
```

The disclaimer is part of the layout, not a footnote. Digit surfaces are where
an honesty-first product either earns trust or quietly becomes a casino UI.

---

## Trade — `/trade?symbol=R_100`

```
┌ Trade · R_100 ▾  [DEMO]                                                      │
├──────────────────────────────────────────────┬───────────────────────────────┤
│ CHART (shared component with Analysis)       │ TICKET                        │
│                                              │ ───────────────────────────── │
│                              ╭─╮             │ Type   [Rise/Fall        ▾]   │
│                    ╭────╮ ╭──╯ ╰──           │  ⓘ types come from            │
│          ╭────╮╭───╯    ╰─╯                  │    contracts_for(R_100)       │
│  ────────╯    ╰╯                             │ Dur.   [5] [ticks ▾]          │
│                                              │ Stake  [10.00] USD            │
│  entry marker ▲ · barrier line ─ ─ ─         │ Barrier  —                    │
│                                              │ ───────────────────────────── │
├──────────────────────────────────────────────│ PROPOSAL          ● live      │
│ OPEN POSITIONS (3)                           │ payout   19.42 USD            │
│ ID      TYPE      SYM   STAKE  P/L    ACT    │ return   +94.2%               │
│ 1024...  CALL     R_100 10.00 +2.10  [Sell]  │ ask      10.00 USD            │
│ 1024...  DIGITEVN R_100  5.00 −5.00  [ — ]   │ updated  0.4s ago             │
│ 1024...  PUT      R_75  30.00 +9.80  [Sell]  │ ───────────────────────────── │
│ ▸ full history                               │ ▣ BUY · Rise 10.00 → 19.42    │
│                                              │ [confirm again in 3s ▾]       │
│                                              │ ⚑ demo funds                  │
└──────────────────────────────────────────────┴───────────────────────────────┘
```

The buy button always shows stake and payout in its own label. The proposal is
a subscription; a stale proposal disables the button rather than buying at an
unknown price.

---

## Bots — `/bots`

```
┌ Bots  [ My Bots | Running (2) | Templates | Archived ]        [ + New bot ]  │
├──────────────────────────────────────────────────────────────────────────────┤
│ ● Even/Odd Guard          R_100 · DEMO · v4                                  │
│   running 42m · 18 trades · +32.40 · win 61% (n=18, low confidence)          │
│   risk: stake 1.00 · max loss 50.00 (used 11.00) · max consec. losses 3 (0)  │
│   state: WAITING — condition "odd streak ≥ 4" not met · next check next tick │
│   [ Pause ] [ Stop ] [ Log ▸ ] [ Edit ▸ ]                                    │
│ ────────────────────────────────────────────────────────────────────────────│
│ ⚑ Digit Over 5            R_50 · DEMO · v2                                   │
│   PAUSED by risk envelope at 15:38 · max consecutive losses 3/3              │
│   resume requires acknowledgement · [ Review log ▸ ] [ Resume… ]             │
│ ────────────────────────────────────────────────────────────────────────────│
│ ○ Rise Momentum           R_75 · DEMO · v7 · idle                            │
│   last run yesterday · 41 trades · −18.20 · [ Run… ] [ Backtest ▸ ]          │
├──────────────────────────────────────────────────────────────────────────────┤
│ [ ⛔ Stop all bots ⌘⇧X ]        Bots run in this browser tab only. Closing   │
│                                 the tab stops them. Nothing runs server-side.│
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Bot Builder — `/builder/:id`

```
┌ Builder · Even/Odd Guard · v4 (draft)      [ Validate ] [ Backtest ] [ Save ]│
├───────────────┬──────────────────────────────────────────┬───────────────────┤
│ BLOCKS        │ CANVAS                                   │ INSPECTOR         │
│ ───────────── │                                          │ ───────────────── │
│ ▾ Triggers    │  ┌ ON TICK ─────────────────┐            │ Block: CONDITION  │
│   On tick     │  │ symbol R_100             │            │ ───────────────── │
│   On candle   │  └────────────┬─────────────┘            │ left  digit streak│
│ ▾ Conditions  │               ▼                          │ op    ≥           │
│   Digit stat  │  ┌ CONDITION ───────────────┐            │ right 4           │
│   Indicator   │  │ odd streak ≥ 4           │  ⚑ 1 warn  │ ───────────────── │
│   Streak      │  └────────┬─────────┬───────┘            │ ⚑ Streak length   │
│ ▾ Actions     │      true ▼         ▼ false              │   has no          │
│   Buy         │  ┌ BUY ─────────┐  ┌ WAIT ──┐            │   predictive      │
│   Wait        │  │ DIGITEVEN    │  │ 1 tick │            │   power on        │
│   Stop        │  │ stake 1.00   │  └────────┘            │   synthetics.     │
│ ▾ Risk        │  │ 1 tick       │                        │   The bot may     │
│   Loss guard  │  └──────┬───────┘                        │   still trade it; │
│   Cooldown    │         ▼                                │   the report will │
│               │  ┌ COOLDOWN 3 ticks ────────┐            │   say so.         │
│               │  └──────────────────────────┘            │                   │
├───────────────┴──────────────────────────────────────────┴───────────────────┤
│ RISK ENVELOPE (required — a bot cannot be saved without it)                  │
│ stake 1.00 · max daily loss [50.00] · max consecutive losses [3]             │
│ take profit [—] · max trades/day [200] · run on [DEMO ▾]  ⚑ real requires    │
│                                                             explicit switch  │
├──────────────────────────────────────────────────────────────────────────────┤
│ VALIDATION  1 error · 1 warning                                              │
│ ✗ BUY block: duration unit "ticks" invalid for contract type on frxEURUSD    │
│ ⚑ CONDITION: streak-based entry — see note in inspector                      │
└──────────────────────────────────────────────────────────────────────────────┘
```

Save is blocked on errors, never on warnings. Warnings are opinions and are
recorded with the version so a later report can reference them.

---

## Backtesting — `/backtest/:botId`

```
┌ Backtest · Even/Odd Guard v4 · R_100 · 5,000 ticks (max available)           │
├──────────────────────────────────────────────────────────────────────────────┤
│ SETUP  symbol [R_100 ▾]  ticks [5000 ▾]  stake [1.00]  payout model [API ▾]  │
│ ⚑ Deriv provides at most 5,000 historical ticks per request. That is roughly │
│   2.8 hours on a 2s symbol. This is a smoke test, not a strategy validation. │
├──────────────────────────────────────────────────────────────────────────────┤
│ EQUITY CURVE                                        RESULTS                  │
│                            ╭─╮                      trades      64           │
│                    ╭───────╯ ╰──╮                   wins        37 (57.8%)   │
│          ╭────╮╭───╯            ╰──                 profit      +8.40        │
│  ────────╯    ╰╯                                    profit fctr 1.09         │
│  0 ─────────────────────────────────────            max DD      −12.60       │
│                                                     longest L   5            │
│ DRAWDOWN                                            expectancy  +0.13/trade  │
│  0 ────╮──────╮────────────╮────────────            ─────────────────────    │
│        ╰──╮   ╰───╮        ╰──╮                     ⚑ n=64 is far below the  │
│  −12.60 ──╯       ╰───────────╯                       ~400 trades needed to  │
│                                                       separate this edge     │
│                                                       from noise at 95%.     │
├──────────────────────────────────────────────────────────────────────────────┤
│ TRADE LIST  ▸ 64 rows · entry tick, digit, contract, payout used, P/L, reason│
│ [ Compare with v3 ▸ ]  [ Run on demo instead ▸ ]                             │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Portfolio — `/portfolio`

```
┌ Portfolio  [ Open | Closed | By bot | By symbol ]     [DEMO ▾]  [ Export ▾ ] │
├──────────────────────────────────────────────────────────────────────────────┤
│ balance 10,270.00 · open stake 45.00 · indicative equity 10,412.60 ⓘ         │
│ ⓘ Indicative equity marks open contracts at the current proposal value from  │
│   the API. It is not a settlement value and it will move.                    │
├──────────────────────────────────────────────────────────────────────────────┤
│ ID        OPENED    SYMBOL TYPE      STAKE  PAYOUT  NOW    P/L    SOURCE     │
│ 1024773…  15:41:02  R_100  DIGITEVEN  5.00   9.50   4.10  −0.90  Even/Odd v4│
│ 1024772…  15:40:31  R_100  CALL      10.00  19.42  12.10  +2.10  manual      │
│ 1024770…  15:38:12  R_75   PUT       30.00  57.60  39.80  +9.80  Rise Mom v7│
│                                                            ▸ contract detail │
├──────────────────────────────────────────────────────────────────────────────┤
│ CONTRACT DETAIL (drawer)                                                     │
│ 1024773… · DIGITEVEN · barrier — · entry 1234.56 · entry digit 6             │
│ opened by bot Even/Odd Guard v4 · rule "odd streak ≥ 4" fired at 15:41:01    │
│ ▸ open the exact block that produced this trade                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Performance — `/performance`

```
┌ Performance   range [30d ▾]  account [DEMO ▾]  source [All ▾]                │
├──────────────────────────────────────────────────────────────────────────────┤
│         MANUAL            BOTS               BACKTEST                        │
│ trades  84                412                1,940                           │
│ win %   52.4% (n=84)      55.1% (n=412)      57.3% (n=1940)                  │
│ net     −22.40            +118.60            +301.20  ⚑ not comparable       │
│ conf.   ±10.7pp           ±4.8pp             ±2.2pp                          │
│ ─────────────────────────────────────────────────────────────────────────── │
│ ⚑ Backtest results are modelled, not executed. They are shown in a separate  │
│   column and are never added into a total with real or demo results.         │
├──────────────────────────────────────────────────────────────────────────────┤
│ BREAKDOWN  [ by symbol | by contract type | by hour | by bot | by version ]  │
│ R_100   214 trades  56.1%  +84.20   ████████████                             │
│ R_75    121 trades  51.2%  −12.40   ██████                                   │
│ R_50     77 trades  58.4%  +46.80   ████                                     │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## AI Assistant — `/ai`

```
┌ AI  [ Analyst | Chat | Strategy assistant ]        model gpt-5.4 · server-run│
├───────────────────────────────────────────────┬──────────────────────────────┤
│ ▸ You: what does R_100 look like right now?   │ CONTEXT SENT                 │
│                                               │ ──────────────────────────── │
│ ◆ Assistant:                                  │ symbol   R_100               │
│   Over the last 500 ticks R_100 has drifted   │ ticks    500 (locally        │
│   +0.12% with realised volatility near its    │          computed)           │
│   30-day median. EMA20 crossed above EMA50    │ EMA20/50 1234.9 / 1233.1     │
│   two bars ago; RSI 14 is 58.2, inside the    │ RSI14    58.2                │
│   neutral band. Digit distribution over the   │ digits   n=100, χ² p=0.71    │
│   last 100 ticks is inside its ±2σ band       │ ──────────────────────────── │
│   (χ² p=0.71), so there is no digit skew to   │ The model never receives raw │
│   act on.                                     │ tick arrays and never        │
│                                               │ computes the numbers itself. │
│   I can describe what is happening. I cannot  │ It explains values this app  │
│   tell you what will happen next, and I will  │ computed deterministically.  │
│   not tell you what to trade.                 │                              │
│                                               │                              │
│ [ Ask about this symbol…                    ] │                              │
└───────────────────────────────────────────────┴──────────────────────────────┘
```

---

## Alerts — `/alerts`

```
┌ Alerts  [ Active (4) | Triggered | New ]              subscriptions 4 / 8 ⚑  │
├──────────────────────────────────────────────────────────────────────────────┤
│ ● R_100 price crosses above 1240.00      · once · browser + in-app           │
│ ● R_75 RSI 14 leaves 30–70               · repeat, 5m cooldown               │
│ ● R_100 digit 7 absent for 40 ticks      · repeat · ⚑ descriptive only       │
│ ○ Bot "Even/Odd Guard" pauses            · always on · cannot be disabled    │
├──────────────────────────────────────────────────────────────────────────────┤
│ ⚑ Alerts are evaluated in this tab while it is open. There is no server-side │
│   monitoring, so nothing fires while the app is closed. Each price/indicator │
│   alert holds a tick subscription against the cap shown above.               │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Settings — `/settings`

```
┌ Settings  [ Profile | Trading | Accounts | Connection | Data | Security ]    │
├──────────────────────────────────────────────────────────────────────────────┤
│ TRADING DEFAULTS                                                             │
│ default stake      [ 10.00 ] USD                                             │
│ default duration   [ 5 ] [ ticks ▾ ]                                         │
│ confirm every buy  [ ☑ ]  · re-confirm above [ 50.00 ]                       │
│ daily loss guard   [ 100.00 ]  ⚑ blocks new manual buys and pauses all bots  │
│ ───────────────────────────────────────────────────────────────────────────  │
│ CONNECTION DIAGNOSTICS                                    ● open · 41ms      │
│ authorised 15:02:11 · reconnects today 1 · last replay 4 subscriptions       │
│ active streams  ticks:R_100 (2) · ticks:R_75 (1) · proposal:… (1)            │
│ [ Reconnect now ]  [ Copy diagnostics ]                                      │
└──────────────────────────────────────────────────────────────────────────────┘
```
