# Twilight Clinet SDK

## Overview

Welcome to the Twilight Client SDK documentation!  
The Twilight Privacy SDK is a comprehensive Rust library for building privacy-preserving wallet applications on the Twilight blockchain ecosystem. This SDK provides complete client-side functionality for managing zero-knowledge transactions, interacting with Twilight relayers, and handling decentralized trading operations.

**Repository:** `https://github.com/twilight-project/zkos-client-wallet`



## Wallet Operations

### Initialize or Load Wallet

```rust
use twilight_privacy_sdk::keys_management;

// Initialize a new wallet or load an existing one
let password = b"a_16_byte_key!!";      // Must be 16 bytes for AES-128
let iv = b"a_16_byte_iv!!!!";         // Must be 16 bytes for AES-128
let wallet_file = "wallet.bin".to_string();
let seed = "a_secure_seed_string_if_creating_a_new_wallet";

// This will load 'wallet.bin' if it exists, otherwise it will create a new
// one using the provided seed.
let secret_key = keys_management::init_wallet(
    password,
    wallet_file,
    iv,
    Some(seed.to_string()),
).expect("Failed to initialize or load wallet");
```

> The result from the above operation looks like this:

```rust
// Returns the master secret key for the wallet
RistrettoSecretKey { /* ... internal key data ... */ }
```

**Description:** Initializes a new encrypted wallet file or loads an existing one. If the specified file path exists, it attempts to decrypt it with the given password. If not, it creates a new wallet using the provided seed, encrypts it with AES-128-CBC, and saves it to the path. This is the primary function for all wallet setup.

**Use Cases:**

-   Onboarding new users by creating a fresh, encrypted wallet.
-   Restoring a user's session by loading their existing wallet file.
-   Seamlessly handling both new and returning users with a single function.
-   Generating a master secret key from a user-provided seed phrase.
-   Ensuring all private key material is stored encrypted at rest.

### Function

`keys_management::init_wallet`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| password | &[u8] | Yes | Password for wallet encryption/decryption. Must be 16 bytes for AES-128. |
| file_path | String | Yes | File path for wallet storage. |
| iv | &[u8] | Yes | 16-byte initialization vector for AES-128-CBC. |
| key_seed | Option<String> | Yes (if new) | A seed string used to generate the private key if the wallet file does not exist. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Option<RistrettoSecretKey> | The `RistrettoSecretKey` on success, or `None` on failure (e.g., file not found and no seed provided). |

---

### Get Public Key

```rust
use twilight_privacy_sdk::keys_management;
use quisquislib::ristretto::RistrettoSecretKey;

// Assume `secret_key` is the RistrettoSecretKey obtained from init_wallet
let secret_key: RistrettoSecretKey = /* ... */;

// Get the corresponding public key
let public_key = keys_management::get_public_key(
    secret_key,
    "public_key.bin".to_string(),
);
```

> The result from the above operation looks like this:

```rust
// Returns the public key
RistrettoPublicKey { /* ... internal key data ... */ }
```

**Description:** Derives the public key from a given secret key. For efficiency, this function will first attempt to load the public key from the specified file path. If the file doesn't exist, it derives the key from the secret key, saves it to the path for future use, and then returns it.

**Use Cases:**

-   Generating a public address for receiving funds.
-   Verifying signatures without exposing the private key.
-   Sharing a public key with other users or services.
-   Caching the public key on disk to avoid repeated derivations.
-   Creating a view-only wallet functionality.

### Function

`keys_management::get_public_key`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| secret_key | RistrettoSecretKey | Yes | The secret key from which to derive the public key. |
| file_path | String | Yes | The file path to load/store the derived public key. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | RistrettoPublicKey | The corresponding `RistrettoPublicKey`. |

---

## Transfer Operations

### Create Private Transfer

```rust
use twilight_privacy_sdk::transfer;
use quisquislib::ristretto::RistrettoSecretKey;

// Create a zero-knowledge private transfer
let tx_wallet = transfer::create_private_transfer_tx_single(
    secret_key,         // RistrettoSecretKey
    sender_input_json,  // String (JSON of zkvm::Input)
    receiver_address,   // String (Hex address)
    100,                // amount: u64
    false,              // address_input: bool
    900,                // updated_sender_balance: u64
    1,                  // fee: u64
);

let tx_hex = tx_wallet.tx_hex;
```

> The result from the above operation looks like this:

```rust
// Returns a TransferTxWallet struct
TransferTxWallet {
    tx_hex: "0a1b2c3d4e5f...transaction_hex_data",
    encrypt_scalar_hex: "somescalarhex...",
}
```

**Description:** Creates a simple one-to-one zero-knowledge private transfer transaction. This is the standard way to send funds privately from one user to another.

**Use Cases:**

-   Private peer-to-peer value transfers 
-   Confidential business transactions with privacy guarantees
-   Privacy-preserving payment processing for e-commerce applications

### Function

`transfer::create_private_transfer_tx_single`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| sk | RistrettoSecretKey | Yes | Sender's secret key. |
| sender | String | Yes | JSON string of the sender's input UTXO (`zkvm::Input`). |
| reciever | String | Yes | Recipient's address (hex string) or their input UTXO as a JSON string. |
| amount | u64 | Yes | Transfer amount in base units. |
| address_input | bool | Yes | `false` if receiver is an address, `true` if it's an input UTXO. |
| updated_sender_balance | u64 | Yes | The sender's balance after the transfer. |
| fee | u64 | Yes | Transaction fee. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | TransferTxWallet | A struct containing the hex-encoded transaction and the encryption scalar. |

---

### Create Private Transfer with Anonymity Set

```rust
use twilight_privacy_sdk::transfer;
use quisquislib::ristretto::RistrettoSecretKey;
use zkvm::Input;

// Create a private transfer with a larger anonymity set
let tx_hex = transfer::create_quisquis_transaction_single(
    secret_key,         // RistrettoSecretKey
    sender_input,       // zkvm::Input
    receiver_address,   // String (Hex address)
    100,                // amount: u64
    false,              // address_input: bool
    900,                // updated_sender_balance: u64
    anonymity_set_json, // String (JSON of Vec<zkvm::Input>)
    1,                  // fee: u64
);
```

> The result from the above operation looks like this:

```rust
// Returns hex-encoded transaction string
String = "0a1b2c3d4e5f...transaction_hex_data"
```

**Description:** Creates a private transfer that is mixed with a set of other "decoy" inputs, making it much harder to trace the true origin of the funds.

**Use Cases:**

-   High-security transfers requiring maximum privacy and anonymity among parties
-   Avoiding on-chain analysis and heuristics.
-   Enhanced protection against transaction graph analysis.

### Function

`transfer::create_quisquis_transaction_single`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| sk | RistrettoSecretKey | Yes | Sender's secret key. |
| sender_inp | zkvm::Input | Yes | The sender's input UTXO. |
| reciever | String | Yes | Recipient's address (hex string) or their input UTXO as a JSON string. |
| amount | u64 | Yes | Transfer amount in base units. |
| address_input | bool | Yes | `false` if receiver is an address, `true` if it's an input UTXO. |
| updated_sender_balance | u64 | Yes | The sender's balance after the transfer. |
| anonymity_set | String | Yes | JSON string of a `Vec<zkvm::Input>` to be used as decoys. |
| fee | u64 | Yes | Transaction fee. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | String | Hex-encoded transaction data. |

---

### Create Burn Message

```rust
use twilight_privacy_sdk::transfer;
use zkvm::Input;
use quisquislib::ristretto::RistrettoSecretKey;

// Create a burn message to move funds out of the ZK system
let tx_hex = transfer::create_burn_message_transaction(
    input,              // zkvm::Input to burn
    1000,               // amount: u64
    encrypt_scalar_hex, // String
    secret_key,         // RistrettoSecretKey
    init_address,       // String, the destination address on the nyks chain
);
```

> The result from the above operation looks like this:

```rust
// Returns hex-encoded transaction string
String = "0a1b2c3d4e5f...transaction_hex_data"
```

**Description:** Creates a special transaction that "burns" a UTXO on the Twilight chain. This is the first step for moving assets from dark accounts to standard cosmos account using the native Nyks bridge. The burn proof is submitted to the relayer network to initiate the transfer.

**Use Cases:**

-   Bridging dark assets out of the Twilight ecosystem.
-   Withdrawing assets from the privacy layer to a transparent chain.
-   Connecting with broader DeFi ecosystems.

### Function

`transfer::create_burn_message_transaction`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| input | zkvm::Input | Yes | The input UTXO to be burned. |
| amount | u64 | Yes | The amount of funds to burn. |
| ecrypt_scalar_hex | String | Yes | The hex-encoded encryption scalar of the input. |
| sk | RistrettoSecretKey | Yes | The secret key corresponding to the input. |
| init_address | String | Yes | The destination address on the nyks blockchain. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | String | Hex-encoded burn message transaction. |

---

## Trading Operations

### Create Trader Order

```rust
use twilight_privacy_sdk::relayer;
use zkvm::{Input, Output};
use quisquislib::ristretto::RistrettoSecretKey;

// Create a new zero-knowledge trader order
let tx_hex = relayer::create_trader_order_zkos(
    input_coin,         // zkvm::Input
    output_memo,        // zkvm::Output
    secret_key,         // RistrettoSecretKey
    rscalar,            // String (hex)
    value,              // u64
    "account_id_xyz".to_string(),
    "LONG".to_string(),
    "MARKET".to_string(),
    10.0,
    100.0,
    1000.0,
    "PENDING".to_string(),
    45000.0,
    45001.0,
)?;
```

> The result from the above operation looks like this:

```rust
// Returns hex-encoded transaction string
String = "0a1b2c3d4e5f...transaction_hex_data"
```

**Description:** Creates a new perpetual contract trading order using zero-knowledge proofs for privacy-preserving execution. This corresponds to the `submit_trade_order` private API call.

**Use Cases:**

-   Direct order placement for manual and algorithmic trading strategies
-   High-frequency trading and automated market making operations
-   Portfolio rebalancing and risk management order execution
-   Privacy-preserving trading with zero-knowledge proof verification

### Function

`relayer::create_trader_order_zkos`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| input_coin | zkvm::Input | Yes | The input coin (UTXO) for the order. |
| output_memo | zkvm::Output | Yes | The output memo for the order. |
| secret_key | RistrettoSecretKey | Yes | User's secret key for signing. |
| rscalar | String | Yes | Hex-encoded scalar for encryption and commitment. |
| value | u64 | Yes | Value of the order, must match input balance. |
| account_id | String | Yes | The account identifier. |
| position_type | String | Yes | "LONG" or "SHORT". |
| order_type | String | Yes | "MARKET" or "LIMIT". |
| leverage | f64 | Yes | Leverage for the trade. |
| initial_margin | f64 | Yes | Initial margin for the trade. |
| available_margin | f64 | Yes | Available margin. |
| order_status | String | Yes | Initial order status (e.g., "PENDING"). |
| entryprice | f64 | Yes | The desired entry price. |
| execution_price | f64 | Yes | The expected execution price. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Result<String, &'static str> | Hex-encoded transaction data on success. |

---

### Settle Trader Order

```rust
use twilight_privacy_sdk::relayer;
use twilight_privacy_sdk::relayer_types::TXType;
use zkvm::Output;
use quisquislib::ristretto::RistrettoSecretKey;
use uuid::Uuid;

// Settle a filled trader order
let tx_hex = relayer::execute_order_zkos(
    output_memo, // zkvm::Output from the created order
    &secret_key,
    "account_id_xyz".to_string(),
    Uuid::new_v4(),
    "MARKET".to_string(),
    50.0, // settle margin
    "SETTLED".to_string(),
    45500.0, // execution price
    TXType::ORDERTX,
);
```

> The result from the above operation looks like this:

```rust
// Returns hex-encoded transaction string
String = "0a1b2c3d4e5f...transaction_hex_data"
```

**Description:** Executes the settlement process for filled trade orders, finalizing the trade and updating account balances with cryptographic verification. This corresponds to the `settle_trade_order` private API call.

**Use Cases:**

-   Order finalization and trade confirmation for executed positions
-   Automated settlement workflows for algorithmic trading systems
-   Risk management through controlled settlement processes


### Function

`relayer::execute_order_zkos`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| output_memo | zkvm::Output | Yes | The prover memo output from the original order creation. |
| secret_key | &RistrettoSecretKey | Yes | User's secret key for signing. |
| account_id | String | Yes | The account identifier. |
| uuid | Uuid | Yes | The unique identifier of the order to settle. |
| order_type | String | Yes | "MARKET" or "LIMIT". |
| settle_margin_settle_withdraw | f64 | Yes | The margin to settle. |
| order_status | String | Yes | The new order status (e.g., "SETTLED"). |
| execution_price_poolshare_price | f64 | Yes | The final execution price. |
| tx_type | TXType | Yes | Must be `TXType::ORDERTX` for settling trade orders. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | String | Hex-encoded transaction data for the settlement. |

---

### Cancel Trader Order

```rust
use twilight_privacy_sdk::relayer;
use quisquislib::ristretto::RistrettoSecretKey;
use uuid::Uuid;

// Cancel an existing trader order
let tx_hex = relayer::cancel_trader_order_zkos(
    "hex_address_string".to_string(),
    &secret_key,
    "account_id_xyz".to_string(),
    Uuid::new_v4(),
    "LIMIT".to_string(),
    "CANCELLED".to_string(),
);
```

> The result from the above operation looks like this:

```rust
// Returns hex-encoded transaction string
String = "0a1b2c3d4e5f...transaction_hex_data"
```

**Description:** Cancels an existing unfilled trading order, removing it from the orderbook with cryptographic verification. This corresponds to the `cancel_trader_order` private API call.

**Use Cases:**

-   Risk management through rapid order cancellation during market volatility
-   Strategy adjustment and order modification for changing market conditions
-   Position size adjustment and order replacement for optimal execution
-   Emergency order cancellation and risk mitigation during system issues

### Function

`relayer::cancel_trader_order_zkos`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| address_hex | String | Yes | Hex-encoded address of the user. |
| secret_key | &RistrettoSecretKey | Yes | User's secret key for signing the cancellation. |
| account_id | String | Yes | The account identifier. |
| uuid | Uuid | Yes | The unique identifier of the order to cancel. |
| order_type | String | Yes | The type of the order being cancelled. |
| order_status | String | Yes | The new status, should be "CANCELLED". |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | String | Hex-encoded transaction data for the cancellation. |

---

### Query Trader Order

```rust
use twilight_privacy_sdk::relayer;
use quisquislib::ristretto::RistrettoSecretKey;

// Query a trader order
let tx_hex = relayer::query_trader_order_zkos(
    "hex_address_string".to_string(),
    &secret_key,
    "account_id_xyz".to_string(),
    "FILLED".to_string(),
);
```

> The result from the above operation looks like this:

```rust
// Returns hex-encoded transaction string
String = "0a1b2c3d4e5f...transaction_hex_data"
```

**Description:** Queries for trading orders with a specific status for the authenticated account. This can be used to get information about open, filled, or cancelled orders. This corresponds to the `open_orders` and `order_history` private API calls.

**Use Cases:**

-   Real-time portfolio monitoring and open position tracking
-   Risk management and exposure calculation for active trades
-   Order management and strategy adjustment based on current positions
-   Performance monitoring 

### Function

`relayer::query_trader_order_zkos`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| address_hex | String | Yes | Hex-encoded address of the user. |
| secret_key | &RistrettoSecretKey | Yes | User's secret key for signing the query. |
| account_id | String | Yes | The account identifier. |
| order_status | String | Yes | The status of orders to query (e.g., "FILLED", "PENDING"). |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | String | Hex-encoded query data. |

---

## Liquidity Pool Operations

### Deposit to Twilight Liquidity Pool

```rust
use twilight_privacy_sdk::relayer;
use zkvm::{Input, Output};
use quisquislib::ristretto::RistrettoSecretKey;

// Create a new zero-knowledge lend order
let tx_hex = relayer::create_lend_order_zkos(
    input_coin,         // zkvm::Input
    output_memo,        // zkvm::Output
    secret_key,         // RistrettoSecretKey
    rscalar,            // String (hex)
    value,              // u64
    "account_id_xyz".to_string(),
    10000.0,
    "LEND".to_string(),
    "PENDING".to_string(),
    10000.0,
)?;
```

> The result from the above operation looks like this:

```rust
// Returns hex-encoded transaction string
String = "0a1b2c3d4e5f...transaction_hex_data"
```

**Description:** Submits a new lending order to participate in the Twilight Liquidity pool and earn yield on deposited assets using zero-knowledge proofs. This corresponds to the `submit_lend_order` private API call.

**Use Cases:**

-   Yield farming and passive income generation through DeFi lending strategies
-   Liquidity provision to support margin trading and leverage operations on the platform
-   Portfolio diversification with fixed-income alternatives and lending products
-   Capital allocation optimization for unused trading capital and idle funds
-   Automated lending strategies and rebalancing for institutional accounts

### Function

`relayer::create_lend_order_zkos`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| input_coin | zkvm::Input | Yes | The input coin (UTXO) for the order. |
| output_memo | zkvm::Output | Yes | The output memo for the order. |
| secret_key | RistrettoSecretKey | Yes | User's secret key for signing. |
| rscalar | String | Yes | Hex-encoded scalar for encryption and commitment. |
| value | u64 | Yes | Value of the order, must match input balance. |
| account_id | String | Yes | The account identifier. |
| balance | f64 | Yes | Current balance. |
| order_type | String | Yes | Type of order, should be "LEND". |
| order_status | String | Yes | Initial order status (e.g., "PENDING"). |
| deposit | f64 | Yes | The amount to deposit. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Result<String, &'static str> | Hex-encoded transaction data on success. |

---

### Query Lend Order

```rust
use twilight_privacy_sdk::relayer;
use quisquislib::ristretto::RistrettoSecretKey;

// Query a lend order
let tx_hex = relayer::query_lend_order_zkos(
    "hex_address_string".to_string(),
    &secret_key,
    "account_id_xyz".to_string(),
    "FILLED".to_string(),
);
```

> The result from the above operation looks like this:

```rust
// Returns hex-encoded transaction string
String = "0a1b2c3d4e5f...transaction_hex_data"
```

**Description:** Queries for lending orders with a specific status for the authenticated account. This can be used to get information about active or past lending positions.

**Use Cases:**

-   Lending position monitoring and yield tracking for DeFi strategies
-   Pool share management and withdrawal planning for liquidity providers
-   Performance analysis and ROI calculation for lending portfolios


### Function

`relayer::query_lend_order_zkos`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| address_hex | String | Yes | Hex-encoded address of the user. |
| secret_key | &RistrettoSecretKey | Yes | User's secret key for signing the query. |
| account_id | String | Yes | The account identifier. |
| order_status | String | Yes | The status of orders to query (e.g., "FILLED", "PENDING"). |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | String | Hex-encoded query data. |

---

## Blockchain Interaction

### Get Coin UTXOs by Address

```rust
use twilight_privacy_sdk::chain;

// Get all coin UTXOs for a given address
let utxos_hex = chain::get_coin_utxo_by_address_hex(
    "hex_address_string".to_string()
)?;
```

> The result from the above operation looks like this:

```rust
// Returns a vector of hex-encoded UTXO strings
vec![
    "utxo_hex_1",
    "utxo_hex_2",
]
```

**Description:** Retrieves all unspent coin transaction outputs (UTXOs) id for a given address from the blockchain. This is essential for building new transactions.

**Use Cases:**

-   Wallet balance calculation by summing the values of all UTXOs.
-   Selecting specific UTXOs to use as inputs for new transactions.
-   Displaying a user's available funds in a wallet application.
-   Auditing and verifying on-chain balances for an address.

### Function

`chain::get_coin_utxo_by_address_hex`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| address_hex | String | Yes | The hex-encoded address to query for UTXOs. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Result<Vec<String>, String> | A vector of hex-encoded UTXO strings on success. |

---

### Get Coin Output by UTXO ID

```rust
use twilight_privacy_sdk::chain;

// Get the output details for a specific UTXO
let output = chain::get_coin_output_by_utxo_id_hex(
    "utxo_id_hex_string".to_string()
)?;
```

> The result from the above operation looks like this:

```rust
// Returns a zkvm::Output struct
Output {
    // ... fields of the output
}
```

**Description:** Retrieves the full output data for a given UTXO identifier. This is necessary to construct a valid input for a new transaction.

**Use Cases:**

-   Constructing a `zkvm::Input` for a new transaction.
-   Verifying the value and ownership of a UTXO before spending.
-   Inspecting the details of a specific on-chain asset.
-   Debugging transaction construction issues.
-   Providing detailed transaction information in a block explorer.

### Function

`chain::get_coin_output_by_utxo_id_hex`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| utxo_id_hex | String | Yes | The hex-encoded ID of the UTXO to retrieve. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Result<zkvm::Output, String> | The `zkvm::Output` struct on success. |

---

### Broadcast Transaction

```rust
use twilight_privacy_sdk::chain;
use transaction::Transaction;

// Broadcast a signed transaction to the network
let tx_hash = chain::tx_commit_broadcast_transaction(
    transaction // A fully constructed and signed transaction::Transaction
)?;
```

> The result from the above operation looks like this:

```rust
// Returns the transaction hash as a string
"a1b2c3d4...transaction_hash"
```

**Description:** Submits a fully formed and signed transaction to the ZkOS network to be included in a block.

**Use Cases:**

-   Sending a private transfer.
-   Executing a trade order.
-   Depositing funds into a lending pool.
-   Interacting with a smart contract.
-   Finalizing any on-chain action.

### Function

`chain::tx_commit_broadcast_transaction`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| tx | transaction::Transaction | Yes | The complete, signed transaction to broadcast. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Result<String, String> | The transaction hash as a string on successful broadcast. |

---

## Smart Contract Operations

### Deploy Contract

```rust
use twilight_privacy_sdk::script;
use quisquislib::ristretto::RistrettoSecretKey;
use curve25519_dalek::scalar::Scalar;
use address::Network;

// Create a contract deployment transaction
let (deploy_tx, new_state_output) = script::create_contract_deploy_transaction(
    secret_key,                 // RistrettoSecretKey
    20000000000,                // value_sats: u64
    2000000,                    // pool_share: u64
    "coin_address_hex".to_string(),
    encryption_scalar,          // Scalar
    "./relayerprogram.json",    // program_json_path: &str
    Network::default(),
    vec![2000000],              // state_variables: Vec<u64>
    "RelayerInitializer".to_string(),
    1,                          // fee: u64
)?;
```

> The result from the above operation looks like this:

```rust
// Returns a tuple containing the transaction and the new output state
(
    Transaction { /* ... transaction data ... */ },
    Output { /* ... new state output data ... */ }
)
```

**Description:** Creates a script transaction to deploy a new smart contract to the ZkOS virtual machine. This involves creating the initial state of the contract, depositing funds, and running its initialization program.

**Use Cases:**

-   Deploying new DeFi protocols like lending pools or decentralized exchanges.
-   Creating custom applications with on-chain logic.
-   Initializing DAOs or other governance contracts.
-   Launching token contracts or other digital assets.
-   Setting up oracle services or other on-chain utilities.

### Function

`script::create_contract_deploy_transaction`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| sk | RistrettoSecretKey | Yes | The secret key of the account deploying the contract. |
| value_sats | u64 | Yes | The initial amount of satoshis to deposit into the contract. |
| pool_share | u64 | Yes | The initial pool share amount (if applicable). |
| coin_address | String | Yes | The hex-encoded address of the deployer. |
| ecryption_commitment_scalar | Scalar | Yes | Scalar used for ElGamal encryption and commitments. |
| program_json_path | &str | Yes | Path to the JSON file containing the contract's programs. |
| chain_net | Network | Yes | The target network (`Main` or `TestNet`). |
| state_variables | Vec<u64> | Yes | Initial values for the contract's state variables. |
| program_tag | String | Yes | The tag of the initialization program to run upon deployment. |
| fee | u64 | Yes | Transaction fee. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Result<(Transaction, Output), String> | On success, a tuple containing the deploy `Transaction` and the new state `Output`. |

---

### Create State for Deployment

```rust
use twilight_privacy_sdk::script;

// Create the initial input and output states for a new contract
let (input_state, output_state) = script::create_state_for_deployment(
    20000000000,                // value_sats: u64
    vec![2000000],              // state_variables: Vec<u64>
    "contract_address_hex".to_string(),
    "coin_address_hex".to_string(),
);
```

> The result from the above operation looks like this:

```rust
// Returns a tuple containing the input and output state
(
    Input { /* ... zeroed input state data ... */ },
    Output { /* ... initial output state data ... */ }
)
```

**Description:** A helper function that prepares the state UTXOs for a contract deployment. It creates a zeroed-out input state (as this is a new contract) and an output state initialized with the contract's starting values.

**Use Cases:**

-   Manually constructing a contract deployment transaction piece by piece.
-   Creating state objects for off-chain testing or simulation of contract logic.
-   Advanced contract interactions where state needs to be prepared separately.
-   Generating state components for multi-step contract operations.

### Function

`script::create_state_for_deployment`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| value_sats | u64 | Yes | The initial value to be committed to the contract's state. |
| state_variables | Vec<u64> | Yes | Initial values for the contract's state variables. |
| contract_address | String | Yes | The hex-encoded address of the new contract. |
| coin_address | String | Yes | The hex-encoded address of the owner/deployer. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | (Input, Output) | A tuple containing the `Input` and `Output` state UTXOs. |

---

### Create Memo for Deployment

```rust
use twilight_privacy_sdk::script;
use curve25519_dalek::scalar::Scalar;

// Create the output memo for a contract deployment
let output_memo = script::create_memo_for_deployment(
    20000000000,                // initial_deposit: u64
    2000000,                    // pool_share: u64
    "contract_address_hex".to_string(),
    "coin_address_hex".to_string(),
    encryption_scalar,          // Scalar
);
```

> The result from the above operation looks like this:

```rust
// Returns an Output struct containing the memo
Output { /* ... memo data ... */ }
```

**Description:** A helper function that creates the `OutputMemo` for a contract deployment. This memo is a public record on the blockchain that contains commitments to the initial deposit and other relevant data like pool shares.

**Use Cases:**

-   Creating the memo component of a deployment transaction manually.
-   Crafting complex contract calls that require specific, publicly committed data.
-   Testing memo generation logic independently from transaction creation.


### Function

`script::create_memo_for_deployment`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| initial_deposit | u64 | Yes | The amount being deposited into the contract. |
| pool_share | u64 | Yes | The initial pool share amount. |
| contract_address | String | Yes | The hex-encoded address of the new contract. |
| coin_address | String | Yes | The hex-encoded address of the owner/deployer. |
| scalar_commitment | Scalar | Yes | The scalar used for creating the value commitments. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Output | An `Output` struct containing the generated `OutputMemo`. |

---

### Import Contract Programs

```rust
use twilight_privacy_sdk::programcontroller::ContractManager;

// Import a collection of contract programs from a file
let contract_manager = ContractManager::import_program("./relayerprogram.json");
```

> The result from the above operation looks like this:

```rust
// Returns a ContractManager struct
ContractManager {
    program_index: { /* ... hashmap of tags to indices ... */ },
    program: [ /* ... vector of hex-encoded programs ... */ ],
}
```

**Description:** Loads a `ContractManager` instance from a JSON file. The JSON file contains a collection of ZkVM programs and their associated tags, which are necessary for interacting with a specific smart contract.

**Use Cases:**

-   Loading the standard program set for a known contract (e.g., the relayer contract).
-   Initializing a client application to interact with a deployed contract.
-   Sharing contract definitions between developers.
-   Preparing to create call proofs or contract addresses.
-   Accessing specific programs within a contract by their human-readable tag.

### Function

`programcontroller::ContractManager::import_program`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| path | &str | Yes | The file path to the contract programs JSON file. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | ContractManager | An instance of `ContractManager` populated with the programs from the file. |

---

### Create Contract Address

```rust
use twilight_privacy_sdk::programcontroller::ContractManager;
use address::Network;

// First, import the programs
let contract_manager = ContractManager::import_program("./relayerprogram.json");

// Then, create the contract address
let address = contract_manager.create_contract_address(Network::default())?;
```

> The result from the above operation looks like this:

```rust
// Returns a hex-encoded address string
"0c...contract_address_hex"
```

**Description:** Calculates the on-chain address for a smart contract. The address is derived from the Merkle root of all the programs that make up the contract. This allows users to verify that they are interacting with the correct, untampered contract.

**Use Cases:**

-   Determining the address to send funds or transactions to when interacting with a contract.
-   Verifying that a given address corresponds to a known set of contract programs.
-   Generating addresses for new contracts before deployment.
-   Using the address as an identifier for a contract in off-chain systems.
-   Building contract deployment scripts.

### Function

`programcontroller::ContractManager::create_contract_address`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| network | Network | Yes | The target network (`Main` or `TestNet`). |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Result<String, &'static str> | The hex-encoded contract address on success. |

---

### Create Call Proof

```rust
use twilight_privacy_sdk::programcontroller::ContractManager;
use address::Network;

// First, import the programs
let contract_manager = ContractManager::import_program("./relayerprogram.json");

// Create a call proof for a specific program within the contract
let call_proof = contract_manager.create_call_proof(
    Network::default(),
    "CreateTraderOrder" // The tag of the program to call
)?;
```

> The result from the above operation looks like this:

```rust
// Returns a CallProof struct
CallProof { /* ... Merkle proof data ... */ }
```

**Description:** Generates a Merkle proof (`CallProof`) that cryptographically proves a specific program is part of a contract. This proof must be included in any transaction that calls a function on the smart contract.

**Use Cases:**

-   Creating a transaction that executes a specific contract function.
-   Authorizing a state change in a smart contract.
-   Ensuring that only valid, existing functions on a contract can be called.
-   Building transactions for trading, lending, or any other contract interaction.
-   Submitting valid instructions to the ZkOS virtual machine.

### Function

`programcontroller::ContractManager::create_call_proof`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| network | Network | Yes | The target network (`Main` or `TestNet`). |
| tag | &str | Yes | The tag identifying the specific program to be called. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Result<CallProof, &'static str> | The `CallProof` struct needed for the transaction on success. |

---

## Utility Functions

### Create Input from Output

```rust
use twilight_privacy_sdk::util;
use zkvm::{Input, Output};

// Create an Input from a previously fetched Output and UTXO ID
let input: Input = util::create_input_coin_from_output_coin(
    output, // zkvm::Output
    utxo_id_hex, // String
)?;
```

> The result from the above operation looks like this:

```rust
// Returns a zkvm::Input struct
Input { /* ... input data ... */ }
```

**Description:** A fundamental utility function that converts a coin `Output` and its corresponding UTXO ID into a spendable `Input`. This is a required step for constructing almost any transaction.

**Use Cases:**

-   Preparing a UTXO to be spent in a transfer, trade, or contract call.
-   Constructing transaction chains where the output of one transaction becomes the input of another.
-   Building custom transaction logic from scratch.
-   Wallet development for managing and spending funds.
-   Assembling inputs for batch transactions.

### Function

`util::create_input_coin_from_output_coin`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| out | Output | Yes | The `zkvm::Output` to be converted into an input. |
| utxo | String | Yes | The hex-encoded UTXO ID corresponding to the output. |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Result<Input, &'static str> | The `zkvm::Input` struct on success. |

---

### Create Trader Memo Output

```rust
use twilight_privacy_sdk::util;
use twilight_privacy_sdk::relayer_types::PositionType;
use curve25519_dalek::scalar::Scalar;

// Create an OutputMemo for a trade order
let output_memo = util::create_output_memo_for_trader(
    "contract_address_hex".to_string(),
    "owner_address_hex".to_string(),
    1000,           // initial_margin
    5000,           // position_size
    10,             // leverage
    45000,          // entry_price
    PositionType::LONG,
    scalar,         // Scalar for blinding
    0,              // timebounds
);
```

> The result from the above operation looks like this:

```rust
// Returns a zkvm::Output struct containing a memo
Output { /* ... memo data for trading ... */ }
```

**Description:** Creates a specialized `OutputMemo` containing all the necessary data for submitting a trade order to the relayer contract.

**Use Cases:**

-   Manually constructing a `submit_trade_order` transaction.
-   Building custom trading clients with specific memo requirements.
-   Testing trade order creation logic.
-   Creating complex trading strategies that require manual memo construction.
-   Integrating with automated trading systems.

### Function

`util::create_output_memo_for_trader`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| script_address | String | Yes | The hex-encoded address of the relayer contract. |
| owner_address | String | Yes | The hex-encoded address of the trader. |
| initial_margin | u64 | Yes | The initial margin for the position. |
| position_size | u64 | Yes | The size of the position. |
| leverage | u64 | Yes | The leverage for the position. |
| entry_price | u64 | Yes | The desired entry price. |
| order_side | PositionType | Yes | `PositionType::LONG` or `PositionType::SHORT`. |
| scalar | Scalar | Yes | The random scalar used for blinding the commitments. |
| timebounds | u32 | Yes | The timebounds for the transaction (usually 0). |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Output | An `Output` struct containing the generated `OutputMemo`. |

---

### Create Lender Memo Output

```rust
use twilight_privacy_sdk::util;
use curve25519_dalek::scalar::Scalar;

// Create an OutputMemo for a lend order
let output_memo = util::create_output_memo_for_lender(
    "contract_address_hex".to_string(),
    "owner_address_hex".to_string(),
    10000,          // deposit
    10000,          // pool_share
    scalar,         // Scalar for blinding
    0,              // timebounds
);
```

> The result from the above operation looks like this:

```rust
// Returns a zkvm::Output struct containing a memo
Output { /* ... memo data for lending ... */ }
```

**Description:** Creates a specialized `OutputMemo` for submitting a lend order to the relayer contract's lending pool.

**Use Cases:**

-   Manually constructing a `submit_lend_order` transaction.
-   Building DeFi applications that interact with the lending pool.
-   Testing lending and yield-farming logic.
-   Creating automated strategies for providing liquidity.
-   Developing custom interfaces for DeFi lending.

### Function

`util::create_output_memo_for_lender`

### Function Parameters

| Parameter | Data_Type | Required | Description |
| --------- | --------- | -------- | ----------- |
| script_address | String | Yes | The hex-encoded address of the relayer contract. |
| owner_address | String | Yes | The hex-encoded address of the lender. |
| deposit | u64 | Yes | The amount of funds to deposit. |
| pool_share | u64 | Yes | The number of pool shares to be received. |
| scalar | Scalar | Yes | The random scalar used for blinding the commitments. |
| timebounds | u32 | Yes | The timebounds for the transaction (usually 0). |

### Return Type

| Field | Data_Type | Description |
| ----- | --------- | ----------- |
| result | Output | An `Output` struct containing the generated `OutputMemo`. |

---
