# Twilight Relayer API Documentation

<h1 style="font-size: 48px; font-weight: bold; vertical-align: middle;"><p align="center">
 <img src="source/images/image.avif" alt="Twilight Pool" width="38"> Twilight
</p></h1>

<p align="center">
  <strong>Complete API documentation for Twilight's Relayer service</strong>
</p>

<p align="center">
  <a href="https://docs.twilight.rest">📖 Read Full Documentation</a>
</p>

---

## Overview

The Twilight Relayer API provides comprehensive access to decentralized trading, lending, and market data functionality built on Twilight's revolutionary Bitcoin reserve architecture. Twilight combines the security of distributed Bitcoin custody with the flexibility of twilight pools - Alternative Trading Systems that allow selective information revelation for sophisticated DeFi strategies.

### 🏦 What is Twilight?

Twilight serves as an antidote to rising walled gardens of centralized custody, maintaining Bitcoin's core value of distributed trust while providing advanced DeFi capabilities. Key properties include:

- **Always Inflight Bitcoin** - Your Bitcoin is constantly moving between validators, preventing any single party from claiming custody
- **Bitcoin-Derived Security** - Direct security inheritance from Bitcoin with inscribed exit hatches for permissionless fund recovery
- **Efficient Capital Requirements** - Secures $1B with only $10M in stake (100:1 ratio) through distributed trust assumptions
- **Distributed Signing** - All validators share signatures on the distributed ledger, eliminating centralized aggregation servers
- **Fast Confirmations** - 2-block deposit confirmation using validity gadgets for predictive finality

### 📡 API Categories

This documentation covers three main API categories:

- **Public API** - Market data, pricing, and public information (no authentication required)
- **Private API** - Trading, lending, and account management (requires authentication)
- **WebSocket API** - Real-time data streams and live updates

## 🌐 API Endpoints

### Production

- **REST API**: `https://twilight.rest`
- **WebSocket**: `wss://twilight.rest/ws`

### Staging

- **REST API**: `https://rpc.twilight.rest`
- **WebSocket**: `wss://rpc.twilight.rest/ws`

## 📚 Complete Documentation

**👉 [Visit docs.twilight.rest](https://docs.twilight.rest) for the complete interactive API documentation**

## 🏊 Twilight Pools Architecture

### What is a Twilight Pool?

Inspired by traditional financial markets, Twilight brings the concept of "twilight pools" to DeFi. In traditional finance:

- **Lit Pools** - All trade information is immediately public
- **Dark Pools** - Trade information is completely hidden
- **Twilight Pools** - Partial trade information is revealed after pre-defined time periods

Twilight pools operate as Alternative Trading Systems (ATS) where selective information like price, trade direction, or partial bids/offers may be revealed strategically. This approach bridges the gap between complete transparency and privacy in DeFi.

### 🎯 Developer Flexibility

Twilight enables developers to build applications with configurable privacy levels:

- **Full Transparency** - Traditional public distributed ledger approach
- **Selective Privacy** - Obfuscate specific parameters based on application needs
- **Time-Delayed Revelation** - Strategic information disclosure after predetermined periods
- **Partial Information Sharing** - Reveal only necessary trade details while protecting sensitive data

This flexibility allows for sophisticated trading strategies while maintaining the distributed custody benefits of the underlying Bitcoin reserve system.

### 🪃 Boomerang Protocol

The Boomerang protocol is Twilight's core innovation for maintaining distributed Bitcoin custody:

- **Bitcoin Fragments** - Your Bitcoin is split into fragments that continuously move between validators
- **Fragment Addresses** - Each fragment has unique addressing for tracking and verification
- **Automatic Sweeping** - Fragments are automatically swept and redistributed to prevent centralization
- **Clearing Mechanism** - Efficient settlement of trades and positions across the distributed network
- **Refund Snapshots** - Regular snapshots ensure funds can always be recovered

### 🕒 Validity Gadget

Twilight's Validity Gadget provides predictive finality and enhanced security:

- **Chaintip Attestation** - Validators attest to Bitcoin blockchain state for faster confirmations
- **2-Block Confirmation** - Achieve deposit confirmation in just 2 blocks instead of traditional 100+ blocks
- **Predictive Finality** - Advanced algorithms predict transaction finality on the longest chain
- **Liveness Guarantees** - Ensures the system remains operational even with validator failures

### 🔐 Security Model

Twilight's unique security approach:

- **1-of-n Trust Assumption** - System operates securely even if only one honest validator exists
- **n-of-n Malicious Resistance** - Designed to handle scenarios where all validators turn malicious
- **Inscribed Exit Hatches** - Bitcoin-native exit mechanisms that can be triggered permissionlessly
- **Distributed Validation** - No single point of failure in the validation process

### 💰 Capital Efficiency

- **100:1 Security Ratio** - Secure $1B in Bitcoin with only $10M in validator stake
- **Minimal Collateral Requirements** - Efficient capital utilization compared to traditional systems
- **Dynamic Stake Adjustment** - Validator stakes adjust based on network conditions and security needs

## 🔓 Public API Features

The Public API provides access to market data and general information without authentication:

### Available Endpoints

- **Candle Data** - OHLCV data with multiple timeframes (1min to 1day)
- **Funding Rates** - Current and historical funding rates
- **Order Book** - Current limit orders
- **Recent Trades** - Latest trade executions
- **Position Data** - Open position information
- **Price Data** - Current and historical BTC/USD prices
- **Server Time** - Synchronized server timestamp
- **Transaction Hash** - Blockchain transaction information

### Example: Get Candle Data

```javascript
const response = await fetch("https://twilight.rest/api", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({
    jsonrpc: "2.0",
    method: "candle_data",
    id: 123,
    params: {
      interval: "ONE_HOUR",
      since: "2024-01-01T00:00:00.0Z",
      limit: 100,
      offset: 0,
    },
  }),
});

const data = await response.json();
console.log(data.result);
```

## 🔐 Private API Features

The Private API requires authentication and provides trading and account management functionality:

### Authentication Headers

- `relayer-api-key` - Your API key
- `signature` - Request signature
- `datetime` - Unix timestamp

### Available Endpoints

- **Submit Trade Orders** - Place market and limit orders using zkOS
- **Submit Lend Orders** - Create lending positions
- **Settle Orders** - Settle trade and lending positions
- **Order Management** - View open orders and order history
- **Trade Volume** - Get trading volume statistics
- **Funding Payments** - Access funding payment history
- **Account Data** - Retrieve account-specific information

### Example: Submit Trade Order

```javascript
const response = await fetch("https://twilight.rest/api/private", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "relayer-api-key": "your-api-key",
    signature: "your-signature",
    datetime: "1706613208905",
  },
  body: JSON.stringify({
    jsonrpc: "2.0",
    method: "submit_trade_order",
    id: 123,
    params: {
      data: "0x...", // Hex data from zkOS WASM
    },
  }),
});

const result = await response.json();
console.log(result);
```

## 🔄 WebSocket API Features

Real-time data streaming for live market updates:

### Available Subscriptions

- **Live Price Data** - Real-time price updates
- **Order Book** - Live order book changes
- **Candle Data** - Real-time OHLCV updates
- **Recent Trades** - Live trade executions
- **Heartbeat** - Connection health monitoring

### Example: Subscribe to Live Price Data

```javascript
const socket = new WebSocket("wss://twilight.rest/ws");

socket.onopen = () => {
  socket.send(
    JSON.stringify({
      jsonrpc: "2.0",
      method: "subscribe_live_price_data",
      id: 123,
      params: null,
    })
  );
};

socket.onmessage = (event) => {
  const data = JSON.parse(event.data);
  console.log("Live price update:", data.params.result);
};
```

## 🛠 Technical Details

### JSON-RPC Protocol

All APIs use JSON-RPC 2.0 protocol with the following structure:

```json
{
  "jsonrpc": "2.0",
  "method": "method_name",
  "id": 123,
  "params": {
    /* method parameters */
  }
}
```

### zkOS Integration

Private API endpoints utilize zkOS (Zero-Knowledge Operating System) for:

- **Privacy-preserving transactions**
- **Cryptographic proof generation**
- **Secure order execution**
- **Decentralized settlement**

### Supported Timeframes

- `ONE_MINUTE` - 1-minute candles
- `FIVE_MINUTE` - 5-minute candles
- `FIFTEEN_MINUTE` - 15-minute candles
- `THIRTY_MINUTE` - 30-minute candles
- `ONE_HOUR` - 1-hour candles
- `FOUR_HOUR` - 4-hour candles
- `EIGHT_HOUR` - 8-hour candles
- `TWELVE_HOUR` - 12-hour candles
- `ONE_DAY` - Daily candles

## 🚀 Getting Started

1. **Explore Public APIs** - Start with public endpoints to understand the data structure
2. **Get API Credentials** - Contact Twilight Protocol for private API access
3. **Review Documentation** - Visit [docs.twilight.rest](https://docs.twilight.rest) for detailed guides
4. **Test Integration** - Use staging endpoints for development and testing
5. **Implement zkOS** - Integrate zkOS WASM for private operations

## 📖 Full Documentation

This README provides a high-level overview. For complete API documentation including:

- **Detailed parameter specifications**
- **Response schemas and examples**
- **Authentication guides**
- **Error handling**
- **Rate limiting information**
- **SDK and integration examples**

**Visit: [https://docs.twilight.rest](https://docs.twilight.rest)**

## 🔗 Links

- **API Documentation**: [docs.twilight.rest](https://docs.twilight.rest)
- **Protocol Documentation**: [docs.twilight.org](https://docs.twilight.org)
- **Twilight Protocol**: [twilight.rest](https://twilight.rest)
- **Support**: Contact through official channels

---

<p align="center">
  <em>Built with ❤️ by the Twilight team</em>
</p>
