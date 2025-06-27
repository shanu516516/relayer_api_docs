# Private API

**Endpoint URL**

`API_ENDPOINT_PRODUCTION = https://relayer.twilight.rest`

`API_ENDPOINT_STAGING = https://app.twilight.rest`

## Authentication

Authentication is required for all private API methods using the following headers:

| Header Name       | Description                                                     | Required |
| ----------------- | --------------------------------------------------------------- | -------- |
| `relayer-api-key` | Your unique API key obtained from the `/register` endpoint      | Yes      |
| `signature`       | HMAC-SHA256 signature of the request body using your API secret | Yes      |
| `datetime`        | Unix timestamp in milliseconds                                  | Yes      |

### JavaScript Methods Used in Examples

```javascript
const CryptoJS = require("crypto-js");

function generateSignature(body, secret) {
  return CryptoJS.HmacSHA256(body, secret).toString();
}

function getCurrentTimestamp() {
  return Date.now().toString();
}
```

### Request Structure

All private API requests follow this structure:

| Component    | Description                    |
| ------------ | ------------------------------ |
| URL          | `API_ENDPOINT/api/private`     |
| Method       | `POST`                         |
| Content-Type | `application/json`             |
| Body         | JSON-RPC 2.0 formatted request |

### Authentication Process

1. Obtain `api_key` and `api_secret` from the `/register` endpoint
2. Create JSON-RPC request body
3. Generate HMAC-SHA256 signature of the body using your `api_secret`
4. Include required headers in your request
5. Send POST request to the private API endpoint

### Getting API Credentials

Before you can use any private API methods, you must first obtain your API credentials (`api_key` and `api_secret`) by calling the **Login** method available in the Public API.

**Step 1: Call the Login Method**

The `login` method is available in the Public API ([Click Here](/#login)) and requires your Twilight account signature for authentication. Here's how to use it:

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  account_address: "twilight1l03j8j5nwegy9fkz9p0whkxch2e2zqcq6lvfda",
  data: "hello",
  signature: {
    pub_key: {
      type: "tendermint/PubKeySecp256k1",
      value: "AkNImdlt3/+4axILXJsyiBigMWheg8i8npwTX/AzBrSC",
    },
    signature:
      "waaVXJnXIYQd2BG4rVA12q5OTuctzcDt7BLyHw7Yx/1b2iDFrl4kOcC/VlvE3tvLZq7Dd/qSiMEdYK1DvDPmZw==",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/register", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

**Step 2: Receive Your API Credentials**

The login method will return your API credentials:

```json
{
  "api_key": "7d4fd427-ab9f-4a4d-8163-7faddb0c50e2",
  "api_secret": "dab81c56-2cb1-4bfb-b58d-26e14d1262d6"
}
```

**Step 3: Use Credentials in Private API Calls**

Once you have your `api_key` and `api_secret`, include them in all private API requests as shown in the authentication headers above.

<aside class="notice">
<strong>Important:</strong> Keep your <code>api_secret</code> secure and never expose it in client-side code. The <code>api_secret</code> is used to generate HMAC-SHA256 signatures for request authentication.
</aside>

---

## Submit Lend Order Zkos

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "submit_lend_order",
  id: 123,
  params: {
    data: "0a000000000000006163636f756e745f696400000000010000000000000000002440000000000000244000000000000024400400000000000000004cdd4000000000004cdd400000000000000000000000000000000000000000000000000000000000000000000000000000000000f67459dae1fc1557f4d04329b55ff99f93f0181c520feae7d2b36d107d6573492c7f1847780e5e00628e6c03daa79d09ec7068e17af8fb458267aacfc9f2902f8a000000000000003063343264383661616637393037306532303235653363353139303461336565393162643065663262643631383064393363643633316265613963373166313636636532633263333936393131356362333238333238363062353631653762636664366433396533313238643732663936343632386562653839616434326466343163626639653036620001000000010000008a000000000000003063343264383661616637393037306532303235653363353139303461336565393162643065663262643631383064393363643633316265613963373166313636636532633263333936393131356362333238333238363062353631653762636664366433396533313238643732663936343632386562653839616434326466343163626639653036628a0000000000000030633432643836616166373930373065323032356533633531393034613365653931626430656632626436313830643933636436333162656139633731663136366365326332633339363931313563623332383332383630623536316537626366643664333965333132386437326639363436323865626538396164343264663431636266396530366201000000000000000a0000000000000000000000000000006ad54205ef1a2c17a85534583b763f7e66856273cfc4792cb787295d563afb0100000000004000000000000000606052ed69a939d6cef060fe417eae2da1ff94bf9c3191818eab27f9cd767d662ab4767e1a103969e3ec8c29486970657062e6f821d82f29693c82b110eb2006010000000100000000000000ab01deb6e24b2810fbc5648cd99875c659530ec11dc7826ba49e3b04f6db130201000000000000004ace5f3aeccc847fcd24ebce3b026ea2b764b825a4b4a1c98ba8e42d31ffbc070000000000000000379cebd51a20359852e8f0ef04fca9c878c9d747c0a606c23f67dab160d2480d",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "msg": "Order request submitted successfully",
    "id_key": "REQIDABCD1234567890ABCDEF1234567890ABCDEF"
  },
  "id": 123
}
```

**Description:** Submits a new lending order to participate in the lending pool and earn yield on deposited assets using zero-knowledge proofs.

**Use Cases:**

- Yield farming and passive income generation through DeFi lending strategies
- Liquidity provision to support margin trading and leverage operations on the platform
- Portfolio diversification with fixed-income alternatives and lending products
- Capital allocation optimization for unused trading capital and idle funds
- Automated lending strategies and rebalancing for institutional accounts

### HTTP Method

`POST`

### RPC Method

`submit_lend_order`

### Message Parameters

| Params | Data_Type | Required | Values                                               |
| ------ | --------- | -------- | ---------------------------------------------------- |
| data   | string    | Yes      | Hex-encoded zkOS transaction data for the lend order |

### Response Fields

| Field  | Data_Type | Description                                     |
| ------ | --------- | ----------------------------------------------- |
| msg    | string    | Success message confirming order submission     |
| id_key | string    | Unique request identifier for tracking purposes |

---

## Submit Trade Order Zkos

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "submit_trade_order",
  id: 123,
  params: {
    data: "0a000000000000006163636f756e745f69640000000001000000000000000000344000000000006af84000000000006af84004000000000000000017e140000000000017e140000000000000000060a86ee9d8da7e431b707fb1e344eea721fab8fe2b1ba9461a23182bf9b6d51e005a0cfa6cb6d5149e4d1602863014a1411fdda83ee83c6ef8b9e9934a4211ad4c62cb8729fda0d937220b11650d2eb063b7dfc5e26ee867597a2d69f52749b3118a000000000000003063376363666332356563306335333561383233326537383564646563333939373264633438653235616535373065333638623933383464633631343765633633396234656137313138623030303238393463396432643962666361663732643437613061343938393335313861346366623330613065383162613334613531363834653266303565390001000000010000002a000000000000003138663265626461313733666663366164326533623464336133383634613936616538613666376533308a000000000000003063376363666332356563306335333561383233326537383564646563333939373264633438653235616535373065333638623933383464633631343765633633396234656137313138623030303238393463396432643962666361663732643437613061343938393335313861346366623330613065383162613334613531363834653266303565390100000000000000a0860100000000000000000000000000b899875f246706825d9a849a195da763b3718fc2bdf44cc4eccbb447fe484d010104000000000000000300000001000000003c534c1000000000000000000000000000000000000000000000000000000002000000010000000000000014000000000000000000000000000000b899875f246706825d9a849a195da763b3718fc2bdf44cc4eccbb447fe484d010300000001000000b888000000000000000000000000000000000000000000000000000000000000030000000100000001000000000000000000000000000000000000000000000000000000000000000000000040000000000000004ea572adc412934d369e32e695dbc3eb00d2179cc725365a22e222a6d8f1b254cfb35aa583cd29e1d60d80fc632b557c8388ddd80d9c65d1e0f7547914608a05010000000100000000000000708bceb6fb2e6b61320047c873243ec510e3da990436e6e51884a226ba8fe30a010000000000000075bc2d792cbe4f8852a58874a3f58301f82c1dd82916076e7da1f805dd88cb070000000000000000c6d6a65205e48547a1baa04fa2a0cbd918957b952ee56eb19eb88cd6a5c47106",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "msg": "Order request submitted successfully",
    "id_key": "REQIDABCD1234567890ABCDEF1234567890ABCDEF"
  },
  "id": 123
}
```

**Description:** Submits a new perpetual contract trading order using zero-knowledge proofs for privacy-preserving execution.

**Use Cases:**

- Direct order placement for manual and algorithmic trading strategies
- High-frequency trading and automated market making operations
- Portfolio rebalancing and risk management order execution
- Strategic position building and liquidation for institutional trading
- Privacy-preserving trading with zero-knowledge proof verification

### HTTP Method

`POST`

### RPC Method

`submit_trade_order`

### Message Parameters

| Params | Data_Type | Required | Values                                                |
| ------ | --------- | -------- | ----------------------------------------------------- |
| data   | string    | Yes      | Hex-encoded zkOS transaction data for the trade order |

### Response Fields

| Field  | Data_Type | Description                                     |
| ------ | --------- | ----------------------------------------------- |
| msg    | string    | Success message confirming order submission     |
| id_key | string    | Unique request identifier for tracking purposes |

---

## Settle Trade Order

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "settle_trade_order",
  id: 123,
  params: {
    data: "0x48656c6c6f576f726c64",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "msg": "Order request submitted successfully",
    "id_key": "REQIDABCD1234567890ABCDEF1234567890ABCDEF"
  },
  "id": 123
}
```

**Description:** Executes the settlement process for filled trade orders, finalizing the trade and updating account balances with cryptographic verification.

**Use Cases:**

- Order finalization and trade confirmation for executed positions
- Settlement timing optimization for tax and accounting purposes
- Automated settlement workflows for algorithmic trading systems
- Risk management through controlled settlement processes
- Compliance and audit trail maintenance for trade settlement records

### HTTP Method

`POST`

### RPC Method

`settle_trade_order`

### Message Parameters

| Params | Data_Type | Required | Values                                               |
| ------ | --------- | -------- | ---------------------------------------------------- |
| data   | string    | Yes      | Hex-encoded zkOS settlement data for the trade order |

### Response Fields

| Field  | Data_Type | Description                                     |
| ------ | --------- | ----------------------------------------------- |
| msg    | string    | Success message confirming order submission     |
| id_key | string    | Unique request identifier for tracking purposes |

---

## Settle Lend Order

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "settle_lend_order",
  id: 123,
  params: {
    data: "0x48656c6c6f576f726c64",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "msg": "Order request submitted successfully",
    "id_key": "REQIDABCD1234567890ABCDEF1234567890ABCDEF"
  },
  "id": 123
}
```

**Description:** Executes the settlement process for lending orders, finalizing the lending position and updating pool shares with yield calculations.

**Use Cases:**

- Lending position finalization and yield calculation confirmation
- Withdrawal processing and capital reallocation for lending strategies
- Automated settlement for DeFi lending and yield optimization protocols
- Pool share reconciliation and accurate yield distribution
- Compliance reporting for lending income and tax calculation purposes

### HTTP Method

`POST`

### RPC Method

`settle_lend_order`

### Message Parameters

| Params | Data_Type | Required | Values                                              |
| ------ | --------- | -------- | --------------------------------------------------- |
| data   | string    | Yes      | Hex-encoded zkOS settlement data for the lend order |

### Response Fields

| Field  | Data_Type | Description                                     |
| ------ | --------- | ----------------------------------------------- |
| msg    | string    | Success message confirming order submission     |
| id_key | string    | Unique request identifier for tracking purposes |

---

## Cancel Trader Order

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "cancel_trader_order",
  id: 123,
  params: {
    data: "0x48656c6c6f576f726c64",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "msg": "Order request submitted successfully",
    "id_key": "REQIDABCD1234567890ABCDEF1234567890ABCDEF"
  },
  "id": 123
}
```

**Description:** Cancels an existing unfilled or partially filled trading order, removing it from the orderbook with cryptographic verification.

**Use Cases:**

- Risk management through rapid order cancellation during market volatility
- Strategy adjustment and order modification for changing market conditions
- Automated order management and stop-loss implementation for trading algorithms
- Position size adjustment and order replacement for optimal execution
- Emergency order cancellation and risk mitigation during system issues

### HTTP Method

`POST`

### RPC Method

`cancel_trader_order`

### Message Parameters

| Params | Data_Type | Required | Values                                                  |
| ------ | --------- | -------- | ------------------------------------------------------- |
| data   | string    | Yes      | Hex-encoded zkOS cancellation data for the trader order |

### Response Fields

| Field  | Data_Type | Description                                     |
| ------ | --------- | ----------------------------------------------- |
| msg    | string    | Success message confirming order submission     |
| id_key | string    | Unique request identifier for tracking purposes |

---

## Submit Bulk Order

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "submit_bulk_order",
  id: 123,
  params: [
    {
      data: "0x48656c6c6f576f726c64",
    },
    {
      data: "0x48656c6c6f576f726c65",
    },
  ],
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": "OK",
  "id": 123
}
```

**Description:** Submits multiple trading orders in a single request for efficient batch processing and reduced latency.

**Use Cases:**

- High-frequency trading strategies requiring multiple simultaneous orders
- Portfolio rebalancing with multiple position adjustments
- Market making strategies with bid/ask order pairs
- Algorithmic trading with complex multi-leg strategies
- Institutional trading with large order coordination

### HTTP Method

`POST`

### RPC Method

`submit_bulk_order`

### Message Parameters

| Params          | Data_Type | Required | Values                                            |
| --------------- | --------- | -------- | ------------------------------------------------- |
| Array of orders | array     | Yes      | Array of order objects with hex-encoded zkOS data |

### Response Fields

| Field  | Data_Type | Description                                                     |
| ------ | --------- | --------------------------------------------------------------- |
| result | string    | Status confirmation ("OK" indicates successful bulk submission) |

---

## Open Order

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "open_orders",
  id: 123,
  params: {},
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "id": 50,
      "uuid": "3374714d-8a95-4096-855f-7e2675fe0dc8",
      "account_id": "0c08ed4f0daeec9b3af55b0cce550ee94cb297171929a64bb598e901fbf0783e67c06ad24938611c9e4620b9467d532c46bdb1212c5c06e66ac65854b9ddf60e77721c4f8b",
      "position_type": "LONG",
      "order_status": "FILLED",
      "order_type": "MARKET",
      "entryprice": "42508.71",
      "execution_price": "30000",
      "positionsize": "4250871",
      "leverage": "10",
      "initial_margin": "10",
      "available_margin": "10",
      "timestamp": "2024-01-31T11:14:45.575359Z",
      "bankruptcy_price": "38644.28",
      "bankruptcy_value": "110",
      "maintenance_margin": "0.5375",
      "liquidation_price": "38834.04",
      "unrealized_pnl": "0",
      "settlement_price": "0",
      "entry_nonce": 0,
      "exit_nonce": 0,
      "entry_sequence": 1,
      "fee_filled": "0",
      "fee_settled": "0"
    }
  ],
  "id": 123
}
```

**Description:** Retrieves all open trading orders for the authenticated account, showing unfilled and partially filled positions.

**Use Cases:**

- Real-time portfolio monitoring and open position tracking
- Risk management and exposure calculation for active trades
- Order management and strategy adjustment based on current positions
- Automated trading system position reconciliation
- Performance monitoring and position size optimization

### HTTP Method

`POST`

### RPC Method

`open_orders`

### Message Parameters

| Params | Data_Type | Required | Values                 |
| ------ | --------- | -------- | ---------------------- |
| N/A    | null      | No       | No parameters required |

### Response Fields

| Field              | Data_Type | Description                                                 |
| ------------------ | --------- | ----------------------------------------------------------- |
| id                 | integer   | Internal order ID                                           |
| uuid               | string    | Unique order identifier                                     |
| account_id         | string    | Account public key associated with the order                |
| position_type      | string    | Position direction ("LONG" or "SHORT")                      |
| order_status       | string    | Current order status ("FILLED", "OPEN", "CANCELLED")        |
| order_type         | string    | Order type ("MARKET", "LIMIT")                              |
| entryprice         | string    | Entry price for the position (2 decimal places)             |
| execution_price    | string    | Actual execution price (2 decimal places)                   |
| positionsize       | string    | Position size in base currency (2 decimal places)           |
| leverage           | string    | Leverage multiplier (2 decimal places)                      |
| initial_margin     | string    | Initial margin requirement (2 decimal places)               |
| available_margin   | string    | Available margin for the position (2 decimal places)        |
| timestamp          | string    | Order creation timestamp (ISO 8601 format)                  |
| bankruptcy_price   | string    | Price at which position becomes bankrupt (2 decimal places) |
| bankruptcy_value   | string    | Value at bankruptcy price (2 decimal places)                |
| maintenance_margin | string    | Maintenance margin requirement (4 decimal places)           |
| liquidation_price  | string    | Price at which position gets liquidated (2 decimal places)  |
| unrealized_pnl     | string    | Current unrealized profit/loss (2 decimal places)           |
| settlement_price   | string    | Settlement price if order is settled (2 decimal places)     |
| entry_nonce        | integer   | Entry transaction nonce                                     |
| exit_nonce         | integer   | Exit transaction nonce                                      |
| entry_sequence     | integer   | Entry sequence number                                       |
| fee_filled         | string    | Fee paid when order was filled (4 decimal places)           |
| fee_settled        | string    | Fee paid when order was settled (4 decimal places)          |

---

## Order History By Id

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "order_history",
  id: 123,
  params: {
    limit: 10,
    offset: 0,
    from: "2024-01-01T00:00:00Z",
    to: "2024-12-31T23:59:59Z",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "id": 50,
      "uuid": "7c9a959b-2ae2-461b-b199-125e1cb5c9c6",
      "account_id": "0c08ed4f0daeec9b3af55b0cce550ee94cb297171929a64bb598e901fbf0783e67c06ad24938611c9e4620b9467d532c46bdb1212c5c06e66ac65854b9ddf60e77721c4f8b",
      "position_type": "LONG",
      "order_status": "FILLED",
      "order_type": "MARKET",
      "entryprice": "42508.71",
      "execution_price": "30000",
      "positionsize": "4250871",
      "leverage": "10",
      "initial_margin": "10",
      "available_margin": "10",
      "timestamp": "2024-01-31T11:14:45.575359Z",
      "bankruptcy_price": "38644.28",
      "bankruptcy_value": "110",
      "maintenance_margin": "0.5375",
      "liquidation_price": "38834.04",
      "unrealized_pnl": "0",
      "settlement_price": "0",
      "entry_nonce": 0,
      "exit_nonce": 0,
      "entry_sequence": 1,
      "fee_filled": "0",
      "fee_settled": "0"
    }
  ],
  "id": 123
}
```

**Description:** Retrieves historical trading order data for the authenticated account with filtering and pagination options.

**Use Cases:**

- Trading performance analysis and strategy evaluation
- Tax reporting and compliance documentation for regulatory requirements
- Risk assessment and historical position review
- Algorithmic trading strategy backtesting with real execution data
- Customer service and dispute resolution with complete order history

### HTTP Method

`POST`

### RPC Method

`order_history`

### Message Parameters

| Params | Data_Type | Required | Values                                    |
| ------ | --------- | -------- | ----------------------------------------- |
| limit  | integer   | No       | Number of records to return (default: 50) |
| offset | integer   | No       | Number of records to skip (default: 0)    |
| from   | datetime  | No       | Start date for filtering                  |
| to     | datetime  | No       | End date for filtering                    |

### Response Fields

| Field              | Data_Type | Description                                                     |
| ------------------ | --------- | --------------------------------------------------------------- |
| id                 | integer   | Internal order ID                                               |
| uuid               | string    | Unique order identifier                                         |
| account_id         | string    | Account public key associated with the order                    |
| position_type      | string    | Position direction ("LONG" or "SHORT")                          |
| order_status       | string    | Current order status ("FILLED", "OPEN", "CANCELLED", "SETTLED") |
| order_type         | string    | Order type ("MARKET", "LIMIT")                                  |
| entryprice         | string    | Entry price for the position (2 decimal places)                 |
| execution_price    | string    | Actual execution price (2 decimal places)                       |
| positionsize       | string    | Position size in base currency (2 decimal places)               |
| leverage           | string    | Leverage multiplier (2 decimal places)                          |
| initial_margin     | string    | Initial margin requirement (2 decimal places)                   |
| available_margin   | string    | Available margin for the position (2 decimal places)            |
| timestamp          | string    | Order creation timestamp (ISO 8601 format)                      |
| bankruptcy_price   | string    | Price at which position becomes bankrupt (2 decimal places)     |
| bankruptcy_value   | string    | Value at bankruptcy price (2 decimal places)                    |
| maintenance_margin | string    | Maintenance margin requirement (4 decimal places)               |
| liquidation_price  | string    | Price at which position gets liquidated (2 decimal places)      |
| unrealized_pnl     | string    | Current unrealized profit/loss (2 decimal places)               |
| settlement_price   | string    | Settlement price if order is settled (2 decimal places)         |
| entry_nonce        | integer   | Entry transaction nonce                                         |
| exit_nonce         | integer   | Exit transaction nonce                                          |
| entry_sequence     | integer   | Entry sequence number                                           |
| fee_filled         | string    | Fee paid when order was filled (4 decimal places)               |
| fee_settled        | string    | Fee paid when order was settled (4 decimal places)              |

---

## Order History By Client

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "order_history",
  id: 123,
  params: {
    limit: 5,
    offset: 10,
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "id": 45,
      "uuid": "3374714d-8a95-4096-855f-7e2675fe0dc8",
      "account_id": "0c08ed4f0daeec9b3af55b0cce550ee94cb297171929a64bb598e901fbf0783e67c06ad24938611c9e4620b9467d532c46bdb1212c5c06e66ac65854b9ddf60e77721c4f8b",
      "position_type": "LONG",
      "order_status": "SETTLED",
      "order_type": "MARKET",
      "entryprice": "42508.71",
      "execution_price": "43000",
      "positionsize": "4250871",
      "leverage": "10",
      "initial_margin": "10",
      "available_margin": "10",
      "timestamp": "2024-01-30T11:14:45.575359Z",
      "bankruptcy_price": "38644.28",
      "bankruptcy_value": "110",
      "maintenance_margin": "0.5375",
      "liquidation_price": "38834.04",
      "unrealized_pnl": "0",
      "settlement_price": "43000",
      "entry_nonce": 0,
      "exit_nonce": 0,
      "entry_sequence": 1,
      "fee_filled": "12.75",
      "fee_settled": "19.13"
    }
  ],
  "id": 123
}
```

**Description:** Retrieves paginated order history for client-specific analysis and reporting with customizable filtering options.

**Use Cases:**

- Client portfolio management and performance tracking
- Detailed trade analysis for strategy optimization
- Historical data export for external analysis tools
- Compliance reporting with granular order details
- Customer account reconciliation and audit support

### HTTP Method

`POST`

### RPC Method

`order_history`

### Message Parameters

| Params | Data_Type | Required | Values                      |
| ------ | --------- | -------- | --------------------------- |
| limit  | integer   | No       | Number of records to return |
| offset | integer   | No       | Pagination offset           |

### Response Fields

| Field              | Data_Type | Description                                                     |
| ------------------ | --------- | --------------------------------------------------------------- |
| id                 | integer   | Internal order ID                                               |
| uuid               | string    | Unique order identifier                                         |
| account_id         | string    | Account public key associated with the order                    |
| position_type      | string    | Position direction ("LONG" or "SHORT")                          |
| order_status       | string    | Current order status ("FILLED", "OPEN", "CANCELLED", "SETTLED") |
| order_type         | string    | Order type ("MARKET", "LIMIT")                                  |
| entryprice         | string    | Entry price for the position (2 decimal places)                 |
| execution_price    | string    | Actual execution price (2 decimal places)                       |
| positionsize       | string    | Position size in base currency (2 decimal places)               |
| leverage           | string    | Leverage multiplier (2 decimal places)                          |
| initial_margin     | string    | Initial margin requirement (2 decimal places)                   |
| available_margin   | string    | Available margin for the position (2 decimal places)            |
| timestamp          | string    | Order creation timestamp (ISO 8601 format)                      |
| bankruptcy_price   | string    | Price at which position becomes bankrupt (2 decimal places)     |
| bankruptcy_value   | string    | Value at bankruptcy price (2 decimal places)                    |
| maintenance_margin | string    | Maintenance margin requirement (4 decimal places)               |
| liquidation_price  | string    | Price at which position gets liquidated (2 decimal places)      |
| unrealized_pnl     | string    | Current unrealized profit/loss (2 decimal places)               |
| settlement_price   | string    | Settlement price if order is settled (2 decimal places)         |
| entry_nonce        | integer   | Entry transaction nonce                                         |
| exit_nonce         | integer   | Exit transaction nonce                                          |
| entry_sequence     | integer   | Entry sequence number                                           |
| fee_filled         | string    | Fee paid when order was filled (4 decimal places)               |
| fee_settled        | string    | Fee paid when order was settled (4 decimal places)              |

---

## Trade Volume

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "trade_volume",
  id: 123,
  params: {
    from: "2024-01-01T00:00:00Z",
    to: "2024-12-31T23:59:59Z",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "total_usd_volume": "1234567.89",
    "total_btc_volume": "28.456789",
    "order_count": 42,
    "average_order_size": "29418.28"
  },
  "id": 123
}
```

**Description:** Calculates total trading volume statistics for the authenticated account over specified time periods.

**Use Cases:**

- Trading performance metrics and volume-based analysis
- Fee calculation and rebate qualification verification
- Portfolio performance attribution and turnover analysis
- Risk management through volume exposure monitoring
- Institutional reporting and client activity summaries

### HTTP Method

`POST`

### RPC Method

`trade_volume`

### Message Parameters

| Params | Data_Type | Required | Values                            |
| ------ | --------- | -------- | --------------------------------- |
| from   | datetime  | No       | Start date for volume calculation |
| to     | datetime  | No       | End date for volume calculation   |

### Response Fields

| Field              | Data_Type | Description                                    |
| ------------------ | --------- | ---------------------------------------------- |
| total_usd_volume   | string    | Total trading volume in USD (2 decimal places) |
| total_btc_volume   | string    | Total trading volume in BTC (6 decimal places) |
| order_count        | integer   | Total number of orders executed                |
| average_order_size | string    | Average order size in USD (2 decimal places)   |

---

## Get Funding Payment

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "get_funding_payment",
  id: 123,
  params: {
    id: "a4340b19-cd90-411e-adfc-a3695109d7a2",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "order_id": "a4340b19-cd90-411e-adfc-a3695109d7a2",
    "funding_payment": "12.45",
    "funding_rate": "0.001",
    "position_size": "1000000",
    "timestamp": "2024-02-28T12:00:00Z"
  },
  "id": 123
}
```

**Description:** Retrieves funding payment information for a specific order, showing the cost or income from holding perpetual positions.

**Use Cases:**

- Cost calculation for holding perpetual contract positions over time
- Funding payment history tracking for accounting and tax purposes
- Arbitrage strategy analysis and funding rate opportunity identification
- Risk management for long-term position holding costs
- Performance attribution analysis including funding payment impacts

### HTTP Method

`POST`

### RPC Method

`get_funding_payment`

### Message Parameters

| Params | Data_Type | Required | Values                                |
| ------ | --------- | -------- | ------------------------------------- |
| id     | string    | Yes      | Order UUID for funding payment lookup |

### Response Fields

| Field           | Data_Type | Description                                      |
| --------------- | --------- | ------------------------------------------------ |
| order_id        | string    | Order UUID that was queried                      |
| funding_payment | string    | Funding payment amount (2 decimal places)        |
| funding_rate    | string    | Applied funding rate (4 decimal places)          |
| position_size   | string    | Position size for calculation (2 decimal places) |
| timestamp       | string    | Timestamp of funding payment (ISO 8601 format)   |

---

## Last Order Detail

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "last_order_detail",
  id: 123,
  params: {},
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "id": 42,
    "uuid": "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf",
    "account_id": "0c08ed4f0daeec9b3af55b0cce550ee94cb297171929a64bb598e901fbf0783e67c06ad24938611c9e4620b9467d532c46bdb1212c5c06e66ac65854b9ddf60e77721c4f8b",
    "position_type": "LONG",
    "order_status": "FILLED",
    "order_type": "MARKET",
    "entryprice": "42508.71",
    "execution_price": "30000",
    "positionsize": "4250871",
    "leverage": "10",
    "initial_margin": "10",
    "available_margin": "10",
    "timestamp": "2024-01-31T11:14:45.575359Z",
    "bankruptcy_price": "38644.28",
    "bankruptcy_value": "110",
    "maintenance_margin": "0.5375",
    "liquidation_price": "38834.04",
    "unrealized_pnl": "0",
    "settlement_price": "0",
    "entry_nonce": 0,
    "exit_nonce": 0,
    "entry_sequence": 1,
    "fee_filled": "0",
    "fee_settled": "0"
  },
  "id": 123
}
```

**Description:** Retrieves details of the most recent trading order for the authenticated account.

**Use Cases:**

- Quick access to latest trading activity and order status
- Trading application UI updates with most recent order information
- Automated trading system verification of last order execution
- Customer service quick lookup for recent order inquiries
- Real-time trading dashboard with latest order status

### HTTP Method

`POST`

### RPC Method

`last_order_detail`

### Message Parameters

| Params | Data_Type | Required | Values                 |
| ------ | --------- | -------- | ---------------------- |
| N/A    | null      | No       | No parameters required |

### Response Fields

| Field              | Data_Type | Description                                                 |
| ------------------ | --------- | ----------------------------------------------------------- |
| id                 | integer   | Internal order ID                                           |
| uuid               | string    | Unique order identifier                                     |
| account_id         | string    | Account public key associated with the order                |
| position_type      | string    | Position direction ("LONG" or "SHORT")                      |
| order_status       | string    | Current order status ("FILLED", "OPEN", "CANCELLED")        |
| order_type         | string    | Order type ("MARKET", "LIMIT")                              |
| entryprice         | string    | Entry price for the position (2 decimal places)             |
| execution_price    | string    | Actual execution price (2 decimal places)                   |
| positionsize       | string    | Position size in base currency (2 decimal places)           |
| leverage           | string    | Leverage multiplier (2 decimal places)                      |
| initial_margin     | string    | Initial margin requirement (2 decimal places)               |
| available_margin   | string    | Available margin for the position (2 decimal places)        |
| timestamp          | string    | Order creation timestamp (ISO 8601 format)                  |
| bankruptcy_price   | string    | Price at which position becomes bankrupt (2 decimal places) |
| bankruptcy_value   | string    | Value at bankruptcy price (2 decimal places)                |
| maintenance_margin | string    | Maintenance margin requirement (4 decimal places)           |
| liquidation_price  | string    | Price at which position gets liquidated (2 decimal places)  |
| unrealized_pnl     | string    | Current unrealized profit/loss (2 decimal places)           |
| settlement_price   | string    | Settlement price if order is settled (2 decimal places)     |
| entry_nonce        | integer   | Entry transaction nonce                                     |
| exit_nonce         | integer   | Exit transaction nonce                                      |
| entry_sequence     | integer   | Entry sequence number                                       |
| fee_filled         | string    | Fee paid when order was filled (4 decimal places)           |
| fee_settled        | string    | Fee paid when order was settled (4 decimal places)          |

---

## Lend Pool Info

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "lend_pool_info",
  id: 123,
  params: {},
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "sequence": 43566,
    "nonce": 8,
    "total_pool_share": "2300000",
    "total_locked_value": "20048615383",
    "pending_orders": 5,
    "aggregate_log_sequence": 123423,
    "last_snapshot_id": 8765
  },
  "id": 123
}
```

**Description:** Retrieves comprehensive information about the lending pool including total value locked, pool shares, and operational metrics.

**Use Cases:**

- Lending pool performance monitoring and yield calculation
- DeFi analytics and total value locked (TVL) tracking
- Pool utilization analysis for capital efficiency assessment
- Lending strategy optimization based on pool metrics
- Risk assessment for lending operations and pool health monitoring

### HTTP Method

`POST`

### RPC Method

`lend_pool_info`

### Message Parameters

| Params | Data_Type | Required | Values                 |
| ------ | --------- | -------- | ---------------------- |
| N/A    | null      | No       | No parameters required |

### Response Fields

| Field                  | Data_Type | Description                                       |
| ---------------------- | --------- | ------------------------------------------------- |
| sequence               | integer   | Current pool sequence number                      |
| nonce                  | integer   | Pool operation nonce                              |
| total_pool_share       | string    | Total pool shares issued (2 decimal places)       |
| total_locked_value     | string    | Total value locked in the pool (2 decimal places) |
| pending_orders         | integer   | Number of pending lending orders                  |
| aggregate_log_sequence | integer   | Aggregate log sequence for pool operations        |
| last_snapshot_id       | integer   | ID of the last pool snapshot                      |

---

## Trade Order Info

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "trader_order_info",
  id: 123,
  params: {
    id: "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "id": 50,
    "uuid": "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf",
    "account_id": "0c08ed4f0daeec9b3af55b0cce550ee94cb297171929a64bb598e901fbf0783e67c06ad24938611c9e4620b9467d532c46bdb1212c5c06e66ac65854b9ddf60e77721c4f8b",
    "position_type": "LONG",
    "order_status": "FILLED",
    "order_type": "MARKET",
    "entryprice": "42508.71",
    "execution_price": "30000",
    "positionsize": "4250871",
    "leverage": "10",
    "initial_margin": "10",
    "available_margin": "10",
    "timestamp": "2024-01-31T11:14:45.575359Z",
    "bankruptcy_price": "38644.28",
    "bankruptcy_value": "110",
    "maintenance_margin": "0.5375",
    "liquidation_price": "38834.04",
    "unrealized_pnl": "0",
    "settlement_price": "0",
    "entry_nonce": 0,
    "exit_nonce": 0,
    "entry_sequence": 1,
    "fee_filled": "0",
    "fee_settled": "0"
  },
  "id": 123
}
```

**Description:** Retrieves detailed information for a specific trading order by its unique identifier.

**Use Cases:**

- Order status verification and execution confirmation for specific trades
- Risk management and position monitoring for individual orders
- Customer service and order inquiry resolution
- Trading algorithm verification and order tracking
- Audit trail and compliance monitoring for specific transactions

### HTTP Method

`POST`

### RPC Method

`trader_order_info`

### Message Parameters

| Params | Data_Type | Required | Values                                   |
| ------ | --------- | -------- | ---------------------------------------- |
| id     | string    | Yes      | Order UUID for the specific trader order |

### Response Fields

| Field              | Data_Type | Description                                                 |
| ------------------ | --------- | ----------------------------------------------------------- |
| id                 | integer   | Internal order ID                                           |
| uuid               | string    | Unique order identifier                                     |
| account_id         | string    | Account public key associated with the order                |
| position_type      | string    | Position direction ("LONG" or "SHORT")                      |
| order_status       | string    | Current order status ("FILLED", "OPEN", "CANCELLED")        |
| order_type         | string    | Order type ("MARKET", "LIMIT")                              |
| entryprice         | string    | Entry price for the position (2 decimal places)             |
| execution_price    | string    | Actual execution price (2 decimal places)                   |
| positionsize       | string    | Position size in base currency (2 decimal places)           |
| leverage           | string    | Leverage multiplier (2 decimal places)                      |
| initial_margin     | string    | Initial margin requirement (2 decimal places)               |
| available_margin   | string    | Available margin for the position (2 decimal places)        |
| timestamp          | string    | Order creation timestamp (ISO 8601 format)                  |
| bankruptcy_price   | string    | Price at which position becomes bankrupt (2 decimal places) |
| bankruptcy_value   | string    | Value at bankruptcy price (2 decimal places)                |
| maintenance_margin | string    | Maintenance margin requirement (4 decimal places)           |
| liquidation_price  | string    | Price at which position gets liquidated (2 decimal places)  |
| unrealized_pnl     | string    | Current unrealized profit/loss (2 decimal places)           |
| settlement_price   | string    | Settlement price if order is settled (2 decimal places)     |
| entry_nonce        | integer   | Entry transaction nonce                                     |
| exit_nonce         | integer   | Exit transaction nonce                                      |
| entry_sequence     | integer   | Entry sequence number                                       |
| fee_filled         | string    | Fee paid when order was filled (4 decimal places)           |
| fee_settled        | string    | Fee paid when order was settled (4 decimal places)          |

---

## Lend Order Info

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "lend_order_info",
  id: 123,
  params: {
    id: "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "id": 25,
    "uuid": "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf",
    "account_id": "0c08ed4f0daeec9b3af55b0cce550ee94cb297171929a64bb598e901fbf0783e67c06ad24938611c9e4620b9467d532c46bdb1212c5c06e66ac65854b9ddf60e77721c4f8b",
    "balance": "153620",
    "order_status": "FILLED",
    "order_type": "LEND",
    "entry_nonce": 0,
    "exit_nonce": 0,
    "deposit": "153620",
    "new_lend_state_amount": "153620",
    "timestamp": "2024-02-28T04:59:44.020048Z",
    "npoolshare": "100",
    "nwithdraw": "0",
    "payment": "0",
    "tlv0": "0",
    "tps0": "0",
    "tlv1": "0",
    "tps1": "0",
    "tlv2": "0",
    "tps2": "0",
    "tlv3": "0",
    "tps3": "0",
    "entry_sequence": 10
  },
  "id": 123
}
```

**Description:** Retrieves detailed information for a specific lending order including pool share calculations and yield metrics.

**Use Cases:**

- Lending position monitoring and yield tracking for DeFi strategies
- Pool share management and withdrawal planning for liquidity providers
- Performance analysis and ROI calculation for lending portfolios
- Risk assessment and exposure management for lending activities
- Compliance reporting and audit trail for lending operations

### HTTP Method

`POST`

### RPC Method

`lend_order_info`

### Message Parameters

| Params | Data_Type | Required | Values                                 |
| ------ | --------- | -------- | -------------------------------------- |
| id     | string    | Yes      | Order UUID for the specific lend order |

### Response Fields

| Field                 | Data_Type | Description                                          |
| --------------------- | --------- | ---------------------------------------------------- |
| id                    | integer   | Internal lend order ID                               |
| uuid                  | string    | Unique lend order identifier                         |
| account_id            | string    | Account public key associated with the lend order    |
| balance               | string    | Current balance in the lend order (2 decimal places) |
| order_status          | string    | Current order status ("FILLED", "OPEN", "CANCELLED") |
| order_type            | string    | Order type ("LEND")                                  |
| entry_nonce           | integer   | Entry transaction nonce                              |
| exit_nonce            | integer   | Exit transaction nonce                               |
| deposit               | string    | Initial deposit amount (2 decimal places)            |
| new_lend_state_amount | string    | Updated lend state amount (2 decimal places)         |
| timestamp             | string    | Order creation timestamp (ISO 8601 format)           |
| npoolshare            | string    | Number of pool shares (2 decimal places)             |
| nwithdraw             | string    | Withdrawal amount (2 decimal places)                 |
| payment               | string    | Payment amount (2 decimal places)                    |
| tlv0                  | string    | Total locked value tier 0 (2 decimal places)         |
| tps0                  | string    | Total pool shares tier 0 (2 decimal places)          |
| tlv1                  | string    | Total locked value tier 1 (2 decimal places)         |
| tps1                  | string    | Total pool shares tier 1 (2 decimal places)          |
| tlv2                  | string    | Total locked value tier 2 (2 decimal places)         |
| tps2                  | string    | Total pool shares tier 2 (2 decimal places)          |
| tlv3                  | string    | Total locked value tier 3 (2 decimal places)         |
| tps3                  | string    | Total pool shares tier 3 (2 decimal places)          |
| entry_sequence        | integer   | Entry sequence number                                |

---

## Unrealized PnL Pubkey

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "unrealized_pnl",
  id: 123,
  params: {
    pubkey:
      "0c08ed4f0daeec9b3af55b0cce550ee94cb297171929a64bb598e901fbf0783e67c06ad24938611c9e4620b9467d532c46bdb1212c5c06e66ac65854b9ddf60e77721c4f8b",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "total_unrealized_pnl": "245.67",
    "position_count": 3,
    "total_position_value": "15000.00"
  },
  "id": 123
}
```

**Description:** Calculates unrealized profit and loss for all open positions associated with a specific public key.

**Use Cases:**

- Real-time portfolio valuation and mark-to-market calculations
- Risk management and exposure monitoring for active positions
- Performance tracking and profitability analysis
- Margin requirement calculation and liquidation risk assessment
- Portfolio management dashboard and trading interface updates

### HTTP Method

`POST`

### RPC Method

`unrealized_pnl`

### Message Parameters

| Params | Data_Type | Required | Values                                 |
| ------ | --------- | -------- | -------------------------------------- |
| pubkey | string    | No       | Account public key for PnL calculation |

### Response Fields

| Field                | Data_Type | Description                                                  |
| -------------------- | --------- | ------------------------------------------------------------ |
| total_unrealized_pnl | string    | Total unrealized PnL across all positions (2 decimal places) |
| position_count       | integer   | Number of open positions                                     |
| total_position_value | string    | Total value of all positions (2 decimal places)              |

---

## Unrealized PnL OrderId

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "unrealized_pnl",
  id: 123,
  params: {
    order_id: "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf",
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "unrealized_pnl": "125.34",
    "order_id": "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf",
    "current_price": "43500.00",
    "entry_price": "42508.71",
    "position_size": "4250871"
  },
  "id": 123
}
```

**Description:** Calculates unrealized profit and loss for a specific order by its unique identifier.

**Use Cases:**

- Individual position performance monitoring and profit tracking
- Risk assessment for specific trading positions
- Order-level profitability analysis and strategy evaluation
- Position sizing and risk management for specific trades
- Real-time P&L updates for trading applications and dashboards

### HTTP Method

`POST`

### RPC Method

`unrealized_pnl`

### Message Parameters

| Params   | Data_Type | Required | Values                                  |
| -------- | --------- | -------- | --------------------------------------- |
| order_id | string    | No       | Specific order UUID for PnL calculation |

### Response Fields

| Field          | Data_Type | Description                                              |
| -------------- | --------- | -------------------------------------------------------- |
| unrealized_pnl | string    | Unrealized PnL for the specific order (2 decimal places) |
| order_id       | string    | Order UUID that was queried                              |
| current_price  | string    | Current market price (2 decimal places)                  |
| entry_price    | string    | Order entry price (2 decimal places)                     |
| position_size  | string    | Position size (2 decimal places)                         |

---

## Unrealized PnL All

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "your-api-key");
myHeaders.append("signature", generateSignature(raw, "your-api-secret"));
myHeaders.append("datetime", getCurrentTimestamp());

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "unrealized_pnl",
  id: 123,
  params: {},
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api/private", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "total_unrealized_pnl": "342.15",
    "position_count": 5,
    "total_position_value": "25000.00",
    "account_id": "0c08ed4f0daeec9b3af55b0cce550ee94cb297171929a64bb598e901fbf0783e67c06ad24938611c9e4620b9467d532c46bdb1212c5c06e66ac65854b9ddf60e77721c4f8b"
  },
  "id": 123
}
```

**Description:** Calculates total unrealized profit and loss for all open positions in the authenticated account.

**Use Cases:**

- Complete portfolio performance monitoring and total return calculation
- Risk management and overall exposure assessment for the entire account
- Margin requirement calculation and account health monitoring
- Portfolio management and performance reporting for all positions
- Real-time account valuation and net worth calculation

### HTTP Method

`POST`

### RPC Method

`unrealized_pnl`

### Message Parameters

| Params | Data_Type | Required | Values                                         |
| ------ | --------- | -------- | ---------------------------------------------- |
| N/A    | null      | No       | No parameters required (returns all positions) |

### Response Fields

| Field                | Data_Type | Description                                                  |
| -------------------- | --------- | ------------------------------------------------------------ |
| total_unrealized_pnl | string    | Total unrealized PnL across all positions (2 decimal places) |
| position_count       | integer   | Number of open positions                                     |
| total_position_value | string    | Total value of all positions (2 decimal places)              |
| account_id           | string    | Account public key associated with the positions             |
