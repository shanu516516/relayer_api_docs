# Twilight Indexer API

## Overview

The **Twilight Indexer API** is a read-only HTTP service that exposes **address-level aggregates** and **event lists** derived from indexed Twilight protocol activity.

This API is intended for:
- analytics and attribution systems (e.g. affiliate and rewards platforms)
- dashboards and explorers
- backend services that require a simplified, query-friendly view of protocol activity

> **Note**
> This is an **off-chain indexer** API backed by indexed chain data.
> For authoritative protocol module REST endpoints and schemas, refer to the Twilight LCD Swagger UI: **https://lcd.twilight.org**.

---

## Base URL

**Production**  
`https://indexer.twilight.org/`

**Local development**  
`http://localhost:3030/`

---

## Authentication

No authentication is required.

---

## Conventions

- **t_address**: Twilight bech32 address (prefix `twilight…`).
- **qq_account / q_address**: QuisQuis (privacy-layer) account identifier associated with a `t_address`.
- **Units**: All BTC-denominated values are expressed in **`sats`** (integer satoshis).
- **Response format**: JSON.

---

## Table of Contents

1. [Health](#health)
2. [Decode Transaction](#decode-transaction)
3. [Transaction Count](#transaction-count)
4. [Funding Transfers](#funding-transfers)
5. [Exchange Withdrawal](#exchange-withdrawal)
6. [Exchange Deposit](#exchange-deposit)
7. [BTC Deposit](#btc-deposit)
8. [BTC Withdrawal](#btc-withdrawal)
9. [QQ Account Mapping](#qq-account-mapping)
10. [Address Summary](#address-summary)

---

## Health

### Health Check

**Description**  
Verifies that the Indexer API service is running.

**HTTP Method**  
`GET`

**Path**  
`/health`

**Example**

```bash
curl -X GET "https://nykschain.twilight.rest/api/health"
```

**Response (200)**

```json
{
  "status": "healthy",
  "service": "twilight-indexer-api"
}
```

---

## Decode Transaction

### Decode Transaction

**Description**  
Decodes a transaction from its bytecode representation and returns a structured JSON representation.

**HTTP Method**  
`POST`

**Path**  
`/decode-transaction`

**Request Body**

| Field | Type | Required | Description |
|---|---:|:---:|---|
| `tx_byte_code` | string | Yes | Hex-encoded transaction bytecode (may include `0x` prefix) |

**Example**

```bash
curl -X POST "https://nykschain.twilight.rest/api/decode-transaction" \
  -H "Content-Type: application/json" \
  -d '{"tx_byte_code":"0x123abc..."}'
```

**Response (200)**

```json
{
  "success": true,
  "tx_type": "transaction",
  "data": {}
}
```

---

## Transaction Count

### Get Transaction Count

**Description**  
Returns the total number of transactions associated with a Twilight address.

**HTTP Method**  
`GET`

**Path**  
`/transactions/{t_address}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `t_address` | string | Yes | Twilight address |

**Example**

```bash
curl -X GET "https://nykschain.twilight.rest/api/transactions/twilight1..."
```

**Response (200)**

```json
{
  "success": true,
  "t_address": "twilight1...",
  "transaction_count": 42
}
```

---

## Funding Transfers

### Funding Transfers

**Description**  
Returns **sats** funds moved between accounts for a given `t_address`.

**HTTP Method**  
`GET`

**Path**  
`/funding/{t_address}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `t_address` | string | Yes | Twilight address |

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `funds_moved[]` | array | List of funding-to-funding transfers |
| `funds_moved[].amount` | integer | Amount in sats |
| `funds_moved[].denom` | string | Denomination (expected `sats`) |
| `funds_moved[].block` | integer | Block height |

---

## Exchange Withdrawal

### Trading → Funding (Dark Burn)

**Description**  
Returns trading-to-funding movement for a `t_address` (**dark sats burned**).

**HTTP Method**  
`GET`

**Path**  
`/exchange-withdrawal/{t_address}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `t_address` | string | Yes | Twilight address |

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `dark_burned_sats[]` | array | List of burn events |
| `dark_burned_sats[].q_address` | string | QuisQuis account |
| `dark_burned_sats[].amount` | integer | Amount in sats |
| `dark_burned_sats[].block` | integer | Block height |

---

## Exchange Deposit

### Funding → Trading (Dark Mint)

**Description**  
Returns funding-to-trading movement for a `t_address` (**dark sats minted**).

**HTTP Method**  
`GET`

**Path**  
`/exchange-deposit/{t_address}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `t_address` | string | Yes | Twilight address |

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `dark_minted_sats[]` | array | List of mint events |
| `dark_minted_sats[].q_address` | string | QuisQuis account |
| `dark_minted_sats[].amount` | integer | Amount in sats |
| `dark_minted_sats[].block` | integer | Block height |

---

## BTC Deposit

### BTC → Twilight (Lit Mint)

**Description**  
Returns BTC bridge deposits recorded for a `t_address` (**lit sats minted**).

**HTTP Method**  
`GET`

**Path**  
`/btc-deposit/{t_address}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `t_address` | string | Yes | Twilight address |

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `lit_minted_sats[]` | array | List of mint events |
| `lit_minted_sats[].amount` | integer | Amount in sats |
| `lit_minted_sats[].block` | integer | Block height |

---

## BTC Withdrawal

### Twilight → BTC (Lit Burn)

**Description**  
Returns BTC bridge withdrawals recorded for a `t_address` (**lit sats burned**).

**HTTP Method**  
`GET`

**Path**  
`/btc-withdrawal/{t_address}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `t_address` | string | Yes | Twilight address |

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `lit_burned_sats[]` | array | List of burn events |
| `lit_burned_sats[].amount` | integer | Amount in sats |
| `lit_burned_sats[].block` | integer | Block height |

---

## QQ Account Mapping

### Get QQ Account(s)

**Description**  
Returns QuisQuis account(s) mapped to a Twilight address.

**HTTP Method**  
`GET`

**Path**  
`/qq-account/{t_address}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `t_address` | string | Yes | Twilight address |

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `q_addresses[]` | array | List of qq account mappings |
| `q_addresses[].qq_account` | string | QuisQuis account |
| `q_addresses[].block` | integer | Block height |

---

## Address Summary

### Get All Address Data

**Description**  
Convenience endpoint returning the aggregated payload across all core address metrics.

**HTTP Method**  
`GET`

**Path**  
`/address/{t_address}/all`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `t_address` | string | Yes | Twilight address |

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `transaction_count` | integer | Total transaction count |
| `funds_moved[]` | array | Funding transfers |
| `dark_burned_sats[]` | array | Exchange withdrawals (dark burn) |
| `dark_minted_sats[]` | array | Exchange deposits (dark mint) |
| `lit_minted_sats[]` | array | BTC deposits (lit mint) |
| `lit_burned_sats[]` | array | BTC withdrawals (lit burn) |

---

## HTTP Status Codes

| Code | Meaning |
|---:|---|
| `200` | Success |
| `400` | Invalid request |
| `500` | Server or database error |

---

## Glossary

| Term | Description |
|---|---|
| **t_address** | Twilight blockchain address |
| **qq_account / q_address** | QuisQuis privacy-layer account identifier |
| **sats** | Satoshis (integer BTC base unit) |
| **Lit minted / burned** | BTC bridge in/out of Twilight |
| **Dark minted / burned** | Funding ↔ trading movement |