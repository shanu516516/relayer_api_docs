# WebSocket API

**Endpoint URL**

`WEBSOCKET_ENDPOINT_PRODUCTION = wss://relayer.twilight.rest/ws`

`WEBSOCKET_ENDPOINT_STAGING = wss://app.twilight.rest/ws`

---

## Subscribe Live Price Data

**Description:** Establishes a real-time WebSocket connection to receive live BTC-USD price updates as they occur in the market.

**Use Cases:**

- Real-time price tickers and dashboard displays
- Algorithmic trading systems requiring instant price updates
- Market monitoring and alert systems
- Live charting and trading interface updates

```javascript
const socket = new WebSocket({{ WEBSOCKET_ENDPOINT }});

const subscriptionPayload = {
  jsonrpc: "2.0",
  method: "subscribe_live_price_data",
  id: 123,
  params: null,
};

socket.onopen = () => {
  socket.send(JSON.stringify(subscriptionPayload));
};

socket.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log("Received live price update:", data);
};
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "method": "s_live_price_data",
  "params": {
    "subscription": 8760936935364034,
    "result": [57198.36, "2024-02-28T05:03:14.455674475Z"]
  }
}
```

### Description

Subscribe Live Price Data

### Parameters

| Parameter | Data Type | Required | Description                                 |
| --------- | --------- | -------- | ------------------------------------------- |
| `jsonrpc` | string    | Yes      | JSON-RPC protocol version (must be `"2.0"`) |
| `method`  | string    | Yes      | The method name to invoke                   |
| `id`      | number    | Yes      | Unique identifier for the request           |
| `params`  | null      | Yes      | No additional parameters required           |

### Method

`subscribe_live_price_data`

### Response Fields

| Field        | Data_Type | Description                                          |
| ------------ | --------- | ---------------------------------------------------- |
| subscription | integer   | Unique subscription ID for this WebSocket connection |
| result       | array     | Array containing [price, timestamp]                  |
| result[0]    | number    | Current BTC-USD price (2 decimal places)             |
| result[1]    | string    | Price timestamp (ISO 8601 format)                    |

---

## Subscribe Order Book

**Description:** Provides real-time updates of the order book changes, showing bid and ask orders as they are placed, modified, or filled.

**Use Cases:**

- Market depth visualization and analysis
- Liquidity assessment for large order placement
- Spread monitoring and market making strategies
- Real-time order book displays in trading interfaces

```javascript
const socket = new WebSocket({{ WEBSOCKET_ENDPOINT }});

const subscriptionPayload = {
  jsonrpc: "2.0",
  method: "subscribe_order_book",
  id: 123,
  params: null,
};

socket.onopen = () => {
  socket.send(JSON.stringify(subscriptionPayload));
};

socket.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log("Received live order book update:", data);
};
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "method": "s_order_book",
  "params": {
    "subscription": 7957441702917313,
    "result": {
      "bid": {
        "id": "",
        "positionsize": 4250871.0,
        "price": 42508.71
      }
    }
  }
}
```

### Description

Subscribe Order Book

### Parameters

| Parameter | Data Type | Required | Description                                 |
| --------- | --------- | -------- | ------------------------------------------- |
| `jsonrpc` | string    | Yes      | JSON-RPC protocol version (must be `"2.0"`) |
| `method`  | string    | Yes      | The method name to invoke                   |
| `id`      | number    | Yes      | Unique identifier for the request           |
| `params`  | null      | Yes      | No additional parameters required           |

### Method

`subscribe_order_book`

### Response Fields

| Field        | Data_Type | Description                                          |
| ------------ | --------- | ---------------------------------------------------- |
| subscription | integer   | Unique subscription ID for this WebSocket connection |
| result       | object    | Order book update data                               |
| bid          | object    | Bid (buy) order information (when present)           |
| ask          | object    | Ask (sell) order information (when present)          |
| id           | string    | Order ID (may be empty)                              |
| positionsize | number    | Order size (2 decimal places)                        |
| price        | number    | Order price (2 decimal places)                       |

---

## Subscribe Candle Data

**Description:** Streams real-time candlestick data updates for specified time intervals, providing OHLCV data as new candles form.

**Use Cases:**

- Real-time charting applications and technical analysis
- Automated trading strategies based on candlestick patterns
- Market trend monitoring and analysis
- Live price action visualization

```javascript
const socket = new WebSocket({{ WEBSOCKET_ENDPOINT }});

const subscriptionPayload = {
  jsonrpc: "2.0",
  method: "subscribe_candle_data",
  id: 123,
  params: { interval: "ONE_HOUR" },
};

socket.onopen = () => {
  socket.send(JSON.stringify(subscriptionPayload));
};

socket.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log("Received live candle data update:", data);
};
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "method": "s_candle_data",
  "params": {
    "subscription": 7957441702917313,
    "result": [
      {
        "btc_volume": "0",
        "close": "57207.50",
        "end": "2024-02-28T04:59:44.020048Z",
        "high": "57271.44",
        "low": "57174.00",
        "open": "57215.20",
        "resolution": "1 hour",
        "start": "2024-02-28T04:54:46.242852Z",
        "trades": 1,
        "usd_volume": "0"
      }
    ]
  }
}
```

### Description

Subscribe Candle Data

### Parameters

| Parameter | Data Type | Required | Description                                                                                                                                                                                  |
| --------- | --------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `jsonrpc` | string    | Yes      | JSON-RPC protocol version (must be `"2.0"`)                                                                                                                                                  |
| `method`  | string    | Yes      | The method name to invoke                                                                                                                                                                    |
| `id`      | number    | Yes      | Unique identifier for the request                                                                                                                                                            |
| `params`  | object    | Yes      | Must include `interval` field with values: `ONE_MINUTE`, `FIVE_MINUTE`, `FIFTEEN_MINUTE`, `THIRTY_MINUTE`, `ONE_HOUR`, `FOUR_HOUR`, `EIGHT_HOUR`, `TWELVE_HOUR`, `ONE_DAY`, `ONE_DAY_CHANGE` |

### Method

`subscribe_candle_data`

### Response Fields

| Field        | Data_Type | Description                                                 |
| ------------ | --------- | ----------------------------------------------------------- |
| subscription | integer   | Unique subscription ID for this WebSocket connection        |
| result       | array     | Array of candle data objects                                |
| btc_volume   | string    | BTC trading volume for the candle period (6 decimal places) |
| close        | string    | Closing price for the candle (2 decimal places)             |
| end          | string    | End timestamp of the candle period (ISO 8601)               |
| high         | string    | Highest price during the candle period (2 decimal places)   |
| low          | string    | Lowest price during the candle period (2 decimal places)    |
| open         | string    | Opening price for the candle (2 decimal places)             |
| resolution   | string    | Time interval resolution (e.g., "1 hour", "1 day")          |
| start        | string    | Start timestamp of the candle period (ISO 8601)             |
| trades       | integer   | Number of trades executed during the candle period          |
| usd_volume   | string    | USD trading volume for the candle period (2 decimal places) |

---

## Subscribe Recent Trades

**Description:** Streams real-time trade execution data, showing completed trades as they happen on the Relayer-matchbook.

**Use Cases:**

- Live trade feed monitoring and analysis
- Volume analysis and market activity tracking
- Time and sales displays for trading interfaces
- Market sentiment analysis through trade flow

```javascript
const socket = new WebSocket({{ WEBSOCKET_ENDPOINT }});

const subscriptionPayload = {
  jsonrpc: "2.0",
  method: "subscribe_recent_trades",
  id: 123,
  params: null,
};

socket.onopen = () => {
  socket.send(JSON.stringify(subscriptionPayload));
};

socket.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log("Received live recent trades update:", data);
};
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "method": "s_recent_trades",
  "params": {
    "subscription": 5213914976941873,
    "result": {
      "order_id": "a2369fcf-489b-4ddf-85f6-78ec076401d0",
      "side": "SHORT",
      "price": 51959.0,
      "positionsize": 7981941580.0,
      "timestamp": "2024-02-28T05:00:00.025776783+00:00"
    }
  }
}
```

### Description

Subscribe Recent Trades

### Parameters

| Parameter | Data Type | Required | Description                                 |
| --------- | --------- | -------- | ------------------------------------------- |
| `jsonrpc` | string    | Yes      | JSON-RPC protocol version (must be `"2.0"`) |
| `method`  | string    | Yes      | The method name to invoke                   |
| `id`      | number    | Yes      | Unique identifier for the request           |
| `params`  | null      | Yes      | No additional parameters required           |

### Method

`subscribe_recent_trades`

### Response Fields

| Field        | Data_Type | Description                                          |
| ------------ | --------- | ---------------------------------------------------- |
| subscription | integer   | Unique subscription ID for this WebSocket connection |
| result       | object    | Recent trade data                                    |
| order_id     | string    | Unique identifier for the executed trade             |
| side         | string    | Trade direction ("LONG" or "SHORT")                  |
| price        | number    | Execution price of the trade (2 decimal places)      |
| positionsize | number    | Size of the executed trade (2 decimal places)        |
| timestamp    | string    | Trade execution timestamp (ISO 8601 format)          |

---

## Subscribe Heartbeat

**Description:** Establishes a heartbeat connection to monitor WebSocket connection health and ensure real-time connectivity.

**Use Cases:**

- Connection health monitoring and reliability
- Network latency measurement and optimization
- Automatic reconnection logic for trading systems
- System health checks and uptime monitoring

```javascript
const socket = new WebSocket({{ WEBSOCKET_ENDPOINT }});

const subscriptionPayload = {
  jsonrpc: "2.0",
  method: "subscribe_heartbeat",
  id: 123,
  params: null,
};

socket.onopen = () => {
  socket.send(JSON.stringify(subscriptionPayload));
};

socket.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log("Received live recent trades update:", data);
};
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "method": "s_heartbeat",
  "params": {
    "subscription": 8669657463617608,
    "result": "BEAT"
  }
}
```

### Description

Subscribe Heartbeat

### Parameters

| Parameter | Data Type | Required | Description                                 |
| --------- | --------- | -------- | ------------------------------------------- |
| `jsonrpc` | string    | Yes      | JSON-RPC protocol version (must be `"2.0"`) |
| `method`  | string    | Yes      | The method name to invoke                   |
| `id`      | number    | Yes      | Unique identifier for the request           |
| `params`  | null      | Yes      | No additional parameters required           |

### Method

`subscribe_heartbeat`

### Response Fields

| Field        | Data_Type | Description                                          |
| ------------ | --------- | ---------------------------------------------------- |
| subscription | integer   | Unique subscription ID for this WebSocket connection |
| result       | string    | Heartbeat status message (always "BEAT")             |

---

### Unsubscribing from a WebSocket Stream

**Description:** Terminates active WebSocket subscriptions to stop receiving real-time updates and manage connection resources.

**Use Cases:**

- Resource management and connection optimization
- Subscription cleanup when switching between data feeds
- Bandwidth management for multiple concurrent connections
- Clean disconnection from specific data streams

To stop receiving updates, send an `unsubscribe_*` request with the subscription ID you received when you subscribed:

```javascript
// Assume you stored the subscription id from the earlier response
const subId = 7957441702917313; // example

const unsubscribePayload = {
  jsonrpc: "2.0",
  method: "unsubscribe_order_book", // change accordingly
  id: 456,
  params: [subId],
};

socket.send(JSON.stringify(unsubscribePayload));
```

### Unsubscribe Parameters

| Parameter | Data Type | Required | Description                                    |
| --------- | --------- | -------- | ---------------------------------------------- |
| `jsonrpc` | string    | Yes      | JSON-RPC protocol version (must be `"2.0"`)    |
| `method`  | string    | Yes      | The unsubscribe method name                    |
| `id`      | number    | Yes      | Unique identifier for the request              |
| `params`  | array     | Yes      | Array containing the subscription ID to cancel |

Available unsubscribe methods:

| Method                        | Description                          |
| ----------------------------- | ------------------------------------ |
| `unsubscribe_live_price_data` | Stop receiving live price updates    |
| `unsubscribe_order_book`      | Stop receiving order book updates    |
| `unsubscribe_candle_data`     | Stop receiving candle data updates   |
| `unsubscribe_recent_trades`   | Stop receiving recent trades updates |
| `unsubscribe_heartbeat`       | Stop receiving heartbeat messages    |
