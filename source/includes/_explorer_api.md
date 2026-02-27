# Explorer API

## Overview

The **Twilight Explorer API** is a read-only REST API that provides comprehensive access to indexed Twilight blockchain data including blocks, transactions, accounts, validators, BTC bridge operations, fragments, and zkOS transactions.

The API includes:
- Paginated list and detail endpoints for all indexed data
- Real-time WebSocket subscriptions for new blocks, transactions, deposits, and withdrawals
- Swagger UI for interactive endpoint testing

> **Note**
> This API serves indexed data from PostgreSQL. For authoritative on-chain state, refer to the Twilight LCD: **https://lcd.twilight.org**.

---

## Base URL

**Production**
`https://indexer.twilight.org/api`

**Local development**
`http://localhost:3001/api`

**Swagger UI**
`https://indexer.twilight.org/api/docs`

---

## Authentication

No authentication is required.

---

## Rate Limiting

All `/api` routes are rate limited.

| Setting | Value |
|---|---:|
| Window | 60 seconds |
| Max requests | 100 per window |

---

## Conventions

- **Pagination**: All list endpoints accept `page` (default: 1) and `limit` (default: 20, max: 100) query parameters.
- **BigInt values**: Balances, gas amounts, and volumes are returned as **strings** for JSON safety.
- **Timestamps**: ISO 8601 format.
- **Response format**: JSON.

---

## Table of Contents

1. [Health Checks](#health-checks)
2. [Blocks](#blocks)
3. [Transactions](#transactions)
4. [Accounts](#accounts)
5. [Validators](#validators)
6. [Statistics](#statistics)
7. [BTC Deposits](#btc-deposits)
8. [BTC Withdrawals](#btc-withdrawals)
9. [Reserves](#reserves)
10. [Fragments](#fragments)
11. [zkOS Transfers](#zkos-transfers)
12. [zkOS Mint/Burn](#zkos-mint-burn)
13. [Bitcoin Info](#bitcoin-info)
14. [Search](#search)
15. [WebSocket API](#websocket-api)

---

## Health Checks

### Liveness Probe

**Description**
Simple liveness check. Returns 200 if the API process is running.

**HTTP Method**
`GET`

**Path**
`/health/live`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/health/live"
```

> The result from the above endpoint looks like this:

```json
{
  "status": "ok"
}
```

---

### Readiness Probe

**Description**
Comprehensive readiness check. Verifies database, Redis, and indexer state.

**HTTP Method**
`GET`

**Path**
`/health/ready`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/health/ready"
```

> The result from the above endpoint looks like this:

```json
{
  "status": "ready",
  "checks": {
    "database": "ok",
    "redis": "ok",
    "lastIndexedHeight": "428041"
  },
  "timestamp": "2024-02-26T10:00:00.000Z"
}
```

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `status` | string | `ready` or `not_ready` |
| `checks.database` | string | `ok` or `failed` |
| `checks.redis` | string | `ok`, `failed`, or `unavailable` |
| `checks.lastIndexedHeight` | string | Last indexed block height |
| `timestamp` | string | ISO 8601 timestamp |

Returns `200` when healthy, `503` when not ready.

---

## Blocks

### List Blocks

**Description**
Returns a paginated list of blocks ordered by height descending.

**HTTP Method**
`GET`

**Path**
`/api/blocks`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/blocks?page=1&limit=20"
```

```javascript
fetch("https://indexer.twilight.org/api/blocks?page=1&limit=20")
  .then((response) => response.json())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "data": [
    {
      "height": 428041,
      "hash": "A1B2C3D4...",
      "timestamp": "2024-02-26T10:00:00.000Z",
      "proposer": "base64address",
      "proposerMoniker": "twilight-validator-1",
      "proposerOperator": "twilightvaloper1...",
      "txCount": 5,
      "gasUsed": "125000",
      "gasWanted": "200000"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 428041,
    "totalPages": 21403
  }
}
```

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `data[].height` | integer | Block height |
| `data[].hash` | string | Block hash |
| `data[].timestamp` | string | Block time (ISO 8601) |
| `data[].proposer` | string | Proposer address (base64) |
| `data[].proposerMoniker` | string | Validator moniker |
| `data[].txCount` | integer | Transaction count |
| `data[].gasUsed` | string | Total gas used |
| `data[].gasWanted` | string | Total gas wanted |

**Cache:** 10 seconds

---

### Get Latest Block

**Description**
Returns the most recent block with its last 10 transactions.

**HTTP Method**
`GET`

**Path**
`/api/blocks/latest`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/blocks/latest"
```

> The result from the above endpoint looks like this:

```json
{
  "height": 428041,
  "hash": "A1B2C3D4...",
  "timestamp": "2024-02-26T10:00:00.000Z",
  "proposer": "base64address",
  "txCount": 5,
  "transactions": []
}
```

---

### Get Block by Height

**Description**
Returns a specific block by height with all transactions and events.

**HTTP Method**
`GET`

**Path**
`/api/blocks/{height}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `height` | integer | Yes | Block height |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/blocks/428041"
```

> The result from the above endpoint looks like this:

```json
{
  "height": 428041,
  "hash": "A1B2C3D4...",
  "timestamp": "2024-02-26T10:00:00.000Z",
  "proposer": "base64address",
  "txCount": 5,
  "gasUsed": "125000",
  "gasWanted": "200000",
  "transactions": [],
  "events": []
}
```

---

### Get Block Transactions

**Description**
Returns paginated transactions for a specific block.

**HTTP Method**
`GET`

**Path**
`/api/blocks/{height}/transactions`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `height` | integer | Yes | Block height |

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/blocks/428041/transactions?page=1&limit=20"
```

---

## Transactions

### List Transactions

**Description**
Returns a paginated, filterable list of transactions.

**HTTP Method**
`GET`

**Path**
`/api/txs`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |
| `type` | string | No | Filter by message type |
| `status` | string | No | `success` or `failed` |
| `module` | string | No | `bridge`, `forks`, `volt`, or `zkos` |
| `programType` | string | No | zkOS program type filter |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/txs?module=bridge&page=1&limit=20"
```

```javascript
fetch("https://indexer.twilight.org/api/txs?module=bridge&page=1&limit=20")
  .then((response) => response.json())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "data": [
    {
      "hash": "A1B2C3D4...",
      "blockHeight": 428041,
      "blockTime": "2024-02-26T10:00:00.000Z",
      "type": "/twilight-project.nyks.bridge.MsgConfirmBtcDeposit",
      "messageTypes": ["/twilight-project.nyks.bridge.MsgConfirmBtcDeposit"],
      "status": "success",
      "gasUsed": "125000"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 654321,
    "totalPages": 32717
  }
}
```

**Cache:** 10 seconds

---

### Get Recent Transactions

**Description**
Returns the most recent transactions.

**HTTP Method**
`GET`

**Path**
`/api/txs/recent`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `limit` | integer | No | Number of results (default: 10, max: 50) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/txs/recent?limit=10"
```

**Cache:** 10 seconds

---

### Get Transaction by Hash

**Description**
Returns complete transaction details including decoded messages, events, and zkOS data.

**HTTP Method**
`GET`

**Path**
`/api/txs/{hash}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `hash` | string | Yes | Transaction hash (case-insensitive) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/txs/A1B2C3D4..."
```

> The result from the above endpoint looks like this:

```json
{
  "hash": "A1B2C3D4...",
  "blockHeight": 428041,
  "blockTime": "2024-02-26T10:00:00.000Z",
  "type": "/twilight-project.nyks.bridge.MsgConfirmBtcDeposit",
  "messageTypes": [],
  "messages": [
    {
      "type": "/twilight-project.nyks.bridge.MsgConfirmBtcDeposit",
      "typeName": "CONFIRM_BTC_DEPOSIT",
      "data": {}
    }
  ],
  "fee": {},
  "gasUsed": "125000",
  "gasWanted": "200000",
  "memo": "",
  "status": "success",
  "errorLog": null,
  "signers": [],
  "events": [],
  "block": {},
  "zkosDecodedData": null
}
```

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `hash` | string | Transaction hash |
| `blockHeight` | integer | Block containing this transaction |
| `type` | string | Primary message type |
| `messageTypes` | array | All message types |
| `messages` | array | Decoded messages with type, typeName, and data |
| `fee` | object | Fee object |
| `gasUsed` | string | Gas used |
| `gasWanted` | string | Gas wanted |
| `status` | string | `success` or `failed` |
| `errorLog` | string | Error message if failed |
| `signers` | array | Signer public keys |
| `events` | array | Transaction events |
| `zkosDecodedData` | object | Decoded zkOS data (if applicable) |

---

### Get Transaction Type Stats

**Description**
Returns the top 20 transaction types by count.

**HTTP Method**
`GET`

**Path**
`/api/txs/types/stats`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/txs/types/stats"
```

> The result from the above endpoint looks like this:

```json
[
  {
    "type": "/twilight-project.nyks.bridge.MsgConfirmBtcDeposit",
    "count": 5432
  }
]
```

**Cache:** 30 seconds

---

### Get Transactions by Script Address

**Description**
Returns transactions associated with a zkOS script address.

**HTTP Method**
`GET`

**Path**
`/api/txs/script/{scriptAddress}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `scriptAddress` | string | Yes | zkOS script address (min 10 chars) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/txs/script/script1abc..."
```

**Cache:** 10 seconds

---

## Accounts

### List Accounts

**Description**
Returns accounts ordered by transaction count.

**HTTP Method**
`GET`

**Path**
`/api/accounts`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/accounts?page=1&limit=20"
```

> The result from the above endpoint looks like this:

```json
{
  "data": [
    {
      "address": "twilight1...",
      "balance": "1000000",
      "txCount": 42
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 5432,
    "totalPages": 272
  }
}
```

---

### Get Account Details

**Description**
Returns comprehensive account details including balances, deposits, withdrawals, clearing account, zkOS operations, and fragment signer records.

**HTTP Method**
`GET`

**Path**
`/api/accounts/{address}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `address` | string | Yes | Twilight address (`twilight1...` or `twilightvaloper1...`) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/accounts/twilight1..."
```

> The result from the above endpoint looks like this:

```json
{
  "account": {
    "address": "twilight1...",
    "balance": "1000000",
    "txCount": 42
  },
  "balances": [
    { "denom": "utlx", "amount": "1000000" }
  ],
  "deposits": [],
  "withdrawals": [],
  "clearingAccount": null,
  "zkosOperations": [],
  "fragmentSigners": []
}
```

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `account` | object | Basic account info (address, balance, txCount) |
| `balances` | array | On-chain token balances from LCD |
| `deposits` | array | Recent BTC deposits |
| `withdrawals` | array | Recent BTC withdrawals |
| `clearingAccount` | object | Clearing account info (if exists) |
| `zkosOperations` | array | Recent zkOS mint/burn operations |
| `fragmentSigners` | array | Fragment signer records |

---

### Get Account Transactions

**Description**
Returns transactions involving an address (as signer or in message data).

**HTTP Method**
`GET`

**Path**
`/api/accounts/{address}/transactions`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `address` | string | Yes | Twilight address |

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/accounts/twilight1.../transactions?page=1&limit=20"
```

---

## Validators

### List Validators

**Description**
Returns validators from LCD.

**HTTP Method**
`GET`

**Path**
`/api/validators`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `status` | string | No | `BOND_STATUS_BONDED` (default), `BOND_STATUS_UNBONDED`, `BOND_STATUS_UNBONDING` |
| `limit` | integer | No | Max results (default: 200, max: 500) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/validators?status=BOND_STATUS_BONDED"
```

> The result from the above endpoint looks like this:

```json
{
  "validators": [
    {
      "operator_address": "twilightvaloper1...",
      "jailed": false,
      "status": "BOND_STATUS_BONDED",
      "tokens": "1000000000",
      "description": {
        "moniker": "Validator Name",
        "identity": "",
        "website": "",
        "details": ""
      },
      "commission": {
        "commission_rates": {
          "rate": "0.100000000000000000",
          "max_rate": "0.200000000000000000",
          "max_change_rate": "0.010000000000000000"
        }
      }
    }
  ]
}
```

**Cache:** 10 minutes

---

### Get Validator Details

**Description**
Returns a single validator's details.

**HTTP Method**
`GET`

**Path**
`/api/validators/{address}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `address` | string | Yes | Validator operator address (`twilightvaloper1...`) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/validators/twilightvaloper1..."
```

**Cache:** 10 minutes

---

### Get Validator Block Production

**Description**
Returns block production statistics for a validator.

**HTTP Method**
`GET`

**Path**
`/api/validators/{address}/blocks`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `address` | string | Yes | Validator operator address |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/validators/twilightvaloper1.../blocks"
```

> The result from the above endpoint looks like this:

```json
{
  "totalBlocks": 5432,
  "blocks24h": 123,
  "blocks7d": 847,
  "percentage": 5.43,
  "lastBlock": {
    "height": 428041,
    "hash": "A1B2C3...",
    "timestamp": "2024-02-26T10:00:00.000Z"
  }
}
```

**Cache:** 30 seconds

---

### Get Validator Count

**Description**
Returns the count of validators for a given status.

**HTTP Method**
`GET`

**Path**
`/api/validators/count`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `status` | string | No | Bond status (default: `BOND_STATUS_BONDED`) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/validators/count"
```

> The result from the above endpoint looks like this:

```json
{
  "count": 42
}
```

**Cache:** 10 minutes

---

## Statistics

### Chain Overview

**Description**
Returns overall chain statistics.

**HTTP Method**
`GET`

**Path**
`/api/stats`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/stats"
```

> The result from the above endpoint looks like this:

```json
{
  "latestBlock": {
    "height": 428041,
    "hash": "A1B2C3...",
    "timestamp": "2024-02-26T10:00:00.000Z"
  },
  "totalBlocks": 428041,
  "totalTransactions": 654321,
  "totalAccounts": 5432,
  "transactionsLast24h": 1234,
  "transactionsByStatus": {
    "success": 1200,
    "failed": 34
  }
}
```

**Cache:** 30 seconds

---

### Module Statistics

**Description**
Returns per-module statistics for bridge, forks, volt, and zkos.

**HTTP Method**
`GET`

**Path**
`/api/stats/modules`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/stats/modules"
```

> The result from the above endpoint looks like this:

```json
{
  "bridge": {
    "deposits": 1234,
    "withdrawals": 567,
    "depositVolume": "1234567890",
    "withdrawalVolume": "567890123"
  },
  "forks": {
    "delegateKeys": 42
  },
  "volt": {
    "fragments": 123,
    "activeFragments": 98
  },
  "zkos": {
    "transfers": 5432,
    "mintBurns": 234,
    "volume": "12345678900"
  }
}
```

**Cache:** 60 seconds

---

### Network Performance

**Description**
Returns network performance metrics including block time, TPS, and gas utilization.

**HTTP Method**
`GET`

**Path**
`/api/stats/network-performance`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/stats/network-performance"
```

> The result from the above endpoint looks like this:

```json
{
  "averageBlockTime": 5.42,
  "tps": 0.89,
  "blockProductionRate": 234,
  "gasUtilization": 65,
  "proposerDistribution": [
    {
      "address": "base64address",
      "blocks": 1234,
      "percentage": 5.43
    }
  ]
}
```

**Cache:** 30 seconds

---

### Bridge Analytics

**Description**
Returns BTC bridge deposit/withdrawal analytics.

**HTTP Method**
`GET`

**Path**
`/api/stats/bridge-analytics`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/stats/bridge-analytics"
```

> The result from the above endpoint looks like this:

```json
{
  "totalVolume": "12345678900",
  "depositVolume24h": "1234567",
  "withdrawalVolume24h": "567890",
  "pendingWithdrawals": 23,
  "confirmedWithdrawals": 1234,
  "averageDepositSize": "1000000",
  "averageWithdrawalSize": "500000",
  "withdrawalSuccessRate": 98.15
}
```

**Cache:** 30 seconds

---

### Active Accounts

**Description**
Returns active account metrics over various time windows.

**HTTP Method**
`GET`

**Path**
`/api/stats/active-accounts`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/stats/active-accounts"
```

> The result from the above endpoint looks like this:

```json
{
  "active24h": 1234,
  "active7d": 5432,
  "active30d": 12345,
  "newAccounts24h": 123,
  "growthRate": 2.34
}
```

**Cache:** 30 seconds

---

### Fragment Health

**Description**
Returns fragment health and signer statistics.

**HTTP Method**
`GET`

**Path**
`/api/stats/fragment-health`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/stats/fragment-health"
```

> The result from the above endpoint looks like this:

```json
{
  "totalFragments": 123,
  "activeFragments": 98,
  "bootstrappingFragments": 15,
  "inactiveFragments": 10,
  "averageSignersPerFragment": 12.5,
  "totalSigners": 1234,
  "fragmentSuccessRate": 79.67
}
```

**Cache:** 30 seconds

---

### Block Chart Data

**Description**
Returns daily aggregated block and transaction data for charting.

**HTTP Method**
`GET`

**Path**
`/api/stats/charts/blocks`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `days` | integer | No | Number of days (default: 7) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/stats/charts/blocks?days=7"
```

> The result from the above endpoint looks like this:

```json
[
  {
    "date": "2024-02-20",
    "blocks": 1234,
    "transactions": 5432,
    "gasUsed": "125000000"
  }
]
```

---

### Transaction Chart Data

**Description**
Returns daily transaction data by status and module.

**HTTP Method**
`GET`

**Path**
`/api/stats/charts/transactions`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `days` | integer | No | Number of days (default: 7) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/stats/charts/transactions?days=7"
```

> The result from the above endpoint looks like this:

```json
[
  {
    "date": "2024-02-20",
    "total": 5432,
    "success": 5398,
    "failed": 34,
    "byModule": {
      "bridge": 1234,
      "forks": 234,
      "volt": 567,
      "zkos": 3397
    }
  }
]
```

---

## BTC Deposits

### List BTC Deposits

**Description**
Returns paginated BTC deposits with optional filtering.

**HTTP Method**
`GET`

**Path**
`/api/twilight/deposits`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |
| `address` | string | No | Filter by Twilight deposit address |
| `reserveAddress` | string | No | Filter by reserve address |
| `search` | string | No | Search by address or reserve |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/deposits?page=1&limit=20"
```

> The result from the above endpoint looks like this:

```json
{
  "data": [
    {
      "id": 1,
      "txHash": "A1B2C3...",
      "blockHeight": 428041,
      "reserveAddress": "bc1q...",
      "depositAmount": "1000000",
      "btcHeight": "878000",
      "btcHash": "00000000...",
      "twilightDepositAddress": "twilight1...",
      "oracleAddress": "twilight1...",
      "votes": 5,
      "confirmed": true,
      "createdAt": "2024-02-26T10:00:00.000Z"
    }
  ],
  "pagination": {}
}
```

---

### Get BTC Deposit by ID

**Description**
Returns a single BTC deposit by ID.

**HTTP Method**
`GET`

**Path**
`/api/twilight/deposits/{id}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `id` | integer | Yes | Deposit ID |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/deposits/1"
```

---

## BTC Withdrawals

### List BTC Withdrawals

**Description**
Returns paginated BTC withdrawals with optional filtering.

**HTTP Method**
`GET`

**Path**
`/api/twilight/withdrawals`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |
| `confirmed` | string | No | `true` or `false` |
| `address` | string | No | Filter by Twilight address |
| `withdrawAddress` | string | No | Filter by BTC withdraw address |
| `search` | string | No | Search term |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/withdrawals?confirmed=true&page=1&limit=20"
```

> The result from the above endpoint looks like this:

```json
{
  "data": [
    {
      "id": 1,
      "withdrawIdentifier": 123,
      "twilightAddress": "twilight1...",
      "withdrawAddress": "bc1q...",
      "withdrawReserveId": "1",
      "blockHeight": 428041,
      "withdrawAmount": "1000000",
      "isConfirmed": true,
      "createdAt": "2024-02-26T10:00:00.000Z"
    }
  ],
  "pagination": {}
}
```

---

### Get BTC Withdrawal by ID

**Description**
Returns a single BTC withdrawal by ID.

**HTTP Method**
`GET`

**Path**
`/api/twilight/withdrawals/{id}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `id` | integer | Yes | Withdrawal ID |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/withdrawals/1"
```

---

## Reserves

### List Reserves

**Description**
Returns all BTC reserves.

**HTTP Method**
`GET`

**Path**
`/api/twilight/reserves`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/reserves"
```

> The result from the above endpoint looks like this:

```json
[
  {
    "id": "1",
    "btcRelayCapacityValue": "1000000",
    "totalValue": "5000000",
    "privatePoolValue": "3000000",
    "publicValue": "2000000",
    "feePool": "500000",
    "unlockHeight": "878000",
    "roundId": "1"
  }
]
```

---

### Get Reserve by ID

**Description**
Returns a single reserve by ID.

**HTTP Method**
`GET`

**Path**
`/api/twilight/reserves/{id}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `id` | string | Yes | Reserve ID |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/reserves/1"
```

---

## Fragments

### List Live Fragments

**Description**
Returns current fragment state from LCD with signers.

**HTTP Method**
`GET`

**Path**
`/api/twilight/fragments/live`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/fragments/live"
```

> The result from the above endpoint looks like this:

```json
{
  "data": [
    {
      "id": "1",
      "status": "active",
      "judgeAddress": "twilight1...",
      "threshold": 10,
      "signerApplicationFee": "1000",
      "feePool": "10000",
      "feeBips": 100,
      "signers": [
        {
          "fragmentId": "1",
          "signerAddress": "twilight1...",
          "status": "active",
          "btcPubKey": "02abc...",
          "applicationFee": "1000"
        }
      ],
      "signersCount": 1
    }
  ],
  "total": 123
}
```

**Cache:** 10 minutes

---

### Get Live Fragment by ID

**Description**
Returns a single live fragment from LCD.

**HTTP Method**
`GET`

**Path**
`/api/twilight/fragments/live/{id}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `id` | string | Yes | Fragment ID |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/fragments/live/1"
```

**Cache:** 10 minutes

---

### List Fragments (Database)

**Description**
Returns fragments from the indexed database with pagination.

**HTTP Method**
`GET`

**Path**
`/api/twilight/fragments`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/fragments?page=1&limit=20"
```

---

### List Fragment Signers

**Description**
Returns fragment signers with optional filtering by fragment ID.

**HTTP Method**
`GET`

**Path**
`/api/twilight/fragment-signers`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |
| `fragmentId` | string | No | Filter by fragment ID |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/fragment-signers?fragmentId=1"
```

---

## zkOS Transfers

### List zkOS Transfers

**Description**
Returns paginated zkOS transfer transactions.

**HTTP Method**
`GET`

**Path**
`/api/twilight/zkos/transfers`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/zkos/transfers?page=1&limit=20"
```

> The result from the above endpoint looks like this:

```json
{
  "data": [
    {
      "zkTxId": "abc123...",
      "txHash": "A1B2C3...",
      "blockHeight": 428041,
      "programType": "Transfer",
      "decodedData": {},
      "txFee": "1000"
    }
  ],
  "pagination": {}
}
```

---

### Get zkOS Transfer by ID

**Description**
Returns a single zkOS transfer by transaction ID.

**HTTP Method**
`GET`

**Path**
`/api/twilight/zkos/transfers/{txId}`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `txId` | string | Yes | zkOS transaction ID |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/zkos/transfers/abc123..."
```

---

## zkOS Mint/Burn

### List Mint/Burn Operations

**Description**
Returns paginated zkOS mint and burn operations.

**HTTP Method**
`GET`

**Path**
`/api/twilight/zkos/mint-burns`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `page` | integer | No | Page number (default: 1) |
| `limit` | integer | No | Results per page (default: 20, max: 100) |
| `type` | string | No | `mint` or `burn` |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/zkos/mint-burns?type=mint&page=1&limit=20"
```

> The result from the above endpoint looks like this:

```json
{
  "data": [
    {
      "txHash": "A1B2C3...",
      "blockHeight": 428041,
      "twilightAddress": "twilight1...",
      "mintOrBurn": true,
      "btcValue": "1000000"
    }
  ],
  "pagination": {}
}
```

---

## Bitcoin Info

### Get Bitcoin Node Info

**Description**
Returns Bitcoin node block height and fee estimates.

**HTTP Method**
`GET`

**Path**
`/api/bitcoin/info`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/bitcoin/info"
```

> The result from the above endpoint looks like this:

```json
{
  "blockHeight": 878000,
  "feeEstimate": {
    "satPerVbyte": 12,
    "btcPerKb": 0.00012,
    "targetBlocks": 6
  }
}
```

**Cache:** 30 seconds

---

### Get Bitcoin Address Balance

**Description**
Returns the balance of a Bitcoin address via mempool.space API.

**HTTP Method**
`GET`

**Path**
`/api/bitcoin/address/{address}/balance`

**Path Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `address` | string | Yes | Bitcoin address (bc1, 1, 3, or tb1 prefix) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/bitcoin/address/bc1q.../balance"
```

> The result from the above endpoint looks like this:

```json
{
  "address": "bc1q...",
  "balanceSats": 1234567,
  "txCount": 42
}
```

**Response Fields**

| Field | Type | Description |
|---|---:|---|
| `address` | string | Bitcoin address |
| `balanceSats` | integer | Balance in satoshis (confirmed + mempool) |
| `txCount` | integer | Total transaction count |

**Cache:** 12 hours

---

## Search

### Global Search

**Description**
Searches across blocks, transactions, accounts, and deposits. Auto-detects query type.

**HTTP Method**
`GET`

**Path**
`/api/twilight/search`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `q` | string | Yes | Search query (min 3 chars). Accepts block height, tx hash, or address |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/search?q=twilight1abc..."
```

> The result from the above endpoint looks like this:

```json
{
  "block": null,
  "transaction": null,
  "account": {
    "address": "twilight1abc...",
    "balance": "1000000",
    "txCount": 42
  },
  "deposits": []
}
```

---

## Delegate Keys

### List Delegate Keys

**Description**
Returns all validator delegate key mappings (validator to oracle addresses).

**HTTP Method**
`GET`

**Path**
`/api/twilight/delegates`

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/delegates"
```

> The result from the above endpoint looks like this:

```json
[
  {
    "validatorAddress": "twilightvaloper1...",
    "btcOracleAddress": "twilight1...",
    "btcPublicKey": "02abc...",
    "zkOracleAddress": "twilight1..."
  }
]
```

---

## Sweep Addresses

### List Sweep Addresses

**Description**
Returns proposed BTC sweep addresses from LCD.

**HTTP Method**
`GET`

**Path**
`/api/twilight/sweep-addresses`

**Query Parameters**

| Parameter | Type | Required | Description |
|---|---:|:---:|---|
| `limit` | integer | No | Max results (default: 100, max: 200) |

**Example**

```shell
curl -X GET "https://indexer.twilight.org/api/twilight/sweep-addresses?limit=100"
```

**Cache:** 10 minutes

---

## WebSocket API

### Connection

**URL**
`wss://indexer.twilight.org/ws`

**Local**
`ws://localhost:3001/ws`

Clients subscribe to all channels by default. Server pings every 30 seconds; unresponsive clients are terminated.

---

### Channels

| Channel | Description |
|---|---|
| `twilight:block:new` | New blocks |
| `twilight:tx:new` | New transactions |
| `twilight:deposit:new` | New BTC deposits |
| `twilight:withdrawal:new` | New BTC withdrawals |

---

### Client Messages

**Subscribe to a channel**

```json
{ "action": "subscribe", "channel": "twilight:block:new" }
```

**Unsubscribe from a channel**

```json
{ "action": "unsubscribe", "channel": "twilight:block:new" }
```

**Subscribe to all channels**

```json
{ "action": "subscribe_all" }
```

**Unsubscribe from all channels**

```json
{ "action": "unsubscribe_all" }
```

**Ping**

```json
{ "action": "ping" }
```

---

### Server Messages

**Connection confirmation**

> The server sends a connection message on connect:

```json
{
  "type": "connected",
  "message": "Connected to Twilight Explorer WebSocket",
  "channels": ["blocks", "transactions", "deposits", "withdrawals"]
}
```

**New block event**

> When a new block is indexed:

```json
{
  "type": "block",
  "data": {
    "height": 428042,
    "hash": "A1B2C3...",
    "timestamp": "2024-02-26T10:00:05.000Z",
    "txCount": 3
  },
  "timestamp": "2024-02-26T10:00:05.000Z"
}
```

**Pong response**

```json
{
  "type": "pong",
  "timestamp": 1708942800000
}
```

---

## HTTP Status Codes

| Code | Meaning |
|---:|---|
| `200` | Success |
| `400` | Invalid request parameters |
| `404` | Resource not found |
| `429` | Rate limit exceeded |
| `500` | Internal server error |
| `503` | Service unavailable (health check failed) |
