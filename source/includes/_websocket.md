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
- **`params`** (Object, optional): Additional parameters for the method, if any. When using `subscribe_candle_data`, the `params` object must include an `interval` field whose value can be one of `ONE_MINUTE`, `FIVE_MINUTE`, `FIFTEEN_MINUTE`, `THIRTY_MINUTE`, `ONE_HOUR`, `FOUR_HOUR`, `EIGHT_HOUR`, `TWELVE_HOUR`, `ONE_DAY`, `ONE_DAY_CHANGE`.

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

### Unsubscribing from a WebSocket Stream

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

Available unsubscribe methods:

- `unsubscribe_live_price_data`
- `unsubscribe_order_book`
- `unsubscribe_candle_data`
- `unsubscribe_recent_trades`
- `unsubscribe_heartbeat`
