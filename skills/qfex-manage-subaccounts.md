---
name: Create QFEX subaccounts and transfer balance
description: Use the QFEX REST API to list and create subaccounts, transfer balance between accounts, and set leverage.
api: openapi/qfex-openapi-original.yml
operations: [list-subaccounts, create-subaccount, transfer-balance, set-user-leverage]
---

# Manage QFEX subaccounts and balance (REST)

Base URL: `https://api.qfex.com`. HMAC-signed requests (see the account-and-history skill for headers).
Subaccounts let you separate margin and risk under one master account.

## Steps
1. `list-subaccounts` — GET `/user/subaccounts` returns subaccount UUIDs (excludes the master account id).
2. `create-subaccount` — POST `/user/subaccounts` allocates a new subaccount id.
3. `transfer-balance` — POST `/user/transfer` moves available balance between two of the user's accounts (master or subaccounts).
4. `set-user-leverage` — POST `/user/leverage` sets leverage for the authenticated user/account.

## Notes
Target a specific subaccount by setting `x-qfex-requested-account-id` to its UUID on the request.
`transfer-balance` requires sufficient **available** (non-margined) balance.
Handle the WebSocket/REST error envelope; see `errors/qfex-problem-types.yml`.
