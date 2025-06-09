# Websocket API

**Endpoint URL**

`WEBSOCKET_ENDPOINT_PRODUCTION = wss://twilight.rest/ws`

`WEBSOCKET_ENDPOINT_STAGING = wss://rpc.twilight.rest/ws`

## Subscribe Live Price Data

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

- **`jsonrpc`** (String, required): The JSON-RPC protocol version. Must be set to `"2.0"`.
- **`method`** (String, required): The name of the method to be invoked.
- **`id`** (Number, required): A unique identifier for the request.
- **`params`** (Object, optional): Additional parameters for the method, if any.

### Method

`subscribe_live_price_data`

## Subscribe Order Book

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
    "result": [
      {
        "account_id": "0c08ed4f0daeec9b3af55b0cce550ee94cb297171929a64bb598e901fbf0783e67c06ad24938611c9e4620b9467d532c46bdb1212c5c06e66ac65854b9ddf60e77721c4f8b",
        "available_margin": "10",
        "bankruptcy_price": "38644.281818181814742274582386016845703125",
        "bankruptcy_value": "110",
        "entry_nonce": 0,
        "entry_sequence": 1,
        "entryprice": "42508.7099999999991268850862979888916015625",
        "execution_price": "30000",
        "exit_nonce": 0,
        "id": 50,
        "initial_margin": "10",
        "leverage": "10",
        "liquidation_price": "38834.039054470704286359250545501708984375",
        "maintenance_margin": "0.53749999999999997779553950749686919152736663818359375",
        "order_status": "FILLED",
        "order_type": "MARKET",
        "position_type": "LONG",
        "positionsize": "4250871",
        "settlement_price": "0",
        "timestamp": "2024-01-31T11:14:45.575359Z",
        "unrealized_pnl": "0",
        "uuid": "3374714d-8a95-4096-855f-7e2675fe0dc8"
      }
    ]
  }
}
```

### Description

Subscribe Order Book

### Parameters

- **`jsonrpc`** (String, required): The JSON-RPC protocol version. Must be set to `"2.0"`.
- **`method`** (String, required): The name of the method to be invoked.
- **`id`** (Number, required): A unique identifier for the request.
- **`params`** (Object, optional): Additional parameters for the method, if any.

### Method

`subscribe_order_book`

## Subscribe Candle Data

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

- **`jsonrpc`** (String, required): The JSON-RPC protocol version. Must be set to `"2.0"`.
- **`method`** (String, required): The name of the method to be invoked.
- **`id`** (Number, required): A unique identifier for the request.
- **`params`** (Object, optional): Additional parameters for the method, if any.

### Method

`subscribe_candle_data`

## Subscribe Recent Trades

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
      "uuid": "a2369fcf-489b-4ddf-85f6-78ec076401d0",
      "account_id": "0ce417fbbff5e00d731c22cc092ce865b2e509f1bcd1a57f8e7b5f94606adf776d444f896fb7f634d8379ea1954d97dc36a782cb8eaf922b091969ebe652ac96430468db20",
      "position_type": "SHORT",
      "order_status": "FILLED",
      "order_type": "MARKET",
      "entryprice": 51959.0,
      "execution_price": 0.0,
      "positionsize": 7981941580.0,
      "leverage": 1.0,
      "initial_margin": 153620.0,
      "available_margin": 142294.10905172027,
      "timestamp": "2024-02-28T05:00:00.025776783+00:00",
      "bankruptcy_price": 0.0,
      "bankruptcy_value": 0.0,
      "maintenance_margin": 614.48,
      "liquidation_price": 12989749.999999736,
      "unrealized_pnl": 0.0,
      "settlement_price": 0.0,
      "entry_nonce": 0,
      "exit_nonce": 0,
      "entry_sequence": 10
    }
  }
}
```

### Description

Subscribe Recent Trades

### Parameters

- **`jsonrpc`** (String, required): The JSON-RPC protocol version. Must be set to `"2.0"`.
- **`method`** (String, required): The name of the method to be invoked.
- **`id`** (Number, required): A unique identifier for the request.
- **`params`** (Object, optional): Additional parameters for the method, if any.

### Method

`subscribe_recent_trades`

## Subscribe Heartbeat

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

- **`jsonrpc`** (String, required): The JSON-RPC protocol version. Must be set to `"2.0"`.
- **`method`** (String, required): The name of the method to be invoked.
- **`id`** (Number, required): A unique identifier for the request.
- **`params`** (Object, optional): Additional parameters for the method, if any.

### Method

`subscribe_heartbeat`
