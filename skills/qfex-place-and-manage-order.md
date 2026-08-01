---
name: Place and manage an order on the QFEX trade WebSocket
description: Authenticate to the QFEX trade WebSocket, subscribe to order responses, then place, modify and cancel an order safely.
api: asyncapi/qfex-trade-asyncapi.yml
operations: [authenticate, subscribeOrdersStream, placeOrder, modifyOrder, cancelOrder, getOrder]
---

# Place and manage a QFEX order

QFEX order entry runs over the multiplexed trade WebSocket at `wss://trade.qfex.com`.
All commands and responses are JSON.

## Rules
- Authenticate within **1 minute** of connecting or the socket is dropped. Re-authenticating an already-authenticated socket returns `AlreadyAuthenticated`.
- Auth is HMAC-SHA256: sign `${nonce}:${unix_ts}` with your secret key, hex-encode, and send with your public key, nonce and timestamp. The nonce must be unique within a 15-minute window.
- **Idempotency:** always set a `client_order_id` on `placeOrder`. On a retry after a network error, reuse the same id so a resend does not create a duplicate.
- You must **subscribe to order responses** before you will receive order state updates.
- Respect rate limits: 12,000 weight units / 60s per user across all connections; `add_order` costs 1 unit. Back off with jitter on `RateLimited`.

## Steps
1. Connect to `wss://trade.qfex.com`.
2. `authenticate` — send the HMAC auth command; wait for the authenticated response.
3. `subscribeOrdersStream` — subscribe so you receive `OrderResponse` updates.
4. `placeOrder` — send an add-order command (symbol, side, order_type, order_time_in_force, quantity, price, `client_order_id`).
5. `getOrder` — confirm the order's current state by id.
6. `modifyOrder` — adjust price/quantity if needed (must include order type).
7. `cancelOrder` — cancel by order id. Cancels use a separate rate-limit bucket.

## Errors
Handle the `err.error_code` envelope: `InvalidParameter`, `InvalidOrder`, `PermissionDenied`, `RateLimited`, `ServerError`. See `errors/qfex-problem-types.yml`.
