# Private API

Required headers for private API:

1. signature
2. datetime
3. relayer-api-key

**Endpoint URL**

`API_ENDPOINT_PRODUCTION = https://twilight.rest`

`API_ENDPOINT_STAGING = https://rpc.twilight.rest`

## Submit Lend Order Zkos

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "e56d9635-b1fd-4e0d-9902-a356ea1a54ae");
myHeaders.append(
  "signature",
  "34b656edeca51b0a2ed85cc804757e163a55cdca671624c5954370bf93dba10a"
);
myHeaders.append("datetime", "1705928223");

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
  "result": "OK",
  "id": 123
}
```

Submit Lend Order Zkos

### HTTP Method

`POST`

### RPC Method

`submit_lend_order`

### Message Parameters

| Params | Data_Type | Values             |
| ------ | --------- | ------------------ |
| data   | string    | Hex from zkos wasm |

## Submit Trade Order Zkos

```javascript
var myHeaders = new Headers();
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append(
  "signature",
  "1de1368e257e5399097af20d182d8462e25a9898bbdb1e8d2ec89eabc3977332"
);
myHeaders.append("datetime", "1706613208905");
myHeaders.append("Content-Type", "application/json");

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
  "result": "OK",
  "id": 123
}
```

Submit Trade Order Zkos

### HTTP Method

`POST`

### RPC Method

`submit_trade_order`

### Message Parameters

| Params | Data_Type | Values             |
| ------ | --------- | ------------------ |
| data   | string    | Hex from zkos wasm |

## Settle Lend Order

```javascript
var myHeaders = new Headers();
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append(
  "signature",
  "1de1368e257e5399097af20d182d8462e25a9898bbdb1e8d2ec89eabc3977332"
);
myHeaders.append("datetime", "1706613208905");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "settle_lend_order",
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
  "result": "OK",
  "id": 123
}
```

Settle Lend Order

### HTTP Method

`POST`

### RPC Method

`settle_lend_order`

### Message Parameters

| Params | Data_Type | Values             |
| ------ | --------- | ------------------ |
| data   | string    | Hex from zkos wasm |

## Settle Trade Order

```javascript
var myHeaders = new Headers();
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append(
  "signature",
  "1de1368e257e5399097af20d182d8462e25a9898bbdb1e8d2ec89eabc3977332"
);
myHeaders.append("datetime", "1706613208905");
myHeaders.append("Content-Type", "application/json");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "settle_trade_order",
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
  "result": "OK",
  "id": 123
}
```

Settle Trade Order

### HTTP Method

`POST`

### RPC Method

`settle_trade_order`

### Message Parameters

| Params | Data_Type | Values             |
| ------ | --------- | ------------------ |
| data   | string    | Hex from zkos wasm |

## Open Order

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append("datetime", "1706618707");
myHeaders.append(
  "signature",
  "1de1368e257e5399097af20d182d8462e25a9898bbdb1e8d2ec89eabc3977332"
);

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "open_orders",
  id: 123,
  params: null,
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
    },
    {
      "account_id": "0c7ccfc25ec0c535a8232e785ddec39972dc48e25ae570e368b9384dc6147ec639b4ea7118b0002894c9d2d9bfcaf72d47a0a49893518a4cfb30a0e81ba34a51684e2f05e9",
      "available_margin": "8.7493463536062794361214400851167738437652587890625",
      "bankruptcy_price": "41368.4380952380961389280855655670166015625",
      "bankruptcy_value": "210",
      "entry_nonce": 0,
      "entry_sequence": 2,
      "entryprice": "43436.860000000000582076609134674072265625",
      "execution_price": "35000",
      "exit_nonce": 0,
      "id": 49,
      "initial_margin": "10",
      "leverage": "20",
      "liquidation_price": "41526.63479923518025316298007965087890625",
      "maintenance_margin": "0.8000000000000000444089209850062616169452667236328125",
      "order_status": "FILLED",
      "order_type": "MARKET",
      "position_type": "LONG",
      "positionsize": "8687372",
      "settlement_price": "0",
      "timestamp": "2024-01-31T11:00:00.041895Z",
      "unrealized_pnl": "0",
      "uuid": "2617f986-7fe6-4497-9e8f-aa1b77de0239"
    }
  ],
  "id": 123
}
```

Open Order

### HTTP Method

`POST`

### RPC Method

`open_orders`

### Message Parameters

`null`

## Order History By Id

```javascript
var myHeaders = new Headers();
myHeaders.append(
  "signature",
  "f4604863b412e0f952a3b59654780a6cca40a752fb29cfa38c30c25b305e69cd"
);
myHeaders.append("Content-Type", "application/json");
myHeaders.append("datetime", "1706618869536");
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "order_history",
  id: 123,
  params: {
    OrderId: "7c9a959b-2ae2-461b-b199-125e1cb5c9c6",
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
  ],
  "id": 123
}
```

Order History By Id

### HTTP Method

`POST`

### RPC Method

`order_history`

### Message Parameters

| Params  | Data_Type | Values        |
| ------- | --------- | ------------- |
| OrderId | string    | UUID of order |

## Order History By Client

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append(
  "signature",
  "75d3ec784cc428a84fa63c52d81b63e0eb64e150158d0a791d0c642762478cc4"
);
myHeaders.append("datetime", "1706619993");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "order_history",
  id: 123,
  params: {
    ClientId: {
      from: "2024-01-13T00:00:00Z",
      to: "2024-02-15T01:00:00Z",
      offset: 0,
      limit: 2,
    },
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
      "account_id": "0c101304bde59a949ab3a00abc0a2b15d71f713ace2f372c789c3f8bcfbedbdb7e12eccdf9d504002e5a775b1e4c756cd601266fa0feae1d3c35500132b23c4062a3f9108f",
      "available_margin": "10",
      "bankruptcy_price": "47185.3555555555576574988663196563720703125",
      "bankruptcy_value": "90",
      "entry_nonce": 0,
      "entry_sequence": 2,
      "entryprice": "42466.8199999999997089616954326629638671875",
      "execution_price": "30000",
      "exit_nonce": 0,
      "id": 51,
      "initial_margin": "10",
      "leverage": "10",
      "liquidation_price": "46918.1825714680235250853002071380615234375",
      "maintenance_margin": "0.5124999999999999555910790149937383830547332763671875",
      "order_status": "FILLED",
      "order_type": "MARKET",
      "position_type": "SHORT",
      "positionsize": "4246682",
      "settlement_price": "0",
      "timestamp": "2024-01-31T11:20:37.427721Z",
      "unrealized_pnl": "0",
      "uuid": "15a35bcd-46c6-42bf-8746-be141dd573b5"
    },
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
  ],
  "id": 123
}
```

Order History By ClientId

### HTTP Method

`POST`

### RPC Method

`order_history`

### Message Parameters

| Params   | Data_Type | Values                           |
| -------- | --------- | -------------------------------- |
| ClientId | object    | <a href="#clientid">ClientId</a> |

### ClientId

| Params | Data_Type | Values             |
| ------ | --------- | ------------------ |
| from   | datetime  | Start time         |
| to     | datetime  | End time           |
| offset | integer   | Page number        |
| limit  | integer   | `Number of entries |

## Trade Volume

```javascript
var myHeaders = new Headers();
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append("Content-Type", "application/json");
myHeaders.append(
  "signature",
  "82df7a176dbe4a4d9e5147bcc7b63512e7e2005be8fc5382b2ab01607f379a0e"
);
myHeaders.append("datetime", "1706620452985");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "trade_volume",
  id: 123,
  params: {
    start: "2023-09-25T00:00:00Z",
    end: "2024-02-26T00:00:00Z",
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
  "result": 4,
  "id": 123
}
```

Trade volume

### HTTP Method

`POST`

### RPC Method

`trade_volume`

### Message Parameters

| Params | Data_Type | Values     |
| ------ | --------- | ---------- |
| from   | datetime  | Start time |
| to     | datetime  | End time   |

## Get Funding Payment

```javascript
const myHeaders = new Headers();
myHeaders.append("relayer-api-key", "7d4fd427-ab9f-4a4d-8163-7faddb0c50e2");
myHeaders.append("Content-Type", "application/json");
myHeaders.append(
  "signature",
  "285d6ca0867a6b20a8eb4a018394f290f8ffb2b0a9b36ba6bd992ad536072932"
);
myHeaders.append("datetime", "1709098568058");

const raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "get_funding_payment",
  id: 123,
  params: {
    id: "a4340b19-cd90-411e-adfc-a3695109d7a2",
  },
});

const requestOptions = {
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
    "funding_payment": "0",
    "funding_rate": "-0.123713",
    "order_id": "a4340b19-cd90-411e-adfc-a3695109d7a2",
    "price": "57208.00"
  },
  "id": 123
}
```

Get Funding Payment

### HTTP Method

`POST`

### RPC Method

`get_funding_payment`

### Message Parameters

| Params | Data_Type | Values        |
| ------ | --------- | ------------- |
| id     | string    | UUID of order |

## Last Order Details

```javascript
var myHeaders = new Headers();
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append("Content-Type", "application/json");
myHeaders.append(
  "signature",
  "d31a4c9191f6ae3b1d5c7d60913cefa516deca4a2ecf26b59602fe2527dd0788"
);
myHeaders.append("datetime", "1706620829597");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "last_order_detail",
  id: 123,
  params: null,
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
    "account_id": "0c101304bde59a949ab3a00abc0a2b15d71f713ace2f372c789c3f8bcfbedbdb7e12eccdf9d504002e5a775b1e4c756cd601266fa0feae1d3c35500132b23c4062a3f9108f",
    "available_margin": "10",
    "bankruptcy_price": "47185.3555555555576574988663196563720703125",
    "bankruptcy_value": "90",
    "entry_nonce": 0,
    "entry_sequence": 2,
    "entryprice": "42466.8199999999997089616954326629638671875",
    "execution_price": "30000",
    "exit_nonce": 0,
    "id": 51,
    "initial_margin": "10",
    "leverage": "10",
    "liquidation_price": "46918.1825714680235250853002071380615234375",
    "maintenance_margin": "0.5124999999999999555910790149937383830547332763671875",
    "order_status": "FILLED",
    "order_type": "MARKET",
    "position_type": "SHORT",
    "positionsize": "4246682",
    "settlement_price": "0",
    "timestamp": "2024-01-31T11:20:37.427721Z",
    "unrealized_pnl": "0",
    "uuid": "15a35bcd-46c6-42bf-8746-be141dd573b5"
  },
  "id": 123
}
```

Last Order Details

### HTTP Method

`POST`

### RPC Method

`last_order_detail`

### Message Parameters

`null`

## Lend Pool Info

```javascript
var myHeaders = new Headers();
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append("Content-Type", "application/json");
myHeaders.append(
  "signature",
  "976d86db2cb544143454ff7e73d79d23c4836a0f3cab41adbf926de8886050aa"
);
myHeaders.append("datetime", "1706620896069");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "lend_pool_info",
  id: 123,
  params: null,
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
    "aggregate_log_sequence": 1,
    "id": 1,
    "last_snapshot_id": 0,
    "nonce": 0,
    "pending_orders": 0,
    "sequence": 0,
    "total_locked_value": "0",
    "total_pool_share": "0"
  },
  "id": 123
}
```

Lend Pool Info

### HTTP Method

`POST`

### RPC Method

`lend_pool_info`

### Message Parameters

`null`

## Trade Order Info

```javascript
var myHeaders = new Headers();
myHeaders.append("Content-Type", "application/json");
myHeaders.append(
  "signature",
  "d940b5080923f12464cb17c5590d2610a716476f8fde792e0f6b5dace951c461"
);
myHeaders.append("datetime", "1706621038691");
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");

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
    "account_id": "0c7ccfc25ec0c535a8232e785ddec39972dc48e25ae570e368b9384dc6147ec639b4ea7118b0002894c9d2d9bfcaf72d47a0a49893518a4cfb30a0e81ba34a51684e2f05e9",
    "available_margin": "10",
    "bankruptcy_price": "41365.2857142857174039818346500396728515625",
    "bankruptcy_value": "209.999999999999971578290569595992565155029296875",
    "entry_nonce": 0,
    "entry_sequence": 1,
    "entryprice": "43433.550000000002910383045673370361328125",
    "execution_price": "35000",
    "exit_nonce": 0,
    "id": 1,
    "initial_margin": "10",
    "leverage": "20",
    "liquidation_price": "41523.4703632887176354415714740753173828125",
    "maintenance_margin": "0.8000000000000000444089209850062616169452667236328125",
    "order_status": "FILLED",
    "order_type": "MARKET",
    "position_type": "LONG",
    "positionsize": "8686710",
    "settlement_price": "0",
    "timestamp": "2024-01-30T11:13:23.386791Z",
    "unrealized_pnl": "0",
    "uuid": "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf"
  },
  "id": 123
}
```

Trade Order Info

### HTTP Method

`POST`

### RPC Method

`trader_order_info`

### Message Parameters

| Params | Data_Type | Values        |
| ------ | --------- | ------------- |
| id     | string    | UUID of order |

## Lend Order Info

```javascript
var myHeaders = new Headers();
myHeaders.append(
  "signature",
  "76fbd9f82340214670ab079163a21a2fb6611945161761f39fc9284179c4a0af"
);
myHeaders.append("Content-Type", "application/json");
myHeaders.append("datetime", "1706621380052");
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");

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
    "account_id": "0c7ccfc25ec0c535a8232e785ddec39972dc48e25ae570e368b9384dc6147ec639b4ea7118b0002894c9d2d9bfcaf72d47a0a49893518a4cfb30a0e81ba34a51684e2f05e9",
    "available_margin": "10",
    "bankruptcy_price": "41365.2857142857174039818346500396728515625",
    "bankruptcy_value": "209.999999999999971578290569595992565155029296875",
    "entry_nonce": 0,
    "entry_sequence": 1,
    "entryprice": "43433.550000000002910383045673370361328125",
    "execution_price": "35000",
    "exit_nonce": 0,
    "id": 1,
    "initial_margin": "10",
    "leverage": "20",
    "liquidation_price": "41523.4703632887176354415714740753173828125",
    "maintenance_margin": "0.8000000000000000444089209850062616169452667236328125",
    "order_status": "FILLED",
    "order_type": "MARKET",
    "position_type": "LONG",
    "positionsize": "8686710",
    "settlement_price": "0",
    "timestamp": "2024-01-30T11:13:23.386791Z",
    "unrealized_pnl": "0",
    "uuid": "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf"
  },
  "id": 123
}
```

Lend Order Info

### HTTP Method

`POST`

### RPC Method

`lend_order_info`

### Message Parameters

| Params | Data_Type | Values        |
| ------ | --------- | ------------- |
| id     | string    | UUID of order |

## Unrealized Pnl Pubkey

```javascript
var myHeaders = new Headers();
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append("Content-Type", "application/json");
myHeaders.append(
  "signature",
  "1ce608176c6c7de4789438fee598e4f48499e14b2b5fd7e99c57b0abf3481307"
);
myHeaders.append("datetime", "1706621523812");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "unrealized_pnl",
  id: 123,
  params: {
    id: "0c3eb16783ccdbee855e0babf6d130101e7d66089bac20484606e52bf507d90e3a5049a3379b8afc47068d2508dfd71fe92adab7a5ad682fbbbb9b401158e62d42aa64cb22",
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
    "order_ids": [],
    "pnl": 0.0
  },
  "id": 123
}
```

Unrealized Pnl Pubkey

### HTTP Method

`POST`

### RPC Method

`unrealized_pnl`

### Message Parameters

| Params | Data_Type | Values          |
| ------ | --------- | --------------- |
| id     | string    | User account id |

## Unrealized Pnl OrderId

```javascript
var myHeaders = new Headers();
myHeaders.append(
  "signature",
  "97895f93e4b6e7c3d1ade05ef9cc63975deb8f6dee5b968d4889a70da92a97cc"
);
myHeaders.append("Content-Type", "application/json");
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append("datetime", "1706621687195");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "unrealized_pnl",
  id: 123,
  params: {
    OrderId: "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf",
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
    "order_ids": ["49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf"],
    "pnl": 0.0
  },
  "id": 123
}
```

Unrealized Pnl OrderId

### HTTP Method

`POST`

### RPC Method

`unrealized_pnl`

### Message Parameters

| Params  | Data_Type | Values        |
| ------- | --------- | ------------- |
| OrderId | string    | UUID of order |

## Unrealized Pnl All

```javascript
var myHeaders = new Headers();
myHeaders.append("relayer-api-key", "1f9500c6-6371-41a4-bdef-5fb7f7709186");
myHeaders.append("Content-Type", "application/json");
myHeaders.append(
  "signature",
  "c56d25fb3998af90814716128f93169fce5ee143597b5e5c8cbc6a1264a72398"
);
myHeaders.append("datetime", "1706621762s");

var raw = JSON.stringify({
  jsonrpc: "2.0",
  method: "unrealized_pnl",
  id: 123,
  params: "All",
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
    "order_ids": [
      "49251ba1-30eb-4545-9e4e-1bdf2ec9c3cf",
      "2617f986-7fe6-4497-9e8f-aa1b77de0239",
      "3374714d-8a95-4096-855f-7e2675fe0dc8",
      "15a35bcd-46c6-42bf-8746-be141dd573b5"
    ],
    "pnl": 0.0
  },
  "id": 123
}
```

Unrealized Pnl All

### HTTP Method

`POST`

### RPC Method

`unrealized_pnl`

### Message Parameters

`All`
