---
name: Read QFEX account state and trading history
description: Use the QFEX REST API to read positions, equity, trades, historic orders and fees for the authenticated user.
api: openapi/qfex-openapi-original.yml
operations: [read-user-positions, read-account-equity, read-user-trades, read-historic-orders, read-user-fees]
---

# Read QFEX account state and history (REST)

Base URL: `https://api.qfex.com`. Every request is HMAC-signed.

## Auth
Send four required headers on every call: `x-qfex-public-key`, `x-qfex-hmac-signature`
(HMAC-SHA256 of `${nonce}:${unix_ts}`, hex-encoded), `x-qfex-nonce`, `x-qfex-timestamp`.
Optionally send `x-qfex-requested-account-id` (UUID) to act as a subaccount; omit it for the primary.

## Steps
1. `read-user-positions` — GET `/user/positions` for current open positions.
2. `read-account-equity` — GET `/user/account-equity` for equity over preset durations.
3. `read-user-trades` — GET `/user/trade` for recent trades.
4. `read-historic-orders` — GET `/user/historic-orders` for filled / terminally closed orders.
5. `read-user-fees` — GET `/user/fees` for the account's fee data (tier-based maker/taker).

## Notes
Historic endpoints are queried by time range. Large result sets are also available as
CSV streams (`read-historic-orders-csv`, `read-trades-csv`, `read-historic-twaps-csv`).
See `conventions/qfex-conventions.yml` and `errors/qfex-problem-types.yml`.
