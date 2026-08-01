---
name: Stream real-time QFEX market data
description: Subscribe to QFEX real-time market data streams (BBO, order book, trades, candles) over the market-data WebSocket. No credentials required.
api: asyncapi/qfex-mds-asyncapi.yml
operations: [subscribeBboStream, subscribeOrderBookStream, subscribeTradesStream, subscribeCandlesStream]
---

# Stream real-time QFEX market data

Market data streams are served from the multiplexed WebSocket gateway at
`wss://mds.qfex.com`. Market data requires **no authentication**.

## Rules
- One channel carries all streams; select a stream by sending the matching subscribe command.
- Subscriptions are light (weight 0.1) but not free — avoid rapid subscribe/unsubscribe churn.
- Updates arrive over the same socket as `*Update` messages.

## Steps
1. Connect to `wss://mds.qfex.com`.
2. `subscribeBboStream` — top-of-book best bid/offer for a symbol (e.g. `AAPL-USD`).
3. `subscribeOrderBookStream` — pulsed order book depth.
4. `subscribeTradesStream` — public trade prints (includes `execution_type`).
5. `subscribeCandlesStream` — OHLCV candles.
6. Read `BboUpdate` / `OrderBookUpdate` / `TradeUpdate` / `CandleUpdate` messages.

## Notes
For historic (non-streaming) data use the REST API (`read-candles`, `read-refdata`,
`read-funding-historic`). The `qfex market bbo AAPL-USD` CLI command wraps this stream.
