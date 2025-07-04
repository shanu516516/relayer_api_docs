# ZkOS RPC API 

Welcome to the ZkoS API!
You can utilize our API to access ZkoS endpoints, which provide information about the UTXO state maintained by the ZkoS server and expose account-specific data stored in the zero-knowledge UTXO set.

**Endpoint URL**

`API_ENDPOINT_PRODUCTION = https://nykschain.twilight.rest/zkos/`

`API_ENDPOINT_STAGING = http://localhost:3030`

## Table of Contents
1. [Transaction Operations](#transaction-operations)
2. [UTXO Queries](#utxo-queries)
3. [Output Queries](#output-queries)
4. [Database Queries](#database-queries)
5. [Test Commands](#test-commands)

---

## Transaction Operations

### 1. txCommit - Submit a Transaction

Commits a transaction to the blockchain. Requires a hex-encoded transaction and optionally a twilight address for message transactions.

```bash
# Transfer/Script Transaction
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "txCommit",
    "params": ["your_hex_encoded_transaction_here"],
    "id": 1
  }'

# Message Transaction (Burn) - requires twilight address as second parameter
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "txCommit",
    "params": ["your_hex_encoded_message_transaction_here", "twilight_address_here"],
    "id": 1
  }'
```

---

## UTXO Queries

### 2. getUtxos - Get UTXOs by Address

Returns coin-type UTXOs for a specific address.

```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getUtxos",
    "params": ["hex_encoded_address"],
    "id": 1
  }'
```

### 3. getMemoUtxos - Get Memo UTXOs by Address

Returns memo-type UTXOs for a specific address.

```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getMemoUtxos",
    "params": ["hex_encoded_address"],
    "id": 1
  }'
```

### 4. getStateUtxos - Get State UTXOs by Address

Returns state-type UTXOs for a specific address.

```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getStateUtxos",
    "params": ["hex_encoded_address"],
    "id": 1
  }'
```

### 5. allUtxos - Get All Coin UTXOs

Returns all coin-type UTXOs in the system.

```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "allUtxos",
    "params": [],
    "id": 1
  }'
```

### 6. allMemoUtxos - Get All Memo UTXOs

Returns all memo-type UTXOs in the system.

```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "allMemoUtxos",
    "params": [],
    "id": 1
  }'
```

### 7. allSateUtxos - Get All State UTXOs

Returns all state-type UTXOs in the system.
*Note: There's a typo in the method name - it should be "allStateUtxos"*

```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "allSateUtxos",
    "params": [],
    "id": 1
  }'
```

---

## Output Queries

### 8. allOutputs - Get All Coin Outputs

Returns all coin-type outputs in the system.

```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "allOutputs",
    "params": [],
    "id": 1
  }'
```

### 9. getOutput - Get Specific Coin Output

Returns a specific coin output by UTXO key.

```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getOutput",
    "params": ["hex_encoded_utxo_key"],
    "id": 1
  }'
```

### 10. getMemoOutput - Get Specific Memo Output

Returns a specific memo output by UTXO key.

```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getMemoOutput",
    "params": ["hex_encoded_utxo_key"],
    "id": 1
  }'
```

### 11. getStateOutput - Get Specific State Output

Returns a specific state output by UTXO key.

```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "getStateOutput",
    "params": ["hex_encoded_utxo_key"],
    "id": 1
  }'
```

---

## Database Queries

### 12. getUtxosFromDB - Query UTXOs from Database

Queries UTXOs from the PostgreSQL database with specific parameters.

```bash
curl -X POST http://localhost:3030 \
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
curl -X POST http://localhost:3030 \
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
curl -X POST http://localhost:3030 \
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

**Parameters:**
- `start_block`: Starting block height (i128)
- `end_block`: Ending block height (i128)
- `limit`: Maximum number of results (max: 10000)
- `pagination`: Pagination offset
- `io_type`: Type of UTXO ("Coin", "Memo", or "State")

---

## Test Commands

### 13. TestCommand - Various Test Operations

Executes various test commands for database operations and statistics.

#### Take Snapshot to LevelDB
```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "TestCommand",
    "params": {
      "test_command": "TakeSnapshotintoLevelDB"
    },
    "id": 1
  }'
```

#### Load Backup from LevelDB
```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "TestCommand",
    "params": {
      "test_command": "LoadBackupFromLevelDB"
    },
    "id": 1
  }'
```

#### Take Snapshot to PostgreSQL
```bash
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "TestCommand",
    "params": {
      "test_command": "TakeSnapshotintoPostgreSQL"
    },
    "id": 1
  }'
```

#### Get UTXO Database Length Statistics
```bash
# Get coin UTXO count
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "TestCommand",
    "params": {
      "test_command": "UtxoCoinDbLength"
    },
    "id": 1
  }'

# Get memo UTXO count
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "TestCommand",
    "params": {
      "test_command": "UtxoMemoDbLength"
    },
    "id": 1
  }'

# Get state UTXO count
curl -X POST http://localhost:3030 \
  -H "Content-Type: application/json" \
  -d '{
    "jsonrpc": "2.0",
    "method": "TestCommand",
    "params": {
      "test_command": "UtxoStateDbLength"
    },
    "id": 1
  }'
```

**Available Test Commands:**
- `UtxoCoinDbLength` - Get coin UTXO database length
- `UtxoMemoDbLength` - Get memo UTXO database length  
- `UtxoStateDbLength` - Get state UTXO database length
- `TakeSnapshotintoLevelDB` - Take snapshot into LevelDB
- `TakeSnapshotintoPostgreSQL` - Take snapshot into PostgreSQL
- `LoadBackupFromLevelDB` - Load backup from LevelDB

---

## Error Response Format

All endpoints return errors in the standard JSON-RPC error format:

```json
{
  "jsonrpc": "2.0",
  "error": {
    "code": -32602,
    "message": "Invalid params",
    "data": "Error description here"
  },
  "id": 1
}
```

## Success Response Format

Successful responses follow the JSON-RPC format:

```json
{
  "jsonrpc": "2.0",
  "result": {
    // Response data here
  },
  "id": 1
}
```

## Notes

1. All hex strings should be valid hexadecimal without the "0x" prefix
2. The server runs on port 3030 by default
3. Some endpoints return empty results with error messages if no data is found
4. The `getUtxosFromDB` endpoint has a maximum limit of 10,000 results
5. Transaction verification is performed before committing transactions
6. Message transactions require a twilight address as a second parameter
