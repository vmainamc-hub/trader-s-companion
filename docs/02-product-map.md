# 02 — Product Map

```
PRECISIONEDGE
│
├── Dashboard                      /
│   ├── Account summary            balance, equity, P/L, today, week
│   ├── Market overview            favourites, active, trending, recent
│   ├── Quick actions              trade, analyse, bot, build, AI, digits
│   ├── Active trading             open contracts, live P/L
│   ├── Bot activity               running bots, P/L, trades, win rate
│   ├── Market alerts              price, signal, bot, risk
│   └── Recent activity            trades, bot events, account events
│
├── Markets                        /markets
│   ├── Market Explorer            browse, search, filter, categories
│   ├── Watchlists                 /markets/watchlists
│   └── Market Detail              /markets/$symbol
│
├── Analysis                       /analysis
│   ├── Chart workspace            /analysis/$symbol
│   ├── Indicator library          modular, 15 indicators at v1
│   ├── Technical analysis panel   trend, momentum, volatility, S/R
│   └── Alerts from analysis
│
├── Trade                          /trade
│   ├── Manual Trader              /trade/$symbol
│   ├── Open Positions             /trade/positions
│   └── Trade History              /history
│
├── Digits                         /digits
│   ├── Digit Analyzer             /digits/$symbol
│   ├── Over/Under
│   ├── Even/Odd
│   ├── Matches/Differs
│   └── Tick Stream
│
├── AI                             /ai
│   ├── Market Analyst             /ai/analyst
│   ├── AI Chat                    /ai/chat
│   └── Strategy Assistant         /ai/strategy
│
├── Bots                           /bots
│   ├── My Bots                    /bots
│   ├── Running                    /bots/running
│   ├── Templates                  /bots/templates
│   ├── Imported / Archived
│   └── Bot Performance            /bots/$id
│
├── Bot Builder                    /builder
│   ├── Workspace / Canvas
│   ├── Toolbox (blocks)
│   ├── Inspector (parameters, risk)
│   ├── Validation
│   ├── Backtest                   /builder/$id/backtest
│   └── Deploy
│
├── Portfolio                      /portfolio
│   ├── Overview
│   ├── Performance                /portfolio/performance
│   └── Analytics
│
└── More
    ├── Trade History              /history
    ├── Performance                /portfolio/performance
    ├── Strategies                 /strategies
    ├── Watchlists                 /markets/watchlists
    ├── Alerts                     /alerts
    ├── Education                  /learn
    ├── Settings                   /settings
    ├── Help                       /help
    ├── API / Connection           /settings/connection
    └── About                      /about
```

## Cross-links (the loop, concretely)

| From | Action | To |
| --- | --- | --- |
| Market Detail | ANALYSE | `/analysis/$symbol` with symbol preloaded |
| Market Detail | TRADE | `/trade/$symbol` with symbol preloaded |
| Analysis | Open AI analysis | `/ai/analyst` seeded with current symbol + indicator state |
| Analysis | Create alert | Alert composer prefilled with the hovered level |
| AI Strategy Assistant | Generate bot | `/builder` with generated blocks laid out |
| Bot Builder | Backtest | `/builder/$id/backtest` |
| Bot Performance | Review trades | `/history?bot=$id` |
| Trade History | Analyse this market | `/analysis/$symbol` at the trade's timestamp |

## Module status model

Every module carries one of: `LIVE`, `PARTIAL`, `NOT BUILT`. `NOT BUILT`
surfaces render an explicit placeholder naming the phase — never a mock.
