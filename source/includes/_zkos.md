# ZkOS RPC API 

## Overview

Welcome to the ZkoS API!  
You can use this API to access ZkoS server endpoints that expose the UTXO-based account state and individual encrypted account details tracked by the node.

These endpoints are primarily used by indexers, explorers, and frontends to track balances, commitments, and UTXO outputs associated with shielded accounts.

**Base URL:** `https://nykschain.twilight.rest/zkos/`

> 🔐 No authentication required.

---

## Use Cases

- Retrieve encrypted account state for frontend wallets
- Index UTXO data for explorers
- Monitor nullifier sets to prevent double-spending
- Get UTXO statistics for ZkoS metrics/monitoring

---

## Table of Contents
1. [Transaction Operations](#transaction-operations)
2. [UTXO Queries](#utxo-queries)
3. [Output Queries](#output-queries)
4. [Database Queries](#database-queries)
5. [Test Commands](#test-commands)

---

## Transaction Operations

### Submit Transaction

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "txCommit",
  "params": ["your_hex_encoded_transaction_here"],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
# Transfer/Script Transaction
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "txCommit",
    "params": ["your_hex_encoded_transaction_here"],
    "id": 1
  }'

# Message Transaction (Burn) - requires twilight address as second parameter
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "txCommit",
    "params": ["your_hex_encoded_message_transaction_here", "twilight_address_here"],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "tx_hash": "A1B2C3D4E5F6789012345678901234567890ABCDEF",
    "status": "success"
  },
  "id": 1
}
```

**Description:** Commits a transaction to the blockchain with cryptographic verification and UTXO state updates.

**Use Cases:**

- Submit zero-knowledge proof transactions for privacy-preserving transfers
- Execute smart contract operations on the zkOS blockchain
- Burn tokens with message transactions for cross-chain operations
- Batch transaction processing for improved efficiency
- Integration with wallet applications for transaction broadcasting

### HTTP Method

`POST`

### RPC Method

`txCommit`

### Message Parameters

| Params | Data_Type | Required | Values                                                                     |
| ------ | --------- | -------- | -------------------------------------------------------------------------- |
| [0]    | string    | Yes      | Hex-encoded transaction data                                               |
| [1]    | string    | No       | Twilight address (required only for message transactions)                  |

### Response Fields

| Field   | Data_Type | Description                                    |
| ------- | --------- | ---------------------------------------------- |
| tx_hash | string    | Transaction hash for blockchain confirmation   |
| status  | string    | Transaction status ("success" or "failed")     |

---

## UTXO Queries

### Get UTXOs by Address

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "getUtxos",
  "params": ["hex_encoded_zkos_account_address"],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getUtxos",
    "params": ["hex_encoded_zkos_account_address"],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "utxo_key": "FILL IT HERE",
      "block_height": 12345
    }
  ],
  "id": 1
}
```

**Description:** Retrieves coin-type UTXOs for a specific address, showing transaction history.

**Use Cases:**

- Wallet balance calculation and display for user interfaces
- Transaction history reconstruction for account management
- UTXO selection for optimal transaction fee calculation


### HTTP Method

`POST`

### RPC Method

`getUtxos`

### Message Parameters

| Params | Data_Type | Required | Values                                          |
| ------ | --------- | -------- | ----------------------------------------------- |
| [0]    | string    | Yes      | Hex-encoded address to query UTXOs for          |

### Response Fields

| Field        | Data_Type | Description                                       |
| ------------ | --------- | ------------------------------------------------- |
| utxo_key     | string    | Unique identifier for the UTXO                    |
| block_height | integer   | Block height when UTXO was created               |

---

### Get Memo UTXOs by Address

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "getMemoUtxos",
  "params": ["hex_encoded_zkos_account_address"],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getMemoUtxos",
    "params": ["hex_encoded_zkos_account_address"],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "utxo_key": "FILL IT HERE",
      "memo_data": "encrypted_memo_content",
      "block_height": 12346
    }
  ],
  "id": 1
}
```

**Description:** Retrieves memo-type UTXOs containing encrypted message data for a specific address.

**Use Cases:**

- Encrypted messaging and communication through blockchain transactions
- Transaction metadata storage for compliance and audit purposes
- Smart contract event logging and state tracking

### HTTP Method

`POST`

### RPC Method

`getMemoUtxos`

### Message Parameters

| Params | Data_Type | Required | Values                                             |
| ------ | --------- | -------- | -------------------------------------------------- |
| [0]    | string    | Yes      | Hex-encoded address to query memo UTXOs for        |

### Response Fields

| Field        | Data_Type | Description                                       |
| ------------ | --------- | ------------------------------------------------- |
| utxo_key     | string    | Unique identifier for the memo UTXO               |
| memo_data    | string    | Encrypted memo content                            |
| block_height | integer   | Block height when memo UTXO was created          |

---

### Get State UTXOs by Address

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "getStateUtxos",
  "params": ["hex_encoded_zkos_account_address"],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getStateUtxos",
    "params": ["hex_encoded_zkos_account_address"],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "utxo_key": "FILL IT HERE",
      "state_data": "encrypted_state_content",
      "block_height": 12347
    }
  ],
  "id": 1
}
```

**Description:** Retrieves state-type UTXOs containing smart contract state data for a specific address.

**Use Cases:**

- Smart contract state management and persistence
- Decentralized application state synchronization
- Privacy-preserving computation result storage

### HTTP Method

`POST`

### RPC Method

`getStateUtxos`

### Message Parameters

| Params | Data_Type | Required | Values                                             |
| ------ | --------- | -------- | -------------------------------------------------- |
| [0]    | string    | Yes      | Hex-encoded address to query state UTXOs for       |

### Response Fields

| Field        | Data_Type | Description                                       |
| ------------ | --------- | ------------------------------------------------- |
| utxo_key     | string    | Unique identifier for the state UTXO              |
| state_data   | string    | Encrypted state content                           |
| block_height | integer   | Block height when state UTXO was created         |

---

### Get All Coin UTXOs

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "allUtxos",
  "params": [],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "allUtxos",
    "params": [],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "utxo_key": "FILL IT HERE",
      "block_height": 12348
    }
  ],
  "id": 1
}
```

**Description:** Retrieves all coin-type UTXOs in the system for network analysis and monitoring.

**Use Cases:**

- Network statistics and total supply calculation
- Blockchain explorer and indexer data collection
- Network health monitoring and analysis

### HTTP Method

`POST`

### RPC Method

`allUtxos`

### Message Parameters

| Params | Data_Type | Required | Values                 |
| ------ | --------- | -------- | ---------------------- |
| N/A    | null      | No       | No parameters required |

### Response Fields

| Field        | Data_Type | Description                                       |
| ------------ | --------- | ------------------------------------------------- |
| utxo_key     | string    | Unique identifier for the UTXO                    |
| block_height | integer   | Block height when UTXO was created               |

---

### Get All Memo UTXOs

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "allMemoUtxos",
  "params": [],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "allMemoUtxos",
    "params": [],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "utxo_key": "FILL IT HERE",
      "memo_data": "encrypted_memo_content",
      "block_height": 12349
    }
  ],
  "id": 1
}
```

**Description:** Retrieves all memo-type UTXOs in the system for comprehensive message data analysis.

**Use Cases:**

- System-wide message and communication analysis
- Network-level memo data indexing and search
- Compliance monitoring for encrypted communications

### HTTP Method

`POST`

### RPC Method

`allMemoUtxos`

### Message Parameters

| Params | Data_Type | Required | Values                 |
| ------ | --------- | -------- | ---------------------- |
| N/A    | null      | No       | No parameters required |

### Response Fields

| Field        | Data_Type | Description                                       |
| ------------ | --------- | ------------------------------------------------- |
| utxo_key     | string    | Unique identifier for the memo UTXO               |
| memo_data    | string    | Encrypted memo content                            |
| block_height | integer   | Block height when memo UTXO was created          |

---

### Get All State UTXOs

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "allStateUtxos",
  "params": [],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "allStateUtxos",
    "params": [],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "utxo_key": "FILL IT HERE",
      "state_data": "encrypted_state_content",
      "block_height": 12350
    }
  ],
  "id": 1
}
```

**Description:** Retrieves all state-type UTXOs in the system for comprehensive smart contract state analysis.

**Use Cases:**

- System-wide smart contract state monitoring
- Network-level state data indexing and analytics
- Decentralized application ecosystem analysis

### HTTP Method

`POST`

### RPC Method

`allStateUtxos`

### Message Parameters

| Params | Data_Type | Required | Values                 |
| ------ | --------- | -------- | ---------------------- |
| N/A    | null      | No       | No parameters required |

### Response Fields

| Field        | Data_Type | Description                                       |
| ------------ | --------- | ------------------------------------------------- |
| utxo_key     | string    | Unique identifier for the state UTXO              |
| state_data   | string    | Encrypted state content                           |
| block_height | integer   | Block height when state UTXO was created         |

---

## Output Queries

### Get All Coin Outputs

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "allOutputs",
  "params": [],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "allOutputs",
    "params": [],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "output_key": "FILL IT HERE",
      "value": "1000000",
      "block_height": 12351
    }
  ],
  "id": 1
}
```

**Description:** Retrieves all coin-type outputs in the system for transaction output analysis.

**Use Cases:**

- Transaction output tracking and verification
- Network-wide output analysis and statistics
- Blockchain explorer transaction detail display

### HTTP Method

`POST`

### RPC Method

`allOutputs`

### Message Parameters

| Params | Data_Type | Required | Values                 |
| ------ | --------- | -------- | ---------------------- |
| N/A    | null      | No       | No parameters required |

### Response Fields

| Field        | Data_Type | Description                                       |
| ------------ | --------- | ------------------------------------------------- |
| output_key   | string    | Unique identifier for the output                  |
| value        | string    | Output value in base currency units               |
| block_height | integer   | Block height when output was created              |

---

### Get Specific Coin Output

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "getOutput",
  "params": ["hex_encoded_utxo_key"],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getOutput",
    "params": ["hex_encoded_utxo_key"],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "output_key": "FILL IT HERE",
    "value": "1000000",
    "block_height": 12352,
    "transaction_hash": "FILL IT HERE"
  },
  "id": 1
}
```

**Description:** Retrieves a specific coin output by its UTXO key for detailed transaction analysis.

**Use Cases:**

- Individual output verification and validation
- Transaction detail lookup for wallet applications
- Output tracing for compliance and audit purposes

### HTTP Method

`POST`

### RPC Method

`getOutput`

### Message Parameters

| Params | Data_Type | Required | Values                                  |
| ------ | --------- | -------- | --------------------------------------- |
| [0]    | string    | Yes      | Hex-encoded UTXO key to query output for |

### Response Fields

| Field            | Data_Type | Description                                       |
| ---------------- | --------- | ------------------------------------------------- |
| output_key       | string    | Unique identifier for the output                  |
| value            | string    | Output value in base currency units               |
| block_height     | integer   | Block height when output was created              |
| transaction_hash | string    | Transaction hash containing this output           |

---

### Get Specific Memo Output

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "getMemoOutput",
  "params": ["hex_encoded_utxo_key"],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getMemoOutput",
    "params": ["hex_encoded_utxo_key"],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "output_key": "FILL IT HERE",
    "memo_data": "encrypted_memo_content",
    "block_height": 12353,
    "transaction_hash": "FILL IT HERE"
  },
  "id": 1
}
```

**Description:** Retrieves a specific memo output by its UTXO key for detailed message analysis.

**Use Cases:**

- Individual memo verification and decryption
- Message detail lookup for communication applications
- Memo tracing for compliance and audit purposes

### HTTP Method

`POST`

### RPC Method

`getMemoOutput`

### Message Parameters

| Params | Data_Type | Required | Values                                       |
| ------ | --------- | -------- | -------------------------------------------- |
| [0]    | string    | Yes      | Hex-encoded UTXO key to query memo output for |

### Response Fields

| Field            | Data_Type | Description                                       |
| ---------------- | --------- | ------------------------------------------------- |
| output_key       | string    | Unique identifier for the memo output             |
| memo_data        | string    | Encrypted memo content                            |
| block_height     | integer   | Block height when memo output was created         |
| transaction_hash | string    | Transaction hash containing this memo output      |

---

### Get Specific State Output

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "getStateOutput",
  "params": ["hex_encoded_utxo_key"],
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getStateOutput",
    "params": ["hex_encoded_utxo_key"],
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "output_key": "FILL IT HERE",
    "state_data": "encrypted_state_content",
    "block_height": 12354,
    "transaction_hash": "FILL IT HERE"
  },
  "id": 1
}
```

**Description:** Retrieves a specific state output by its UTXO key for detailed smart contract state analysis.

**Use Cases:**

- Individual state verification and smart contract debugging
- State detail lookup for decentralized applications
- State tracing for compliance and audit purposes

### HTTP Method

`POST`

### RPC Method

`getStateOutput`

### Message Parameters

| Params | Data_Type | Required | Values                                        |
| ------ | --------- | -------- | --------------------------------------------- |
| [0]    | string    | Yes      | Hex-encoded UTXO key to query state output for |

### Response Fields

| Field            | Data_Type | Description                                       |
| ---------------- | --------- | ------------------------------------------------- |
| output_key       | string    | Unique identifier for the state output            |
| state_data       | string    | Encrypted state content                           |
| block_height     | integer   | Block height when state output was created        |
| transaction_hash | string    | Transaction hash containing this state output     |

---

## Database Queries

### Query UTXOs from Database

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

// Query coin UTXOs
var raw = JSON.stringify({
  "jsonrpc": "2.0",
  "method": "getUtxosFromDB",
  "params": {
    "start_block": 0,
    "end_block": 1000,
    "limit": 100,
    "pagination": 0,
    "io_type": "Coin"
  },
  "id": 1
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("https://nykschain.twilight.rest/zkos/", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

```bash
# Query coin UTXOs
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getUtxosFromDB",
    "params": {
      "start_block": 0,
      "end_block": 1000,
      "limit": 100,
      "pagination": 0,
      "io_type": "Coin"
    },
    "id": 1
  }'

# Query memo UTXOs
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getUtxosFromDB",
    "params": {
      "start_block": 0,
      "end_block": 1000,
      "limit": 50,
      "pagination": 0,
      "io_type": "Memo"
    },
    "id": 1
  }'

# Query state UTXOs
curl -X POST https://nykschain.twilight.rest/zkos/ \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getUtxosFromDB",
    "params": {
      "start_block": 0,
      "end_block": 1000,
      "limit": 75,
      "pagination": 0,
      "io_type": "State"
    },
    "id": 1
  }'
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "utxo_key": "FILL IT HERE",
      "block_height": 12355,
      "io_type": "Coin",
      "data": "encrypted_utxo_data"
    }
  ],
  "id": 1
}
```

**Description:** Queries UTXOs from the PostgreSQL database with specific filtering parameters including block range, pagination, and UTXO type.

**Use Cases:**

- Advanced UTXO filtering and search operations
- Historical data analysis within specific block ranges
- Paginated UTXO retrieval for large datasets
- Database-level UTXO management and analytics

### HTTP Method

`POST`

### RPC Method

`getUtxosFromDB`

### Message Parameters

| Params     | Data_Type | Required | Values                                    |
| ---------- | --------- | -------- | ----------------------------------------- |
| start_block| integer   | Yes      | Starting block height (i128)             |
| end_block  | integer   | Yes      | Ending block height (i128)               |
| limit      | integer   | Yes      | Maximum number of results (max: 10,000)  |
| pagination | integer   | Yes      | Pagination offset for result sets        |
| io_type    | string    | Yes      | Type of UTXO ("Coin", "Memo", or "State")|

### Response Fields

| Field        | Data_Type | Description                                       |
| ------------ | --------- | ------------------------------------------------- |
| utxo_key     | string    | Unique identifier for the UTXO                    |
| block_height | integer   | Block height when UTXO was created               |
| io_type      | string    | Type of UTXO (Coin, Memo, or State)              |
| data         | string    | Encrypted UTXO content based on type             |

---

## Notes

1. All hex strings should be valid hexadecimal without the "0x" prefix
2. Some endpoints return empty results with error messages if no data is found
3. Transaction verification is performed before committing transactions

