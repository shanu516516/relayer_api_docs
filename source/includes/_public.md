# Public API

**Endpoint URL**

`API_ENDPOINT_PRODUCTION = https://twilight.rest`

`API_ENDPOINT_STAGING = https://rpc.twilight.rest`

## Candle Data

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "candle_data",
  id: 123,
  params: {
    interval: "ONE_DAY",
    since: "2023-09-25T00:01:00.0Z",
    limit: 20,
    offset: 0,
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api", requestOptions)
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
      "btc_volume": "234549776",
      "close": "42988.8099999999976716935634613037109375",
      "end": "2024-01-31T00:00:59.907514Z",
      "high": "43867.3799999999973806552588939666748046875",
      "low": "42686",
      "open": "43493.2600000000020372681319713592529296875",
      "resolution": "1 day",
      "start": "2024-01-30T11:00:17.892660Z",
      "trades": 27,
      "usd_volume": "1172748.880000000048312358558177947998046875"
    },
    {
      "btc_volume": "173740820",
      "close": "42507.33999999999650754034519195556640625",
      "end": "2024-01-31T10:51:37.281376Z",
      "high": "43107.7699999999967985786497592926025390625",
      "low": "42462.639999999999417923390865325927734375",
      "open": "42990.6200000000026193447411060333251953125",
      "resolution": "1 day",
      "start": "2024-01-31T00:01:00.908144Z",
      "trades": 20,
      "usd_volume": "868704.100000000034924596548080444335937500"
    }
  ],
  "id": 123
}
```

Candle data (Kline data: 1min, 5min, 15min, 30min, 1hr, 4hr, 8hr, 12hr, 24hr)

### HTTP Method

`POST`

### RPC Method

`candle_data`

### Message Parameters

| Params   | Data_Type | Values                                                                                                                          |
| -------- | --------- | ------------------------------------------------------------------------------------------------------------------------------- |
| interval | string    | `ONE_MINUTE`, `FIVE_MINUTE`, `FIFTEEN_MINUTE`, `THIRTY_MINUTE`, `ONE_HOUR`, `FOUR_HOUR`, `EIGHT_HOUR`, `TWELVE_HOUR`, `ONE_DAY` |
| since    | datetime  | Start time                                                                                                                      |
| limit    | integer   | Number of entries                                                                                                               |
| offset   | integer   | Page number                                                                                                                     |

## Get Funding Rate

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "get_funding_rate",
  id: 123,
  params: null,
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "id": 23,
    "price": "42643.9899999999979627318680286407470703125",
    "rate": "0",
    "timestamp": "2024-01-31T10:00:00.075603Z"
  },
  "id": 123
}
```

Current funding rate

### HTTP Method

`POST`

### RPC Method

`get_funding_rate`

### Message Parameters

`null`

## Historical Funding Rate

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "historical_funding_rate",
  id: 123,
  params: {
    from: "2024-01-13T00:00:00Z",
    to: "2024-02-15T01:00:00Z",
    limit: 3,
    offset: 0,
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api", requestOptions)
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
      "id": 1,
      "price": "43537.7399999999979627318680286407470703125",
      "rate": "0.125",
      "timestamp": "2024-01-30T12:00:00.027626Z"
    },
    {
      "id": 2,
      "price": "43436.860000000000582076609134674072265625",
      "rate": "0.125",
      "timestamp": "2024-01-30T13:00:00.026145Z"
    },
    {
      "id": 3,
      "price": "43287.610000000000582076609134674072265625",
      "rate": "0.125",
      "timestamp": "2024-01-30T14:00:00.070450Z"
    }
  ],
  "id": 123
}
```

Historical funding rate

### HTTP Method

`POST`

### RPC Method

`historical_funding_rate`

### Message Parameters

| Params | Data_Type | Values            |
| ------ | --------- | ----------------- |
| from   | datetime  | Start time        |
| to     | datetime  | End time          |
| limit  | integer   | Number of entries |
| offset | integer   | Page number       |

## Open Limit Order

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "open_limit_orders",
  id: 123,
  params: null,
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "ask": [],
    "bid": []
  },
  "id": 123
}
```

Open Limit Order

### HTTP Method

`POST`

### RPC Method

`open_limit_orders`

### Message Parameters

`null`

## Recent Trade Order

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "recent_trade_orders",
  id: 123,
  params: null,
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api", requestOptions)
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
      "positionsize": "8686710",
      "price": "35000",
      "side": "LONG",
      "timestamp": "2024-01-30T11:13:23.386791Z"
    },
    {
      "positionsize": "8687372",
      "price": "35000",
      "side": "LONG",
      "timestamp": "2024-01-30T12:42:59.466955Z"
    }
  ],
  "id": 123
}
```

Recent Trade Order

### HTTP Method

`POST`

### RPC Method

`recent_trade_orders`

### Message Parameters

`null`

## Position Size

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "position_size",
  id: 123,
  params: null,
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "total": "17374082",
    "total_long": "17374082",
    "total_short": "0"
  },
  "id": 123
}
```

Position Size

### HTTP Method

`POST`

### RPC Method

`position_size`

### Message Parameters

`null`

## Btc Usd Price

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "btc_usd_price",
  id: 123,
  params: null,
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "id": 45551,
    "price": "42447.0400000000008731149137020111083984375",
    "timestamp": "2024-01-31T11:01:30.907388Z"
  },
  "id": 123
}
```

Btc Usd Price

### HTTP Method

`POST`

### RPC Method

`btc_usd_price`

### Message Parameters

`null`

## Historical Price

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "historical_price",
  id: 123,
  params: {
    from: "2024-01-14T00:00:00Z",
    to: "2024-01-31T01:00:00Z",
    limit: 3,
    offset: 0,
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api", requestOptions)
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
      "id": 1,
      "price": "43493.2600000000020372681319713592529296875",
      "timestamp": "2024-01-30T11:00:17.892660Z"
    },
    {
      "id": 2,
      "price": "43493.2699999999967985786497592926025390625",
      "timestamp": "2024-01-30T11:00:18.894869Z"
    },
    {
      "id": 3,
      "price": "43493.2600000000020372681319713592529296875",
      "timestamp": "2024-01-30T11:00:19.895641Z"
    }
  ],
  "id": 123
}
```

Historical BTC price

### HTTP Method

`POST`

### RPC Method

`historical_price`

### Message Parameters

| Params | Data_Type | Values            |
| ------ | --------- | ----------------- |
| from   | datetime  | Start time        |
| to     | datetime  | End time          |
| limit  | integer   | Number of entries |
| offset | integer   | Page number       |

## Login

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

> The result from the above endpoint looks like this:

```json
{
  "api_key": "7d4fd427-ab9f-4a4d-8163-7faddb0c50e2",
  "api_secret": "dab81c56-2cb1-4bfb-b58d-26e14d1262d6"
}
```

Endpoint to get `api_key` and `api_secret` for private API endpoints.

### HTTP Method

`POST`

### Message Body

| Params          | Data_Type | Values                                                                                      |
| --------------- | --------- | ------------------------------------------------------------------------------------------- |
| account_address | string    | Twilight address                                                                            |
| data            | string    | Message string                                                                              |
| signature       | object    | `{"pub_key": {"type": "tendermint/PubKeySecp256k1", "value": string}, "signature": string}` |

<aside class="notice">
You must add <code>api_key</code> and <code>api_secret</code> in your private API endpoint header.
</aside>

## Server Time

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "server_time",
  id: 123,
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api", requestOptions)
  .then((response) => response.text())
  .then((result) => console.log(result))
  .catch((error) => console.log("error", error));
```

> The result from the above endpoint looks like this:

```json
{
  "jsonrpc": "2.0",
  "result": "2024-01-31T11:05:37.546616309Z",
  "id": 123
}
```

Server time

### HTTP Method

`POST`

### RPC Method

`server_time`

### Message Parameters

`null`

## Transaction Hash

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "transaction_hashes",
  id: 123,
  params: {
    AccountId: {
      id: "0c3eb16783ccdbee855e0babf6d130101e7d66089bac20484606e52bf507d90e3a5049a3379b8afc47068d2508dfd71fe92adab7a5ad682fbbbb9b401158e62d42aa64cb22",
    },
  },
});

var requestOptions = {
  method: "POST",
  headers: myHeaders,
  body: raw,
  redirect: "follow",
};

fetch("API_ENDPOINT/api", requestOptions)
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
      "account_id": "0c3eb16783ccdbee855e0babf6d130101e7d66089bac20484606e52bf507d90e3a5049a3379b8afc47068d2508dfd71fe92adab7a5ad682fbbbb9b401158e62d42aa64cb22",
      "datetime": "1708363831398559",
      "id": 4,
      "order_id": "83216790-d1c6-40d9-a70e-712d5d81cecd",
      "order_status": "FILLED",
      "order_type": "MARKET",
      "output": "01000000010000002a000000000000003138363065656636336564656531303738313738623361646236336539663836393231636161313662358a00000000000000306333656231363738336363646265653835356530626162663664313330313031653764363630383962616332303438343630366535326266353037643930653361353034396133333739623861666334373036386432353038646664373166653932616461623761356164363832666262626239623430313135386536326434326161363463623232010000000000000082000000000000000000000000000000671ca31e9c9274ef4cf068098060878c960afaa5d7c2a205d3cd3f38f858e00f0104000000000000000300000001000000386567000000000000000000000000000000000000000000000000000000000002000000010000000000000001000000000000000000000000000000671ca31e9c9274ef4cf068098060878c960afaa5d7c2a205d3cd3f38f858e00f03000000010000009ccb0000000000000000000000000000000000000000000000000000000000000300000001000000010000000000000000000000000000000000000000000000000000000000000001000000",
      "tx_hash": "8E291447D61EBC7E0AF5BB006576190E117516CA9A29358554C108718586FF58"
    },
    {
      "account_id": "0c3eb16783ccdbee855e0babf6d130101e7d66089bac20484606e52bf507d90e3a5049a3379b8afc47068d2508dfd71fe92adab7a5ad682fbbbb9b401158e62d42aa64cb22",
      "datetime": "1708411999147047",
      "id": 8,
      "order_id": "83216790-d1c6-40d9-a70e-712d5d81cecd",
      "order_status": "SETTLED",
      "order_type": "MARKET",
      "output": null,
      "tx_hash": "C5680F08C4D315241924BB1B4F172B12ABB44A6A49DA0CBAD69552D43A9EBA4A"
    }
  ],
  "id": 123
}
```

Transaction Hash

### HTTP Method

`POST`

### RPC Method

`transaction_hashes`

### Message Parameters

| Params | Data_Type | Values          |
| ------ | --------- | --------------- |
| id     | string    | User account id |
