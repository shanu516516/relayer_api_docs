# Explorer Indexer

## Overview

The **Twilight Explorer Indexer** is a background service that syncs the Twilight blockchain into PostgreSQL. It processes blocks from the LCD API, decodes 23 custom module message types, and publishes real-time events via Redis pub/sub.

**Key features:**
- PostgreSQL advisory lock prevents concurrent indexer instances
- Block linkage validation detects chain reorganizations
- Async enrichment worker decodes zkOS transactions without blocking sync
- Real-time events via Redis pub/sub for WebSocket delivery

---

## Architecture

```
Twilight Chain (LCD API)
       |
       v
  Sync Loop ──> PostgreSQL <── API Server
       |              ^
       |              |
       +──> Redis ────+──> WebSocket
       |
  Enrichment Worker ──> zkOS Decode API
```

| Component | File | Description |
|---|---|---|
| Sync loop | `src/sync.ts` | Main block processor |
| Enrichment worker | `src/enrichment.ts` | Async zkOS decoder |
| LCD client | `src/lcd-client.ts` | Chain API client |
| Message decoders | `src/decoders/` | 23 message type parsers |
| Entry point | `src/index.ts` | Startup, shutdown, Redis |
| Config | `src/config.ts` | Environment configuration |

---

## Configuration

| Variable | Default | Description |
|---|---|---|
| `DATABASE_URL` | `postgresql://...` | PostgreSQL connection |
| `REDIS_URL` | `redis://localhost:6379` | Redis for pub/sub |
| `TWILIGHT_LCD_URL` | `https://lcd.twilight.org` | Cosmos LCD endpoint |
| `ZKOS_DECODE_URL` | `https://indexer.twilight.org` | zkOS decode API |
| `INDEXER_START_HEIGHT` | `1` | Starting block height |
| `INDEXER_POLL_INTERVAL` | `2000` | Milliseconds between polls |
| `INDEXER_BATCH_SIZE` | `10` | Blocks per batch |
| `CHAIN_ID` | `nyks` | Chain identifier |

---

## Startup Sequence

1. Connect to PostgreSQL (fail if unavailable)
2. Connect to LCD API and verify by fetching latest block
3. Initialize Redis connection (optional, non-blocking)
4. Setup Redis pub/sub event publishing
5. Register SIGINT/SIGTERM signal handlers
6. Start enrichment worker (async, non-blocking)
7. Start sync loop (blocking, main loop)

---

## Advisory Lock

The indexer acquires a PostgreSQL advisory lock (`pg_try_advisory_lock(123456789)`) at startup.

- If the lock cannot be acquired, another indexer is already running — log a warning and exit
- Lock is released on graceful shutdown
- Prevents duplicate writes from concurrent instances
- Running multiple instances is safe — extras exit immediately

---

## Block Processing

### Sync Loop

The sync loop polls the LCD API for new blocks and processes them in batches.

1. Read `lastIndexedHeight` from the `IndexerState` table
2. Fetch latest chain height from LCD
3. If caught up: sleep for `INDEXER_POLL_INTERVAL`, then check again
4. Otherwise: process blocks from `lastIndexed + 1` to `lastIndexed + INDEXER_BATCH_SIZE`
5. On error: wait 5 seconds, then retry

### Per-Block Processing

For each block height:

1. **Fetch** block header from LCD
2. **Validate linkage** — compare `last_block_id.hash` with previous block's hash in DB
   - On mismatch: log error and halt (possible chain reorganization)
   - Skip for genesis block (height 1)
3. **Fetch** all transactions for the block
4. **Decode** each transaction's messages using module decoders
5. **Atomic write** to database:
   - Upsert Block record
   - Create Transaction records
   - Create Event records
   - Update Account activity
   - Update `lastIndexedHeight` in IndexerState
6. **Process custom messages** (separate transaction):
   - Write module-specific records (deposits, withdrawals, fragments, etc.)
7. **Emit events** via EventEmitter → Redis pub/sub

---

## Message Types

The indexer decodes **23 custom Cosmos SDK message types** across 4 modules.

### Bridge Module (17 types)

| Message | Description | DB Table |
|---|---|---|
| `MsgConfirmBtcDeposit` | Oracle confirms BTC deposit | `BtcDeposit` |
| `MsgRegisterBtcDepositAddress` | Register BTC deposit address | `BtcDepositAddress` |
| `MsgRegisterReserveAddress` | Register reserve address | `Reserve` |
| `MsgBootstrapFragment` | Initialize multisig fragment | `Fragment` |
| `MsgWithdrawBtcRequest` | Request BTC withdrawal | `BtcWithdrawal` |
| `MsgConfirmBtcWithdraw` | Confirm withdrawal on BTC chain | — |
| `MsgWithdrawTxSigned` | Validator signs withdrawal | — |
| `MsgWithdrawTxFinal` | Judge submits final signed withdrawal | — |
| `MsgSweepProposal` | Propose reserve sweep | `SweepProposal` |
| `MsgSignSweep` | Sign sweep transaction | `SweepSignature` |
| `MsgSignRefund` | Sign refund transaction | `RefundSignature` |
| `MsgBroadcastTxSweep` | Broadcast signed sweep | — |
| `MsgBroadcastTxRefund` | Broadcast signed refund | — |
| `MsgProposeSweepAddress` | Propose sweep destination | — |
| `MsgProposeRefundHash` | Propose refund hash | — |
| `MsgUnsignedTxSweep` | Propose unsigned sweep tx | — |
| `MsgUnsignedTxRefund` | Propose unsigned refund tx | — |

### Forks Module (2 types)

| Message | Description | DB Table |
|---|---|---|
| `MsgSetDelegateAddresses` | Validator sets oracle delegates | `DelegateKey` |
| `MsgSeenBtcChainTip` | Oracle reports BTC chain tip | `BtcChainTip` |

### Volt Module (2 types)

| Message | Description | DB Table |
|---|---|---|
| `MsgSignerApplication` | Apply to join a fragment | `FragmentSigner` |
| `MsgAcceptSigners` | Judge accepts signer applications | `FragmentSigner` (update) |

### zkOS Module (2 types)

| Message | Description | DB Table |
|---|---|---|
| `MsgTransferTx` | Zero-knowledge transfer | `ZkosTransfer` |
| `MsgMintBurnTradingBtc` | Mint or burn trading BTC | `ZkosMintBurn` |

---

## Enrichment Worker

The enrichment worker runs in the background alongside the sync loop. It decodes zkOS transaction bytecode asynchronously so the main sync loop is never blocked by slow external API calls.

| Setting | Value |
|---|---:|
| Batch size | 20 records |
| Poll interval | 10 seconds |
| Max attempts | 5 per record |

### Workflow

1. Query `ZkosTransfer` records where `decodeStatus = 'pending'` and `decodeAttempts < 5`
2. For each record, POST to the zkOS decode API with `txByteCode`
3. On success: store `decodedData`, `inputs`, `outputs`, `programType`; set `decodeStatus = 'ok'`
4. On failure: increment `decodeAttempts`, store error in `lastDecodeError`
5. After 5 failures: set `decodeStatus = 'failed'`

### Decode Statuses

| Status | Meaning |
|---|---|
| `pending` | Waiting to be decoded (initial state) |
| `ok` | Successfully decoded |
| `failed` | Max attempts reached, requires manual inspection |

### External API

```
POST {ZKOS_DECODE_URL}/api/decode-zkos-transaction
Content-Type: application/json

{ "tx_byte_code": "hex_encoded_bytecode" }
```

Returns decoded transaction with inputs, outputs, witnesses, and fee.

---

## Database Schema

### Core Tables

| Table | Primary Key | Description |
|---|---|---|
| `Block` | `height` | Block headers with proposer, gas, tx count |
| `Transaction` | `id` (auto) | Transactions with decoded messages |
| `Event` | `id` (auto) | Block and transaction events |
| `Account` | `address` | Account activity tracking |
| `IndexerState` | `key` | Sync state (e.g., `lastIndexedHeight`) |

### Bridge Tables

| Table | Unique Constraint | Description |
|---|---|---|
| `BtcDeposit` | `(btcHash, twilightDepositAddress)` | Confirmed BTC deposits |
| `BtcDepositAddress` | `btcDepositAddress` | Registered deposit addresses |
| `BtcWithdrawal` | `withdrawIdentifier` | Withdrawal requests |
| `Reserve` | `id` (BigInt) | BTC reserves |
| `SweepProposal` | — | Reserve sweep proposals |
| `SweepSignature` | `(reserveId, roundId, signerAddress)` | Sweep signatures |
| `RefundSignature` | `(reserveId, roundId, signerAddress)` | Refund signatures |

### Forks Tables

| Table | Unique Constraint | Description |
|---|---|---|
| `DelegateKey` | `validatorAddress` | Validator oracle delegations |
| `BtcChainTip` | `(btcHeight, btcOracleAddress)` | BTC chain observations |

### Volt Tables

| Table | Unique Constraint | Description |
|---|---|---|
| `Fragment` | `id` (BigInt) | Multisig fragments |
| `FragmentSigner` | `(fragmentId, signerAddress)` | Fragment signers |
| `ClearingAccount` | `twilightAddress` | Clearing accounts |

### zkOS Tables

| Table | Unique Constraint | Description |
|---|---|---|
| `ZkosTransfer` | `zkTxId` | Zero-knowledge transfers (with decode status) |
| `ZkosMintBurn` | — | Mint and burn operations |

---

## Redis Pub/Sub

Events are published after each block is processed. The API's WebSocket server subscribes and delivers to connected clients.

| Channel | Payload |
|---|---|
| `twilight:block:new` | Block data |
| `twilight:tx:new` | Transaction data |
| `twilight:deposit:new` | BTC deposit data |
| `twilight:withdrawal:new` | BTC withdrawal data |

All payloads are JSON with BigInt values serialized as strings.

---

## Graceful Shutdown

On `SIGINT` or `SIGTERM`:

1. Stop enrichment worker
2. Stop sync loop (wait for current block to finish)
3. Release PostgreSQL advisory lock
4. Disconnect Prisma
5. Disconnect Redis
6. Exit with code 0

---

## LCD API Endpoints Used

### Block & Transaction

| Method | Endpoint |
|---|---|
| GET | `/cosmos/base/tendermint/v1beta1/blocks/latest` |
| GET | `/cosmos/base/tendermint/v1beta1/blocks/{height}` |
| GET | `/cosmos/tx/v1beta1/txs/block/{height}` |
| GET | `/cosmos/tx/v1beta1/txs/{hash}` |
| GET | `/cosmos/tx/v1beta1/txs?events=tx.height={height}` |

### Module Queries

| Method | Endpoint |
|---|---|
| GET | `/twilightproject/nyks/bridge/registered_btc_deposit_addresses` |
| GET | `/twilightproject/nyks/bridge/withdraw_btc_request_all` |
| GET | `/twilightproject/nyks/forks/delegate_keys_all` |
| GET | `/twilightproject/nyks/volt/btc_reserve` |
| GET | `/twilightproject/nyks/volt/fragments` |
| GET | `/twilightproject/nyks/volt/fragment/{id}` |
| GET | `/twilightproject/nyks/volt/clearing_account/{address}` |
| GET | `/twilightproject/nyks/zkos/transfer_tx/{txId}` |

### Account & Node

| Method | Endpoint |
|---|---|
| GET | `/cosmos/auth/v1beta1/accounts/{address}` |
| GET | `/cosmos/bank/v1beta1/balances/{address}` |
| GET | `/cosmos/base/tendermint/v1beta1/node_info` |
| GET | `/cosmos/base/tendermint/v1beta1/syncing` |
