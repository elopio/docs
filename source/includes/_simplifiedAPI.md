Simplified API
===========
<aside class="notice">The Simplified API section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

<aside class="warning">The Simplified API provides an easy way to make transactions and query for information within Augur. This API is simplified in the sense that single requests to this API can be used to fetch a large amount of data, without the need for complicated RPC batch queries. However, many of the functions in this API require a connection to an <a href="#augur-node">Augur Node</a>, which has been indicated in their descriptions. Please read the <a href="#augur-node">Augur Node</a> section before using these functions.</aside>

Accounts Functions
-----------------
```javascript
// Accounts Simplified API Examples:

augur.accounts.approveAugur({
  address: "0x0000000000000000000000000000000000000b0b", 
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function(result) { console.log(result); },
  onSuccess: function(result) { console.log("Approval was successful."); },
  onFailed: function(result) { console.log("Approval failed due to error:", result); }
});
// example output:
"Approval was successful."

augur.accounts.getAccountTransferHistory({
  account: "0x0000000000000000000000000000000000000b0b",
  token: null,
  isSortDescending: false
}, function (error, result) {
  console.log(result);
});
// example output:
[
  {
    transactionHash: "0x00000000000000000000000000000000000000000000000000000000deadbeef",
    logIndex: 0,
    creationBlockNumber: 1400000,
    blockHash: "0x1400000",
    creationTime: 1506473474,
    sender: " 0x0000000000000000000000000000000000000b0b",
    recipient: " 0x000000000000000000000000000000000000d00d",
    token: " 0x1000000000000000000000000000000000000000",
    value: 10,
    symbol: "shares",
    marketId: " 0x0000000000000000000000000000000000000001",
    outcome: 0,
  },
  {
    transactionHash: "0x00000000000000000000000000000000000000000000000000000000d3adb33f",
    logIndex: 0,
    creationBlockNumber: 1400001,
    blockHash: "0x1400001",
    creationTime: 1506473500,
    sender: " 0x000000000000000000000000000000000000d00d",
    recipient: " 0x0000000000000000000000000000000000000b0b",
    token: " 0x1000000000000000000000000000000000000000",
    value: 2,
    symbol: "shares",
    marketId: " 0x0000000000000000000000000000000000000001",
    outcome: 0,
  },
  {
    transactionHash: "0x00000000000000000000000000000000000000000000000000000000deadb33f",
    logIndex: 1,
    creationBlockNumber: 1400001,
    blockHash: "0x1400001",
    creationTime: 1506473500,
    sender: " 0x0000000000000000000000000000000000000b0b",
    recipient: " 0x000000000000000000000000000000000000d00d",
    token: " 0x7a305d9b681fb164dc5ad628b5992177dc66aec8",
    value: 47,
    symbol: "REP",
    marketId: null,
    outcome: null,
  }
]
```
### augur.accounts.approveAugur(p)

Internally, Augur uses an ERC-20 token called Cash as a wrapper for ETH. Many of Augur's transactions require Augur to be able to spend Cash on behalf of the account executing the transaction. However, the account must first approve Augur to spend that amount of Cash on its behalf. This function calls the function `augur.api.Cash.approve` to approve Augur to spend up to `augur.constants.ETERNAL_APPROVAL_VALUE` Cash on behalf of the account. `augur.constants.ETERNAL_APPROVAL_VALUE` is equal to 2^256 - 1, or the maximum amount of Cash that can be approved. This value is used so that `augur.api.Cash.approve` does not have to be called multiple times in order to execute multiple transactions.

#### **Parameters:**

* **`p`** (Object) Parameters object.
  * **`p.address`** (string) Ethereum address of the account making the approval.
  * **`p.meta`** (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
  * **`p.onSent`**  (function) Called if/when the transaction is broadcast to the network.
  * **`p.onSuccess`**  (function) Called if/when the transaction is sealed and confirmed.
  * **`p.onFailed`**  (function) Called if/when the transaction fails.

#### **Returns:**

* Does not return a value.

### augur.accounts.getAccountTransferHistory(p, callback)

Returns the token transfers made to or from a specific Ethereum address.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.account`**  (string) Ethereum address of the account for which to get transfer history, as a 20-byte hexadecimal string.
    * **`p.token`**  (string) &lt;optional> Contract address of the token contract by which to limit the history results, as a 20-byte hexadecimal string.
    * **`p.earliestCreationTime`**  (number) &lt;optional> Earliest timestamp, in seconds, at which to truncate history results. (This timestamp is when the block on the Ethereum blockchain containing the transfer was created.)
    * **`p.latestCreationTime`**  (number) &lt;optional> Latest timestamp, in seconds, at which to truncate history results. (This timestamp is when the block on the Ethereum blockchain containing the transfer was created.)
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort transfer history.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort transfers in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of transfers to return.
    * **`p.offset`**  (string) &lt;optional> Number of transfers to truncate from the beginning of the history results.
* **`callback`** (function) Called after the account transfer history has been retrieved.

#### **Returns:**

* (Array.&lt;<a href="#AccountTransfer">AccountTransfer</a>>) Array representing the account's transfer history.

Assets Functions
----------------
```javascript
// Assets Simplified API Examples:

augur.assets.sendEther({
  etherToSend: "0.001",
  from: "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
  to: "0x40485264986740c8fb3d11e814bd94cf86012d29",
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function(result) { console.log(result); },
  onSuccess: function(result) { console.log(result); },
  onFailed: function(result) { console.log(result); }
});

augur.assets.sendReputation({
  universe: "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
  reputationToSend: "0.001",
  _to: "0x40485264986740c8fb3d11e814bd94cf86012d29",
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function(result) { console.log(result); },
  onSuccess: function(result) { console.log(result); },
  onFailed: function(result) { console.log(result); }
});
```
### augur.assets.sendEther(p)

Sends Ether to a specified Ethereum address.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.etherToSend`**  (string) Amount of Ether to send, as a base-10 string.
    * **`p.from`**  (string) Ethereum address of the sender, as a 20-byte hexadecimal string.
    * **`p.to`**  (string) Ethereum address of the recipient, as a 20-byte hexadecimal string.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when the transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when the transaction is sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when the transaction fails.

#### **Returns:**

* Return value cannot be obtained because Ethereum nodes [discard](#transaction-return-values) transaction return values.

### augur.assets.sendReputation(p)

Sends [REP](#rep) to a specified Ethereum address. This function will trigger a [`TokensTransferred`](#TokensTransferred) event if the REP is successfully sent.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) The universe of Reputation to use.
    * **`p.reputationToSend`**  (string) Amount of Reputation to send, as a base-10 string.
    * **`p._to`**  (string) Ethereum address of the recipient, as a 20-byte hexadecimal string.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when the transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when the transaction is sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when the transaction fails.

#### **Returns:**

* Return value cannot be obtained because Ethereum nodes [discard](#transaction-return-values) transaction return values.

Augur Node Functions
--------------------
```javascript
// Augur Node Simplified API Examples:

augur.augurNode.connect(
  "ws://127.0.0.1:9001",
  function(error, wsTransport) {
    console.log("Connected to Augur Node!");
    console.log("WebSocket transport:");
    console.log(wsTransport);
  }
);
// example output:
"Connected to Augur Node!"
"WebSocket transport:"
{
  "address": "ws://127.0.0.1:9001",
  "timeout": 100,
  "workQueue": [],
  "awaitingPump": false,
  "connected": true,
  "backoffMilliseconds": 1,
  "nextListenerToken": 1,
  "reconnectListeners": { ... },
  "disconnectListeners": { ... },
  "webSocketClient": { ... }
}

augur.augurNode.getContractAddresses(
  function(error, result) {
    console.log(result);
  }
);
// example output:
{
  "version": "4.7.0-47",
  "net_version": "4",
  "addresses": {
    "Controller": "0x392be0a9d1ab1bde2931c2ddf1d722f9e13b6085",
    "Universe": "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
    "Augur": "0x852684b374fe03ab77d06931f1b2831028fd58f5",
    "LegacyReputationToken": "0x097c198dcc997086d1555ad9ae6f52375cfb58d0",
    "CancelOrder": "0x389c0b3f0d51cfba9e4d214712a1142f5685814d",
    "Cash": "0xd2ee83a8a2a904181ccfddd8292f178614062aa0",
    "ClaimTradingProceeds": "0x8aa774927fb928ee1df0d0d3f94c8217658e0bce",
    "CompleteSets": "0xbf749b00e42751dba8e0872e66d3ba854f6c2632",
    "CreateOrder": "0xdadc071ecc3b7e97b139d2ef692defdc398c8211",
    "FillOrder": "0x0c77f6af7b3b5fed8ca980414a97c62da283098a",
    "Order": "0x4811d582f64e68e657bec21834012af38ef093bc",
    "Orders": "0x1ba5cf0ce546bf7d7943c4d4fb21ea59f6bb0eee",
    "OrdersFetcher": "0xb9f2cf78542de87ba7542d92e2937d0eedec5feb",
    "ShareToken": "0x925bee44fec28deb228d2251e1a9d32f7c73ebed",
    "Trade": "0x0dec7fd04933b8673cef99b64978113065b03926",
    "TradingEscapeHatch": "0x157a8998f5470a2be3917aab31d334109f56c30c"
  }
}

augur.augurNode.submitRequest(
  "getMarketsInCategory",
  {
    universe: "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
    category: "sports"
  },
  function(error, result) {
    console.log(result);
  }
);
// example output:
[
  "0x55132e5b02c94bef39983f31392091c626a4b8f0",
  "0x5e44a67cfef54f6e5af5c9aa762204e85cf1c319",
  "0xbf8efd9b940cddeb6eb31f28dae47245cd6ee6a1"
]

augur.augurNode.subscribeToEvent(
  "TokensTransferred",
  function(error, result) {
    console.log(error);
    console.log(result);
    console.log("Tokens transferred!");
  },
  function(error, subscriptionId) {
    console.log("Subscribed to event! Subscription ID:", subscriptionId);
  }
);
// example output:
"Subscribed to event! Subscription ID: 8cac9a4b-f5b8-4fb1-be50-5aa16dc99e67"

augur.augurNode.unsubcribeFromEvent(
  "8cac9a4b-f5b8-4fb1-be50-5aa16dc99e67",
  function(error) {
    if (!error) {
      console.log("Unsubscribe successful!");
    }
  }
);
// example output:
"Unsubscribed from 8cac9a4b-f5b8-4fb1-be50-5aa16dc99e67"
"Unsubscribe successful!"

augur.augurNode.unsubscribeFromAllEvents(
  function() {
    console.log("Unsubscribed from all events!");
  }
);
// example output:
"Unsubscribed from 32a81c04-8b35-47fa-a6d0-b12a34145108"
"Unsubscribed from a6354b5d-64a7-49db-acd9-ff56dcc54b4d"
"Unsubscribed from all events!"
```
### augur.augurNode.connect(augurNodeUrl, callback)

Establishes a connection to an [Augur Node](#augur-node).

#### **Parameters:**

* **`augurNodeUrl`**  (string) WebSocket URL of an Augur Node to connect to.
* **`callback`**  (function) Called after attempting to connect to an Augur Node.

#### **Returns:**

* (<a href="#WsTransport">WsTransport</a>) WebSocket transfer object containing information about the WebSocket connection to the Augur Node.

### augur.augurNode.getContractAddresses(callback)

Returns the version and contract address set from an [Augur Node](#augur-node).

This function will fail if:

* A connection to an Augur Node has not been established.

#### **Parameters:**

* **`callback`**  (function) Called after the version and contract addresses have been retrieved.

#### **Returns:**

* (<a href="#ContractAddresses">ContractAddresses</a>) An object containing the version and contract addresses.

### augur.augurNode.submitRequest(method, params, callback)

Submits an RPC request to an [Augur Node](#augur-node).

This function will fail if:

* A connection to an Augur Node has not been established.

#### **Parameters:**

* **`method`**  (string) Method name to call.
* **`params`**  (Object) Parameters to pass into `method`.
* **`callback`**  (function) Called once the Augur Node responds to the RPC request.

#### **Returns:**

* (Data type can vary based on the method being called.) Result from the RPC request.

### augur.augurNode.subscribeToEvent(eventName, subscriptionCallback, onComplete)

Listens to an [Augur Node](#augur-node) for when a specific [event](#event-types) occurs.

This function will fail if:

* A connection to an Augur Node has not been established.

#### **Parameters:**

* **`eventName`**  (string) Type of event to listen for.
* **`subscriptionCallback`**  (function) Called whenever the Augur Node detects a new occurrence of the specified event.
* **`onComplete`**  (function) Called after attempting to set up the subscription to listen on the Augur Node.

#### **Returns:**

* (string) ID of the subscription that was created.

### augur.augurNode.unsubscribeFromEvent(subscriptionId, callback)

Stops the specified subscription from listening on an Augur Node.

This function will fail if:

* A connection to an Augur Node has not been established.

#### **Parameters:**

* **`subscriptionId`**  (string) Subscription ID to stop listening.
* **`callback`**  (function) Called after attempting to stop the subscription from listening on the Augur Node.

#### **Returns:**

* This function does not return a value.

### augur.augurNode.unsubscribeFromAllEvents(callback)

Stops the current subscriptions from listening on an Augur Node.

This function will fail if:

* A connection to an Augur Node has not been established.

#### **Parameters:**

* **`callback`**  (function) Called after attempting to stop all subscriptions from listening on the Augur Node.

#### **Returns:**

* This function does not return a value.

Connect Function
----------------
```javascript
// Connect Simplified API Examples:

augur.connect({
  ethereumNode: {
    httpAddresses: [
      "http://127.0.0.1:8545", // local HTTP address for Geth node
      "https://rinkeby.ethereum.nodes.augur.net" // hosted HTTP address for Geth node on the Ethereum Rinkeby test network
    ],
    wsAddresses: [
      "ws://127.0.0.1:8546", // local WebSocket address for Geth node
      "wss://ws9000.augur.net" // hosted WebSocket address for Geth node
    ]
  },
  augurNode: "ws://127.0.0.1:9001" // local WebSocket address for an Augur Node
}, function (error, connectionInfo) {
  console.log(connectionInfo);
});
// example output:
connecting to augur-node: ws://127.0.0.1:9001
connecting to ethereum-node: {"httpAddresses":["http://127.0.0.1:8545","https://rinkeby.ethereum.nodes.augur.net"],"wsAddresses":["ws://127.0.0.1:8546","wss://websocket-rinkeby.ethereum.nodes.augur.net"]}
connected to augur
Web3: not connected
Sync: http://127.0.0.1:8545
HTTP: http://127.0.0.1:8545
WS: ws://127.0.0.1:8546
IPC: not connected
connected to ethereum
{
  augurNode: "ws://127.0.0.1:9001",
  ethereumNode: {
    abi: { ... },
    blockNumber: "0x184e24",
    coinbase: "0x40485264986740c8fb3d11e814bd94cf86012d29",
    contracts: { ... },
    gasPrice: 20000000000,
    networkId: "4"
    rpc: { ... }
  }
}
```
### augur.connect(connectOptions, callback)

Connects augur.js to an Ethereum node and an [Augur Node](#augur-node).

#### **Parameters:**

* **`connectOptions`** ([ConnectOptions](#ConnectOptions)) Options used to connect to an Ethereum node and an Augur Node.
* **`callback`** (function) &lt;optional> Callback function.

#### **Returns:**

* (Object) Transport object containing information about the Augur Node and Ethereum node connections.

Create Market Functions
----------------
```javascript
// Create Market Simplified API Examples:

var _extraInfo = {
  "resolutionSource": "http://www.nasdaq.com/symbol/msft",
  "tags": [ "Stocks", "Microsoft" ],
  "longDescription": ""
};
augur.createMarket.createBinaryMarket({
  universe: "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
  _endTime: 1546300799,
  _feePerEthInWei: 1193046,
  _denominationToken: "0xd2ee83a8a2a904181ccfddd8292f178614062aa0",
  _designatedReporterAddress: "0x01114f4bda09ed6c6715cf0baf606b5bce1dc96a",
  _topic: "stocks",
  _description: "Will Microsoft stock (MSFT) be below $50 at any point during 2018?",
  _extraInfo: JSON.stringify(_extraInfo),
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  "callReturn": "0xc841ee153e45e74074eae9685e815d08dee965eb",
  "blockHash": "0xf5aab811242a73fe433995fdf2212548385b81acef4d6e5634faec74014f39df",
  "blockNumber": 1657080,
  "from": "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
  "gas": "0x5b8d80",
  "gasPrice": "0x1a13b8600",
  "hash": "0xb55fa7c2e9d43fc9d11badb33f1d571539859059bdd7447594748733b112f7d2",
  "input": "0x59381d2e000000000000000000000000000000000000000000000000000000005c2aad7f0000000000000000000000000000000000000000000000000000000000123456000000000000000000000000d2ee83a8a2a904181ccfddd8292f178614062aa00000000000000000000000008fa56abe36d8dc76cf85fecb6a3026733e0a12ac73746f636b73000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000e00000000000000000000000000000000000000000000000000000000000000160000000000000000000000000000000000000000000000000000000000000004d57696c6c2074686520446f77204a6f6e657320496e647573747269616c2041766572616765206578636565642032372c30303020617420616e7920706f696e7420647572696e6720323031383f000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000085227b5c227265736f6c7574696f6e536f757263655c223a5c2268747470733a2f2f7777772e6d61726b657477617463682e636f6d2f696e76657374696e672f696e6465782f646a69615c222c5c22746167735c223a5b5c2253746f636b735c222c5c22444a49415c225d2c5c226c6f6e674465736372697074696f6e5c223a5c225c227d22000000000000000000000000000000000000000000000000000000",
  "nonce": "0x43",
  "to": "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
  "transactionIndex": "0x0",
  "value": "0xa8c0ff92d4c000",
  "v": "0x2b",
  "r": "0x256cedf5b3ef2f49f6eef99d10ca53ba0484da0e9cbf4434bb3042f90fb376de",
  "s": "0x4161413a76a41468272e85ea7af8362393ea886f743b9882f42bea39fc6ec0b9",
  "timestamp": 1516925924,
  "gasFees": "0.022312465"
}

var _extraInfo = {
  "resolutionSource": "http://www.espn.com",
  "tags": ["college football", "football"],
  "outcomeNames": ["Georgia", "Florida"],
  "longDescription": ""
};
augur.createMarket.createCategoricalMarket({
  universe: "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
  _endTime: 1540875600,
  _feePerEthInWei: 1193046,
  _denominationToken: "0xd2ee83a8a2a904181ccfddd8292f178614062aa0",
  _designatedReporterAddress: "0x01114f4bda09ed6c6715cf0baf606b5bce1dc96a",
  _outcomes: ["outcome1","outcome2"],
  _topic: "sports",
  _description: "Who will win the University of Georgia vs. University of Florida football game in 2018?",
  _extraInfo: JSON.stringify(_extraInfo),
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  "callReturn": "0xc841ee153e45e74074eae9685e815d08dee965eb",
  "blockHash": "0xf5aab811242a73fe433995fdf2212548385b81acef4d6e5634faec74014f39df",
  "blockNumber": 1657080,
  "from": "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
  "gas": "0x5b8d80",
  "gasPrice": "0x1a13b8600",
  "hash": "0xb55fa7c2e9d43fc9d11badb33f1d571539859059bdd7447594748733b112f7d2",
  "input": "0x59381d2e000000000000000000000000000000000000000000000000000000005c2aad7f0000000000000000000000000000000000000000000000000000000000123456000000000000000000000000d2ee83a8a2a904181ccfddd8292f178614062aa00000000000000000000000008fa56abe36d8dc76cf85fecb6a3026733e0a12ac73746f636b73000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000e00000000000000000000000000000000000000000000000000000000000000160000000000000000000000000000000000000000000000000000000000000004d57696c6c2074686520446f77204a6f6e657320496e647573747269616c2041766572616765206578636565642032372c30303020617420616e7920706f696e7420647572696e6720323031383f000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000085227b5c227265736f6c7574696f6e536f757263655c223a5c2268747470733a2f2f7777772e6d61726b657477617463682e636f6d2f696e76657374696e672f696e6465782f646a69615c222c5c22746167735c223a5b5c2253746f636b735c222c5c22444a49415c225d2c5c226c6f6e674465736372697074696f6e5c223a5c225c227d22000000000000000000000000000000000000000000000000000000",
  "nonce": "0x43",
  "to": "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
  "transactionIndex": "0x0",
  "value": "0xa8c0ff92d4c000",
  "v": "0x2b",
  "r": "0x256cedf5b3ef2f49f6eef99d10ca53ba0484da0e9cbf4434bb3042f90fb376de",
  "s": "0x4161413a76a41468272e85ea7af8362393ea886f743b9882f42bea39fc6ec0b9",
  "timestamp": 1516925924,
  "gasFees": "0.022312465"
}

var _extraInfo = {
  "resolutionSource": "http://forecast.weather.gov",
  "tags": ["San Francisco", "weather"],
  "longDescription": ""
};
augur.createMarket.createScalarMarket({
  universe: "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
  _endTime: 1530507600,
  _feePerEthInWei: 1193046,
  _denominationToken: "0xd2ee83a8a2a904181ccfddd8292f178614062aa0",
  _designatedReporterAddress: "0x01114f4bda09ed6c6715cf0baf606b5bce1dc96a",
  _minPrice: -10,
  _maxPrice: 120,
  _numTicks: 10,
  _topic: "temperature",
  _description: "High temperature (in degrees Fahrenheit) in San Francisco, California, on July 1, 2018",
  _extraInfo: JSON.stringify(_extraInfo),
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  "callReturn": "0x008f89903707dbaaed3c42202ec00a94673e96db",
  "blockHash": "0xfbb0b8ced5dab79a5ef58dd0eb45c28e5124832e5c27c0479ea8d6947fef4ed2",
  "blockNumber": 1657371,
  "from": "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
  "gas": "0x5b8d80",
  "gasPrice": "0x4a817c800",
  "hash": "0x7f52926eac45ec6483cc19b5da795b7425fd3caf191c3eeb208627f4ea0e43b9",
  "input": "0x72d61dfa000000000000000000000000000000000000000000000000000000005b39b1500000000000000000000000000000000000000000000000000000000000123456000000000000000000000000d2ee83a8a2a904181ccfddd8292f178614062aa00000000000000000000000008fa56abe36d8dc76cf85fecb6a3026733e0a12acffffffffffffffffffffffffffffffffffffffffffffffff7538dcfb761800000000000000000000000000000000000000000000000000068155a43676e00000000000000000000000000000000000000000000000000000000000000013d62074656d7065726174757265000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000014000000000000000000000000000000000000000000000000000000000000001c00000000000000000000000000000000000000000000000000000000000000056486967682074656d70657261747572652028696e20646567726565732046616872656e686569742920696e2053616e204672616e636973636f2c2043616c69666f726e69612c206f6e204a756c7920312c203230313800000000000000000000000000000000000000000000000000000000000000000000000000000000007a227b5c227265736f6c7574696f6e536f757263655c223a5c22687474703a2f2f666f7265636173742e776561746865722e676f765c222c5c22746167735c223a5b5c2253616e204672616e636973636f5c222c5c22776561746865725c225d2c5c226c6f6e674465736372697074696f6e5c223a5c225c227d22000000000000",
  "nonce": "0x45",
  "to": "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
  "transactionIndex": "0x0",
  "value": "0xa8c0ff92d4c000",
  "v": "0x2b",
  "r": "0xa8dee275a834a3dc9b2f36c0f7bb9564557d6943623013d772b966ce7045e029",
  "s": "0x14355beeb51c6565f923e2f2e4133261765cc6357a3d8038b18a4d25e874fec8",
  "timestamp": 1516930289,
  "gasFees": "0.0618542"
}

augur.createMarket.getMarketCreationCost({
    universe: "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
}, function (error, result) {
  console.log(result);
});
// example output:
{
  designatedReportNoShowReputationBond: "0.174840291341145834",
  etherRequiredToCreateMarket: "0.025"
}

augur.createMarket.getMarketCreationCostBreakdown({
    universe: "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
}, function (error, result) {
  console.log(result);
});
// example output:
{
  validityBond: "0.01",
  designatedReportNoShowReputationBond: "0.174840291341145834",
  targetReporterGasCosts: "0.015"
}

augur.createMarket.getMarketFromCreateMarketReceipt(
  "0xa50c02cf3de82139fc6f66a9c1726f59b93b4a94725bddbf9ecae0c38edc4f06", 
  function (error, result) {
    console.log(result);
  }
);
// example output: 
"0x5e05e281a4564077985debdb91159a825a6774d3"
```
### augur.createMarket.createBinaryMarket(p)

Creates a [Binary Market](#binary-market) in a specified [Universe](#universe). This function will trigger a [`MarketCreated`](#MarketCreated) event and [`TokensTransferred`](#TokensTransferred) event if the [Market](#market) is created successfully.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Universe in which to create a Binary Market.
    * **`p._endTime`**  (number) Binary Market expiration timestamp, in seconds.
    * **`p._feePerEthInWei`**  (string) &lt;optional> Amount of wei per ether settled that goes to the [Market Creator](#market-creator), as a base-10 string.
    * **`p._denominationToken`**  (string) Ethereum address of the token used as this Market's currency.
    * **`p._designatedReporterAddress`**  (string) Ethereum address of this Market's [Designated Reporter](#designated-reporter).
    * **`p._topic`**  (string) The topic (category) to which this Market belongs, as a UTF8 string. Note: This string is limited to 32-characters.
    * **`p._description`**  (string) Description of the Market, as a UTF8 string.
    * **`p._extraInfo`**  ([ExtraInfo](#ExtraInfo)) &lt;optional> Extra info which will be converted to JSON and logged to the chain in the `MarketCreated` event.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when the createBinaryMarket transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when the createBinaryMarket transaction is sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when the createBinaryMarket transaction fails.

#### **Returns:**

* (Object) Object containing information about the Market creation transaction. The `callReturn` property of this object contains the Ethereum address of the Market that was created.

### augur.createMarket.createCategoricalMarket(p)

Creates a [Categorical Market](#categorical-market) in a specified [Universe](#universe). This function will trigger a [`MarketCreated`](#MarketCreated) event and [`TokensTransferred`](#TokensTransferred) event if the [Market](#market) is created successfully.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Universe in which to create a Categorical Market.
    * **`p._endTime`**  (number) Categorical Market expiration timestamp, in seconds.
    * **`p._feePerEthInWei`**  (string) &lt;optional> Amount of wei per ether settled that goes to the [Market Creator](#market-creator), as a base-10 string.
    * **`p._denominationToken`**  (string) Ethereum address of the token used as this Market's currency.
    * **`p._designatedReporterAddress`**  (string) Ethereum address of this Market's [Designated Reporter](#designated-reporter).
    * **`p._outcomes`**  (Array.&lt;string>) Array of names for all possible outcomes for the Market event.
    * **`p._topic`**  (string) The topic (category) to which this Market belongs, as a UTF8 string. Note: This string is limited to 32-characters.
    * **`p._description`**  (string) Description of the Market, as a UTF8 string.
    * **`p._extraInfo`**  ([ExtraInfo](#ExtraInfo)) &lt;optional> Extra info which will be converted to JSON and logged to the chain in the `MarketCreated` event.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when the createCategoricalMarket transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when the createCategoricalMarket transaction is sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when the createCategoricalMarket transaction fails.

#### **Returns:**

* (Object) Object containing information about the Market creation transaction. The `callReturn` property of this object contains the Ethereum address of the Market that was created.

### augur.createMarket.createScalarMarket(p)

Creates a [Scalar Market](#scalar-market) in a specified [Universe](#universe). This function will trigger a [`MarketCreated`](#MarketCreated) event and [`TokensTransferred`](#TokensTransferred) event if the [Market](#market) is created successfully.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Universe in which to create this market.
    * **`p._endTime`**  (number) Market expiration timestamp, in seconds.
    * **`p._feePerEthInWei`**  (string) &lt;optional> Amount of wei per ether settled that goes to the [Market Creator](#market-creator), as a base-10 string.
    * **`p._denominationToken`**  (string) Ethereum address of the token used as this Market's currency.
    * **`p._minPrice`**  (string) Minimum display (non-normalized) price for this Market, as a base-10 string.
    * **`p._maxPrice`**  (string) Maximum display (non-normalized) price for this Market, as a base-10 string.
    * **`p._designatedReporterAddress`**  (string) Ethereum address of this Market's [Designated Reporter](#designated-reporter).
    * **`p._topic`**  (string) The topic (category) to which this Market belongs, as a UTF8 string. Note: This string is limited to 32-characters.
    * **`p._description`**  (string) Description of the Market, as a UTF8 string.
    * **`p.tickSize`**  (string) &lt;optional> The [Tick](#tick) size for this Market, as a base-10 string.
    * **`p._extraInfo`**  (ExtraInfo) &lt;optional> Extra info which will be converted to JSON and logged to the chain in the `MarketCreated` event.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when the createScalarMarket transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when the createScalarMarket transaction is sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when the createScalarMarket transaction fails.

#### **Returns:**

* (Object) Object containing information about the Market creation transaction. The `callReturn` property of this object contains the Ethereum address of the Market that was created.

### augur.createMarket.getMarketCreationCost(p, callback)

Retrieves the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) amount and total Ether required to create a new [Market](#market).

Note: This function will send a transaction if needed to create the current [Fee Window](#fee-window).

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) [Universe](#universe) on which to create this Market.
* **`callback`** (function) Called after the Market creation cost has been looked up.

#### **Returns:**

* (<a href="#MarketCreationCost">MarketCreationCost</a>) Costs of creating a new Market.

<!-- TODO: Add link to augur.createMarket.getMarketCreationCost -->
### augur.createMarket.getMarketCreationCostBreakdown(p, callback)

Similar to `augur.createMarket.getMarketCreationCost`, but provides more detail about the ether costs required to create a new [Market](#market). These Ether costs are broken down by the gas cost paid to the [First Public Reporter](#first-public-reporter) and the cost of the [Validity Bond](#validity-bond).

Note: This function will send a transaction if needed to create the current [Fee Window](#fee-window).

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) [Universe](#universe) on which to create this Market.
* **`callback`** (function) Called when all Market creation costs have been looked up.

#### **Returns:**

* (<a href="#MarketCreationCostBreakdown">MarketCreationCostBreakdown</a>) Cost breakdown for creating a new Market.

### augur.createMarket.getMarketFromCreateMarketReceipt(transactionHash, callback)

Uses a transaction hash to query an Augur Node for the address of the [Market](#market) created by that transaction.

#### **Parameters:**

* **`transactionHash`** (string) Hash returned in the receipt of the transaction that created the Market, as a 32-byte hexadecimal string.
* **`callback`** (function) Called when all Market creation costs have been looked up.

#### **Returns:**

* (string) Ethereum contract address of the Market that was created in the transaction with the specified hash, as a 20-byte hexadecimal value.

Generate Contracts API Function
-------------------------------
```javascript
// No examples for Generate Contracts Simplified API
```
### augur.generateContractApi(functionsAbi)

Generates a set of JavaScript bindings for the Solidity ABIs passed in.

#### **Parameters:**

* **`functionsAbi`** (Object) Parameters object.

#### **Returns:**

* (Array) Two-dimensional array of contracts APIs, keyed by contract name and function name.

Markets Functions
----------------
```javascript
// Markets Simplified API Examples:

augur.markets.getCategories({
  universe: "0x000000000000000000000000000000000000000b",
  sortBy: "popularity",
  isSortDescending: true
}, function (error, result) {
  console.log(result);
});
// example output:
[
  { category: "finance", popularity: 12345 },
  { category: "politics", popularity: 5000 },
  { category: "ethereum", popularity: 1000 },
  { category: "augur", popularity: 500 },
]

augur.markets.getMarketPriceHistory({
  marketId: "0x0000000000000000000000000000000000000001",
  sortBy: null,
  isSortDescending: null,
  limit: null,
  offset: null,
}, function (error, result) {
  console.log(result);
});
// example output:
{
  0: [{
    price: 5.5,
    amount: 0.2,
    timestamp: 1506474500,
  }],
}

augur.markets.getMarkets({
  universe: "0x000000000000000000000000000000000000000b",
  reportingState: "DESIGNATED_REPORTING",
  designatedReporter: "0x000000000000000000000000000000000000d00d",
}, function (error, result) {
  console.log(result);
});
// example output:
[
  "0x0000000000000000000000000000000000000003",
]

augur.markets.getMarketsClosingInDateRange({
  universe: "0x000000000000000000000000000000000000000b",
  earliestClosingTime: 1506573450,
  latestClosingTime: 1506573470,
  limit: 10,
}, function (error, result) {
  console.log(result);
});
// example output:
[
  "0x0000000000000000000000000000000000000001",
]

augur.markets.getMarketsCreatedByUser({
  universe: "0x000000000000000000000000000000000000000b",
  creator: "0x0000000000000000000000000000000000000b0b",
}, function (error, result) {
  console.log(result);
});
// example output:
[
  "0x0000000000000000000000000000000000000012",
  "0x0000000000000000000000000000000000000013",
  "0x0000000000000000000000000000000000000014",
  "0x0000000000000000000000000000000000000015",
  "0x0000000000000000000000000000000000000016",
  "0x0000000000000000000000000000000000000017",
  "0x0000000000000000000000000000000000000018",
  "0x0000000000000000000000000000000000000019",
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000011",
  "0x0000000000000000000000000000000000000211",
  "0x0000000000000000000000000000000000000222",
]

augur.markets.getMarketsInCategory({
  universe: "0x000000000000000000000000000000000000000b",
  category: "augur",
}, function (error, result) {
  console.log(result);
});
// example output:
[
  "0x0000000000000000000000000000000000000012",
  "0x0000000000000000000000000000000000000013",
  "0x0000000000000000000000000000000000000014",
  "0x0000000000000000000000000000000000000015",
  "0x0000000000000000000000000000000000000016",
  "0x0000000000000000000000000000000000000017",
  "0x0000000000000000000000000000000000000018",
  "0x0000000000000000000000000000000000000019",
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000003",
  "0x0000000000000000000000000000000000000011",
  "0x0000000000000000000000000000000000000211",
  "0x0000000000000000000000000000000000000222",
]

augur.markets.getMarketsInfo({
  marketIds: [
    "0x0000000000000000000000000000000000000001",
    "0x0000000000000000000000000000000000000002",
  ],
}, function (error, result) {
  console.log(result);
});
// example output:
[
  0: {
    id: "0x0000000000000000000000000000000000000001",
    universe: "0x000000000000000000000000000000000000000b",
    marketType: "categorical",
    numOutcomes: 8,
    minPrice: 0,
    maxPrice: 1,
    cumulativeScale: "1",
    author: "0x0000000000000000000000000000000000000b0b",
    creationTime: 1506473474,
    creationBlock: 1400000,
    creationFee: 10,
    settlementFee: "0.03",
    reportingFeeRate: 0.02,
    marketCreatorFeeRate: 0.01,
    marketCreatorFeesCollected: 0,
    initialReportSize: null,
    category: "test category",
    tags: ["test tag 1", "test tag 2"],
    volume: 0,
    outstandingShares: 0,
    reportingState: "DESIGNATED_REPORTING",
    feeWindow: "0x1000000000000000000000000000000000000000",
    endDate: 1506573470,
    finalizationTime: null,
    description: "This is a categorical test market created by b0b.",
    extraInfo: null,
    designatedReporter: "0x0000000000000000000000000000000000000b0b",
    designatedReportStake: 10,
    resolutionSource: "http://www.trusted-third-party.com",
    numTicks: 10000,
    tickSize: "0.0001",
    consensus: null,
    outcomes: [{
      id: 0,
      volume: 100,
      price: 0.125,
      name: "outcome 0",
    }, {
      id: 1,
      volume: 100,
      price: 0.125,
      name: "outcome 1",
    }, {
      id: 2,
      volume: 100,
      price: 0.125,
      name: "outcome 2",
    }, {
      id: 3,
      volume: 100,
      price: 0.125,
      name: "outcome 3",
    }, {
      id: 4,
      volume: 100,
      price: 0.125,
      name: "outcome 4",
    }, {
      id: 5,
      volume: 100,
      price: 0.125,
      name: "outcome 5",
    }, {
      id: 6,
      volume: 100,
      price: 0.125,
      name: "outcome 6",
    }, {
      id: 7,
      volume: 100,
      price: 0.125,
      name: "outcome 7",
    }],
  },
  1: {
    id: "0x0000000000000000000000000000000000000002",
    universe: "0x000000000000000000000000000000000000000b",
    marketType: "binary",
    numOutcomes: 2,
    minPrice: 0,
    maxPrice: 1,
    cumulativeScale: "1",
    author: "0x0000000000000000000000000000000000000b0b",
    creationTime: 1506480000,
    creationBlock: 1400100,
    creationFee: 10,
    settlementFee: "0.03",
    reportingFeeRate: 0.02,
    marketCreatorFeeRate: 0.01,
    marketCreatorFeesCollected: 0,
    category: "test category",
    tags: ["test tag 1", "test tag 2"],
    volume: 0,
    outstandingShares: 0,
    reportingState: "DESIGNATED_REPORTING",
    feeWindow: "0x1000000000000000000000000000000000000000",
    endDate: 1506573480,
    finalizationTime: null,
    description: "This is a binary test market created by b0b.",
    extraInfo: null,
    designatedReporter: "0x0000000000000000000000000000000000000b0b",
    designatedReportStake: 10,
    resolutionSource: "http://www.trusted-third-party.com",
    numTicks: 10000,
    consensus: null,
    outcomes: [{
      id: 0,
      volume: 1000,
      price: 0.5,
      name: "outcome 0",
    }, {
      id: 1,
      volume: 1000,
      price: 0.5,
      name: "outcome 1",
    }]
  }
]

augur.markets.getUnclaimedMarketCreatorFees({
  marketIds: [
    "0x0000000000000000000000000000000000000001",
    "0x0000000000000000000000000000000000000002"
  ]
}, function(error, result) {
  console.log(result);
});
// example output:
{
  "0x0000000000000000000000000000000000000001": 0,
  "0x0000000000000000000000000000000000000002": 0
}
```
### augur.markets.getCategories(p, callback)

Returns the Market Categories in a specific [Universe](#universe).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**
* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Contract address of the Universe from which to retrieve the categories, as a 20-byte hexadecimal string.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the categories.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort categories in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of categories to return.
    * **`p.offset`**  (string) &lt;optional> Number of categories to truncate from the beginning of the results.
* **`callback`** (function) Called after the categories have been retrieved.

#### **Returns:**
* (Array.&lt;<a href="#Category">Category</a>>) Array representing the categories in the specified Universe.

### augur.markets.getMarketPriceHistory(p, callback)

Returns the prices and timestamps of a specific [Market's](#market) [Outcomes](#outcomes) over time.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.marketId`**  (string) Market contract address for which to look up orders, as a 20-byte hexadecimal string.
* **`callback`** (function) Called after the price time-series has been received and parsed.

#### **Returns:**

* (<a href="#MarketPriceTimeSeries">MarketPriceTimeSeries</a>) The Market's price time-series, keyed by outcome ID.

### augur.markets.getMarkets(p, callback)

Returns an array of [Markets](#market) in a specific [Universe](#universe).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Contract address of the Universe from which to get transfer history.
    * **`p.creator`** (string) &lt;optional> Ethereum address of a [Market Creator](#market-creator) by which to filter the returned results, as a 20-byte hexadecimal string.
    * **`p.category`** (string) &lt;optional> Market category by which to filter the returned results.
    * **`p.reportingState`** (string) &lt;optional> [REPORTING_STATE](#REPORTING_STATE) by which to filter the returned results.
    * **`p.feeWindow`** (string) &lt;optional> Ethereum address of a [Fee Window](#fee-window) by which to filter the returned results, as a 20-byte hexadecimal string.
    * **`p.designatedReporter`** (string) &lt;optional> Ethereum address of a [Designated Reporter](#designated-reporter) by which to filter the returned results, as a 20-byte hexadecimal string.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`p.offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* (Array.&lt;string>) Array of Market addresses in the Universe, as hexadecimal strings.

### augur.markets.getMarketsClosingInDateRange(p, callback)

Returns the [Markets](#market) closing between a given time range in a specific [Universe](#universe).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Contract address of the Universe from which to get the Markets, as a 20-byte hexadecimal string.
    * **`p.earliestClosingTime`**  (number) Earliest Market close timestamp at which to truncate Market results, in seconds.
    * **`p.latestClosingTime`**  (number) Latest Market close timestamp at which to truncate Market results, in seconds.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`p.offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* (Array.&lt;string>) Array of closing Market addresses, as hexadecimal strings.

### augur.markets.getMarketsCreatedByUser(p, callback)

Returns the [Markets](#market) created by a specific user, as well as the total amount of fees earned so far by that user.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Contract address of the [Universe](#universe) from which to get the Markets, as a 20-byte hexadecimal string.
    * **`p.creator`**  (string) Ethereum address of the [Market Creator](#market-creator), as a 20-byte hexadecimal string.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`p.offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* (Array.&lt;<a href="#MarketsContractAddressRow">MarketsContractAddressRow</a>>) Array of [MarketsContractAddressRows](#MarketsContractAddressRow), as hexadecimal strings.

### augur.markets.getMarketsInCategory(p, callback)

Returns the [Markets](#market) within a specific category.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Contract address of the [Universe](#universe) from which to get the Markets, as a 20-byte hexadecimal string.
    * **`p.category`**  (string) Name of the category from which to get the Markets.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`p.offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* (Array.&lt;string>) Array of Market addresses in the specified category, as hexadecimal strings.

### augur.markets.getMarketsInfo(p, callback)

Returns information about [Markets](#markets) that are stored on-contract. The returned result includes basic information about the Markets as well as information about each Market [Outcome](#outcome). It does not include Order Book information; however the function `augur.trading.getOrders` can be used to get information about [Orders](#order) for the specified Market.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.marketIds`**  (Array.&lt;string>) Contract addresses of the Markets for which to get details, as hexadecimal strings.
* **`callback`** (function) Called after the Market info has been retrieved.

#### **Returns:**

* (Array.&lt;<a href="#MarketInfo">MarketInfo</a>>) Array of MarketInfo objects.

### augur.markets.getUnclaimedMarketCreatorFees(p, callback)

Returns the amount of unclaimed [Creator Fees](#creator-fee) in a set of [Markets](#market). Fees are only available on [Finalized Markets](#finalized-market).

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.marketIds`**  (Array.<string>) Contract addresses of the Markets for which to get unclaimed Creator Fees, as 20-byte hexadecimal values.
* **`callback`** (function) Called after the Market Creator Fee information has been retrieved.

#### **Returns:**

* (Object) Market Creator Fees, in [attoETH](#atto-prefix), keyed by Market ID.

Reporting Functions
-------------
```javascript
// Reporting Simplified API Examples:

augur.reporting.finalizeMarket({
  market: "0x0000000000000000000000000000000000000011",
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function(result) { console.log(result); },
  onSuccess: function(result) { console.log(result); },
  onFailed: function(result) { console.log(result); }
});
// This function does not return a value.

var secondsInFeeWindow = 604800;
var currentTimestamp = 1518648436;
augur.reporting.getCurrentPeriodProgress(secondsInFeeWindow, currentTimestamp);
// example output:
99.27843915343915

augur.reporting.getDisputeInfo({
  marketIds: [
    "0x0000000000000000000000000000000000000211",
    "0x0000000000000000000000000000000000000011",
  ],
  account: "0x0000000000000000000000000000000000000021",
}, function (error, result) {
  console.log(result);
});
// example output:
[
  {
    marketId: "0x0000000000000000000000000000000000000211",
    stakes: [
      {
        isInvalid: false,
        payout: [
          10000,
          0,
        ],
        accountStakeComplete: "0",
        accountStakeIncomplete: "0",
        totalStake: "0",
        completedStake: "0",
        size: "204",
        currentStake: "0",
        tentativeWinning: false,
      },
      {
        isInvalid: false,
        payout: [
          0,
          10000,
        ],
        accountStakeComplete: "0",
        totalStake: "102",
        completedStake: "102",
        tentativeWinning: true,
      },
      {
        isInvalid: true,
        payout: [
          5000,
          5000,
        ],
        accountStakeComplete: "0",
        accountStakeIncomplete: "0",
        totalStake: "20",
        completedStake: "0",
        size: "204",
        currentStake: "20",
        tentativeWinning: false,
      },
    ],
    disputeRound: 0,
  },
  {
    marketId: "0x0000000000000000000000000000000000000011",
    stakes: [
      {
        isInvalid: false,
        payout: [
          0,
          2,
        ],
        accountStakeComplete: "0",
        accountStakeIncomplete: "17",
        totalStake: "40102",
        completedStake: "102",
        size: "60000",
        currentStake: "40000",
        tentativeWinning: false,
      },
      {
        isInvalid: true,
        payout: [
          1,
          1,
        ],
        accountStakeComplete: "500",
        totalStake: "20000",
        completedStake: "20000",
        tentativeWinning: true,
      },
    ],
    disputeRound: 1,
  },
]

augur.reporting.getDisputeTokens({
  universe: "0x000000000000000000000000000000000000000b",
  account: "0x0000000000000000000000000000000000000021",
  disputeTokenState: "UNFINALIZED",
}, function (error, result) {
  console.log(result);
});
// example output:
{
  "0x0000000000000000001000000000000000000001": {
    disputeToken: "0x0000000000000000001000000000000000000001",
    marketId: "0x0000000000000000000000000000000000000011",
    payout0: 0,
    payout1: 2,
    payout2: null,
    payout3: null,
    payout4: null,
    payout5: null,
    payout6: null,
    payout7: null,
    isInvalid: false,
    balance: 17,
    winningToken: null,
    tentativeWinning: 0,
    claimed: false,
    reportingState: "CROWDSOURCING_DISPUTE",
  },
}

augur.reporting.getFeeWindowCurrent({
  universe: "0x000000000000000000000000000000000000000b",
  reporter: "0x0000000000000000000000000000000000000b0b",
}, function (error, result) {
  console.log(result);
});
// example output:
{
  endBlockNumber: null,
  endTime: 1511657473,
  feeWindow: "0x2000000000000000000000000000000000000000",
  feeWindowId: 457,
  startBlockNumber: 1500001,
  startTime: 1509065473,
  totalStake: "26",
  universe: "0x000000000000000000000000000000000000000b",
}

augur.reporting.getFeeWindows({
  universe: "0x000000000000000000000000000000000000000b",
  account: "0x0000000000000000000000000000000000000021",
  includeCurrent: true,
}, function (error, result) {
  console.log(result);
});
// example output:
{
  "0x1000000000000000000000000000000000000000": {
    startTime: 1506473473,
    endTime: 1509065473,
    balance: 100,
    expectedFees: 100 * 1000 / 300,
  },
  "0x2000000000000000000000000000000000000000": {
    startTime: 1509065473,
    endTime: 1511657473,
    balance: 500,
    expectedFees: 500 * 2000 / 1100,
  },
}

augur.reporting.getInitialReporters({
  reporter: "0x0000000000000000000000000000000000000b0b",
}, function (error, result) {
  console.log(result);
});
// example output:
{
  "0x0000000000000000000000000000000000abe123": {
    marketId: "0x0000000000000000000000000000000000000011",
    reporter: "0x0000000000000000000000000000000000000b0b",
    amountStaked: 102,
    initialReporter: "0x0000000000000000000000000000000000abe123",
    redeemed: 0,
    isDesignatedReporter: 0,
    repBalance: 2000,
  },
  "0x0000000000000000000000000000000000abe321": {
    marketId: "0x0000000000000000000000000000000000000211",
    reporter: "0x0000000000000000000000000000000000000b0b",
    amountStaked: 102,
    initialReporter: "0x0000000000000000000000000000000000abe321",
    redeemed: 0,
    isDesignatedReporter: 1,
    repBalance: 2000,
  },
}

augur.reporting.getReportingHistory({
  reporter: "0x0000000000000000000000000000000000000021",
  universe: "0x000000000000000000000000000000000000000b",
}, function (error, result) {
  console.log(result);
});
// example output:
{
  "0x000000000000000000000000000000000000000b": {
    "0x0000000000000000000000000000000000000011": [{
      transactionHash: "0x0000000000000000000000000000000000000000000000000000000000000D00",
      logIndex: 0,
      creationBlockNumber: 1400051,
      blockHash: "0x1400051",
      creationTime: 1506474500,
      marketId: "0x0000000000000000000000000000000000000011",
      feeWindow: "0x1000000000000000000000000000000000000000",
      payoutNumerators: [0, 2],
      amountStaked: 17,
      crowdsourcerId: "0x0000000000000000001000000000000000000001",
      isCategorical: false,
      isScalar: false,
      isInvalid: false,
      isSubmitted: true,
    }],
    "0x0000000000000000000000000000000000000019": [{
      transactionHash: "0x0000000000000000000000000000000000000000000000000000000000000D03",
      logIndex: 0,
      creationBlockNumber: 1400052,
      blockHash: "0x1400052",
      creationTime: 1506474515,
      marketId: "0x0000000000000000000000000000000000000019",
      feeWindow: "0x1000000000000000000000000000000000000000",
      payoutNumerators: [1, 1],
      amountStaked: 229,
      crowdsourcerId: "0x0000000000000000001000000000000000000003",
      isCategorical: false,
      isScalar: false,
      isInvalid: false,
      isSubmitted: true,
    }],
  }
}

augur.reporting.getReportingSummary({
  feeWindow: "0x1000000000000000000000000000000000000000"
}, function (error, result) {
  console.log(result);
});
// example output:
{
  "AWAITING_NEXT_WINDOW": 1,
  "DESIGNATED_REPORTING": 8,
  "CROWDSOURCING_DISPUTE": 2,
  "FINALIZED": 1,
}

augur.reporting.getStakeRequiredForDesignatedReporter({
    universe: "0x000000000000000000000000000000000000000b"
}, function (error, result) {
  console.log(result);
});
// example output:
"1.2345"
```
### augur.reporting.finalizeMarket(p)

[Finalizes](#finalized-market) a [Market](#market), meaning it sets the winning [Payout Distribution Hash](#payout-distribution-hash) for the Market, redistributes [REP](#rep) Staked on non-winning [Outcomes](#outcome) to REP holders who Staked on the winning Outcome, and distributes the [Validity Bond](#validity-bond) based on whether the Market resolved as [Invalid](#invalid-outcome). Then, once the [Post-Finalization Waiting Period](#post-finalization-waiting-period) has elapsed, users can [Settle](#settlement) their [Shares](#share). This transaction will trigger a [`MarketFinalized`](#MarketFinalized) event if the Market Finalized without any errors.

This transaction will fail if:

* The [Initial Report](#initial-report) has not been submitted.
* The [Fee Window](#fee-window) has not ended.
* The Market passed in is a [Forked Market](#forked-market).

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.market`**  (string) Address of the Market to Finalize, as a hex string.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when the transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when the transaction is sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when the transaction fails.

#### **Returns:**

* This function does not return a value.

### augur.reporting.getCurrentPeriodProgress(reportingPeriodDurationInSeconds, timestamp) 

Returns the percentage of the current [Fee Window](#fee-window) that has elapsed.

#### **Parameters:**

* **`reportingPeriodDurationInSeconds`** (number) Number of seconds in a Fee Window.
* **`timestamp`** (number) &lt;optional> Unix timestamp at which to check what percentage of the Fee Window has elapsed. (If not specified, the current Unix timestamp will be used.)

#### **Returns:**

* (number) Percentage of the current Fee Window that has elapsed.

### augur.reporting.getDisputeInfo(p, callback)

Returns information about the [Dispute Stake](#dispute-stake) in a list of [Markets](#market).

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.marketIds`**  (Array.&lt;string>) Array of Market Ethereum contract addresses, as 20-byte hexadecimal strings.
    * **`p.account`**  (string) &lt;optional> Ethereum address (as a 20-byte hexadecimal string) of a user by which to filter the returned results.

#### **Returns:**

* (Array.&lt;<a href="#StakeInfo">StakeInfo</a>>) Array of objects containing information about the Dispute Stake in the list of Markets.

### augur.reporting.getDisputeTokens(p, callback)

Returns the [Dispute Tokens](#dispute-token) owned by a specific user that are either unclaimed or are in [Markets](#market) that have not been [Finalized](#finalized-market).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Contract address of the [Universe](#universe) in which to retrieve the Dispute Tokens, as a 20-byte hexadecimal string.
    * **`p.account`**  (string) Contract address of the account for which to retrieve the Dispute Tokens, as a 20-byte hexadecimal string.
    * **`p.disputeTokenState`**  (<a href="#DISPUTE_TOKEN_STATE">DISPUTE_TOKEN_STATE</a>) &lt;optional> Token state by which to filter results.
* **`callback`** (function) Called after the Dispute Tokens have been retrieved.

#### **Returns:**

* (Array.&lt;<a href="#DisputeToken">DisputeToken</a>>) Dispute Token details, keyed by Dispute Token ID.

### augur.reporting.getFeeWindowCurrent(p, callback)

Returns information about the current [Fee Window](#fee-window). If `p.reporter` is specified, this returned information will also include the total amount of [attoREP](#atto-prefix) the [Reporter](#reporter) has Staked in the current Fee Window (this includes attoREP Staked in [Initial Reports](#initial-report), as well as attoREP Staked in [Dispute Crowdsourcers](#crowdsourcers)).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Ethereum contract address of the Universe in which the Fee Windows exist, as a 20-byte hexadecimal string.
    * **`p.reporter`**  (string) &lt;optional> Ethereum address of a [Reporter](#reporter) for which to return the amount of attoREP they have Staked in the current Fee Window.
* **`callback`** (function) Called after the Fee Windows have been retrieved.

#### **Returns:**

* (<a href="#FeeWindowCurrent">FeeWindowCurrent</a>) Object containing information about the current Fee Window.

### augur.reporting.getFeeWindows(p, callback)

Returns the [Fee Windows](#fee-window) where a specific user has unclaimed [Reporting Fees](#reporting-fee).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Ethereum contract address of the Universe in which the Fee Windows exist, as a 20-byte hexadecimal string.
    * **`p.account`**  (string) Ethereum address of the user who has unclaimed Reporting Fees, as a 20-byte hexadecimal string.
    * **`p.includeCurrent`**  (boolean) Whether to include the current Fee Window in the returned results.
* **`callback`** (function) Called after the Fee Windows have been retrieved.

#### **Returns:**

* (Object) Object containing <a href="#UnclaimedFeeWindowInfo">UnclaimedFeeWindowInfo</a> objects, indexed by the Ethereum address of each FeeWindow contract.

### augur.reporting.getInitialReporters(p, callback)

Returns a list of InitialReporter contracts that a given [Reporter](#reporter) has Staked [REP](#rep) in, along with how much [attoREP](#atto-prefix) was Staked and how much has been redeemed.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.reporter`**  (string) Ethereum address of a Reporter who has Staked REP in InitialReporter contracts, as a 20-byte hexadecimal string.
    * **`p.redeemed`**  (boolean) &lt;optional> If true, the returned results will include only InitialReporter contracts where the Reporter has redeemed Staked REP; if false, the returned results will include only InitialReporter contracts where the Reporter has not redeemed Staked REP. If not specified, the results will include all InitialReporters in which the Repoter has Staked REP.
    * **`p.withRepBalance`**  (boolean) &lt;optional> Whether the InitialReporter contract has a balance greater than 0. If set to `true`, only InitialReporters with a balance greater than 0 will be returned.
* **`callback`** (function) Called after the InitialReporters have been retrieved.

#### **Returns:**

* (Object) Object containing <a href="#InitialReporter">InitialReporter</a> objects, keyed by the Ethereum contract address of the InitialReporter.

### augur.reporting.getReportingHistory(p, callback)

Returns information about the [Reports](#report) submitted by a particular user. For [Fee Windows](#fee-window) that have ended, this includes the [Final Outcome](#final-outcome) of the [Market](#market), whether the user’s Report matched that Final Outcome, how much REP the user gained or lost from redistribution, and how much the user earned in [Reporting Fees](#reporting-fee).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.reporter`**  (string) Ethereum address of the [Reporter](#reporter) for which to retrieve reporting history, as a 20-byte hexadecimal string.
    * **`p.universe`**  (string) &lt;optional> Contract address of the [Universe](#universe) in which to look up the reporting history, as a 20-byte hexadecimal string. Either this parameter, the Market ID, or the Fee Window must be specified.
    * **`p.marketId`**  (string) &lt;optional> Contract address of the Market in which to look up the reporting history, as a 20-byte hexadecimal string. Either this parameter, the Universe, or the Fee Window must be specified.
    * **`p.feeWindow`**  (string) &lt;optional> Contract address of the Fee Window in which to look up the reporting history, as a 20-byte hexadecimal string. Either this parameter, the Universe, or the Market ID must be specified.
    * **`p.earliestCreationTime`**  (number) &lt;optional> Earliest timestamp, in seconds, at which to truncate history results. (This timestamp is when the block on the Ethereum blockchain containing the report submission was created.)
    * **`p.latestCreationTime`**  (number) &lt;optional> Latest timestamp, in seconds, at which to truncate history results. (This timestamp is when the block on the Ethereum blockchain containing the report submission was created.)
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the reporting history.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the reporting history in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of reporting history reports to return.
    * **`p.offset`**  (string) &lt;optional> Number of reporting history reports to truncate from the beginning of the results.
* **`callback`** (function) Called when reporting history has been received and parsed.

#### **Returns:**

* (Object) Reporting history, keyed by Universe or Market ID. Each report is of type <a href="#Report">Report</a>.

### augur.reporting.getReportingSummary(p, callback)

Returns the number of [Markets](#market) in various reporting phases, including “DESIGNATED_REPORTING”, “FIRST_REPORTING”, “LAST_REPORTING”, “AWAITING_FINALIZATION” (when a Market has been [Reported](#report) on and is in a [Dispute Round Phase](#dispute-round-phase)), “FORKING” (for the Market that has [Forked](#fork)), “AWAITING_FORK_MIGRATION” (for Markets that are waiting for a [Forked Market](#forked-market) to resolve), and “FINALIZED”.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.feeWindow`**  (string) Contract address of the [Fee Window](#fee-window) for which to get the summary, as a 20-byte hexadecimal string.
* **`callback`** (function) Called after the reporting summary has been retrieved.

#### **Returns:**

* (Object) Summary of the number of Markets in each reporting phase, keyed by reporting phase.

### augur.reporting.getStakeRequiredForDesignatedReporter(p, callback)

Returns the amount of [REP](#rep) a [Designated Reporter](#designated-reporter) must Stake when submitting a [Designated Report](#designated-report) in a given [Universe](#universe).

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) Ethereum address of a Universe.
* **`callback`** (function) Called after Stake required has been retrieved.

#### **Returns:**

*  (string) Amount of Stake required for the Designated Reporter on this Universe, as a base-10 string.

Trading Functions
-----------------
```javascript
// Trading Simplified API Examples:

augur.trading.calculateProfitLoss({
  trades: [{
    type: "buy",
    amount: "1",
    price: "2",
    maker: false,
  }],
  lastPrice: "2"
});
// example output:
{
  position: "1",
  meanOpenPrice: "2",
  realized: "0",
  queued: "0",
  unrealized: "0"
}

augur.trading.claimMarketsTradingProceeds({
  markets: [
    "0x0000000000000000000000000000000000000001",
    "0x0000000000000000000000000000000000000002",
  ],
  _shareHolder: "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function(result) { console.log(result); },
  onSuccess: function(result) { console.log(result); },
  onFailed: function(result) { console.log(result); }
});
// example onSuccess output:
[
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
]

augur.trading.claimTradingProceeds({
  _market: "0x0000000000000000000000000000000000000001",
  _shareHolder: "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function(result) { console.log(result); },
  onSuccess: function(result) { console.log(result); },
  onFailed: function(result) { console.log(result); }
});
// This function does not return a value.

augur.trading.denormalizePrice({
  minPrice: "0",
  maxPrice: "2",
  normalizedPrice: "0.2",
});
// example output:
"0.4"

// buying: 5 asks, 1 from user, 2 with price too high
augur.trading.filterByPriceAndUserSortByPrice({
  singleOutcomeOrderBookSide: {
    BID_0: {
      amount: "2",
      fullPrecisionPrice: "0.6",
      owner: "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
    },
    BID_1: {
      amount: "1",
      fullPrecisionPrice: "0.5",
      owner: "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
    },
    BID_3: {
      amount: "7",
      fullPrecisionPrice: "0.8",
      owner: "0x33458506efb60601cb3e0585e1afcf0a8f6ae67b",
    },
    BID_4: {
      amount: "3",
      fullPrecisionPrice: "0.7",
      owner: "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
    },
    BID_5: {
      amount: "42",
      fullPrecisionPrice: "0.4",
      owner: "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
    },
  },
  orderType: 1,
  price: "0.6",
  userAddress: "0x33458506efb60601cb3e0585e1afcf0a8f6ae67b",
});
// example output:
[
  {
    amount: "3",
    fullPrecisionPrice: "0.7",
    owner: "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
  }, {
    amount: "2",
    fullPrecisionPrice: "0.6",
    owner: "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
  }
]

augur.trading.getBetterWorseOrders({
  marketId: "0x0000000000000000000000000000000000000001",
  outcome: 0,
  orderType: "buy",
  price: 0.65,
}, function (error, result) {
  console.log(result);
});
// example output:
{
  betterOrderId: "0x1000000000000000000000000000000000000000000000000000000000000000",
  worseOrderId: "0x2000000000000000000000000000000000000000000000000000000000000000",
}

augur.trading.getOrders({
  marketId: "0x0000000000000000000000000000000000000001",
  universe: "0x000000000000000000000000000000000000000b",
  outcome: null,
  orderType: "buy",
  creator: null,
  orderState: "OPEN",
}, function (error, result) {
  console.log(result);
});
// example output:
{
  "0x0000000000000000000000000000000000000001": {
    0: {
      buy: {
        "0x1000000000000000000000000000000000000000000000000000000000000000": {
          orderId: "0x1000000000000000000000000000000000000000000000000000000000000000",
          shareToken: "0x1000000000000000000000000000000000000000",
          transactionHash: "0x0000000000000000000000000000000000000000000000000000000000000A00",
          logIndex: 0,
          owner: "0x0000000000000000000000000000000000000b0b",
          creationTime: 1506473500,
          creationBlockNumber: 1400001,
          orderState: "OPEN",
          price: 0.7,
          amount: 1,
          fullPrecisionPrice: 0.7,
          fullPrecisionAmount: 1,
          tokensEscrowed: 0.7,
          sharesEscrowed: 0,
        },
        "0x2000000000000000000000000000000000000000000000000000000000000000": {
          orderId: "0x2000000000000000000000000000000000000000000000000000000000000000",
          shareToken: "0x1000000000000000000000000000000000000000",
          transactionHash: "0x0000000000000000000000000000000000000000000000000000000000000A01",
          logIndex: 0,
          owner: "0x000000000000000000000000000000000000d00d",
          creationTime: 1506473515,
          creationBlockNumber: 1400002,
          orderState: "OPEN",
          price: 0.6,
          amount: 2,
          fullPrecisionPrice: 0.600001,
          fullPrecisionAmount: 2,
          tokensEscrowed: 1.200002,
          sharesEscrowed: 0,
        },
        "0x5000000000000000000000000000000000000000000000000000000000000000": {
          orderId: "0x5000000000000000000000000000000000000000000000000000000000000000",
          amount: 1,
          creationBlockNumber: 1400001,
          creationTime: 1506473500,
          transactionHash: "0x0000000000000000000000000000000000000000000000000000000000000A06",
          logIndex: 0,
          fullPrecisionAmount: 1,
          fullPrecisionPrice: 0.7,
          orderState: "OPEN",
          owner: "0x0000000000000000000000000000000000000b0b",
          price: 0.7,
          shareToken: "0x1000000000000000000000000000000000000000",
          sharesEscrowed: 0,
          tokensEscrowed: 0.7,
        },
      },
    },
    1: {
      buy: {
        "0x3000000000000000000000000000000000000000000000000000000000000000": {
          orderId: "0x3000000000000000000000000000000000000000000000000000000000000000",
          shareToken: "0x2000000000000000000000000000000000000000",
          transactionHash: "0x0000000000000000000000000000000000000000000000000000000000000A02",
          logIndex: 0,
          owner: "0x000000000000000000000000000000000000d00d",
          creationTime: 1506473515,
          creationBlockNumber: 1400002,
          orderState: "OPEN",
          price: 0.6,
          amount: 2,
          fullPrecisionPrice: 0.6,
          fullPrecisionAmount: 2.0000001,
          tokensEscrowed: 1.20000006,
          sharesEscrowed: 0,
        },
      },
    },
  },
}

augur.trading.getPositionInMarket({
  address: "0x25ff5dc79a7c4e34254ff0f4a19d69e491201dd3",
  market: "0x2221185d7f125b84ac4a1837a0688d2bb58e8491",
  tickSize: "0.0001",
}, function (error, result) {
  console.log(result);
});
// example output:
["1", "0.5", "1", "0", "0", "1", "1.5"]

augur.trading.getUserTradingHistory({
  account: "0x000000000000000000000000000000000000d00d",
  universe: null,
  marketId: "0x0000000000000000000000000000000000000001",
  outcome: 0,
  orderType: null,
  sortBy: null,
  isSortDescending: null,
  limit: null,
  offset: null,
}, function (error, result) {
  console.log(result);
});
// example output:
[
  {
    transactionHash: "0x0000000000000000000000000000000000000000000000000000000000000C00",
    logIndex: 0,
    type: "sell",
    price: 5.5,
    amount: 0.2,
    maker: false,
    marketCreatorFees: 0,
    reporterFees: 0,
    settlementFees: "0",
    marketId: "0x0000000000000000000000000000000000000001",
    outcome: 0,
    shareToken: "0x1000000000000000000000000000000000000000",
    timestamp: 1506474500,
    tradeGroupId: null,
  }
]

augur.trading.getUserTradingPositions({
  account: "0x000000000000000000000000000000000000d00d",
  universe: "0x000000000000000000000000000000000000000b",
  marketId: null,
  outcome: null,
  sortBy: null,
  isSortDescending: null,
  limit: null,
  offset: null,
}, function (error, result) {
  console.log(result);
});
// example output:
[
  {
    "marketId": "0x0000000000000000000000000000000000000001",
    "outcome": 0,
    "numShares": 0.2,
    "numSharesAdjusted": 0.2,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 11,
    "averagePrice": 0,
  }, {
    "marketId": "0x0000000000000000000000000000000000000001",
    "outcome": 1,
    "numShares": 0,
    "numSharesAdjusted": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketId": "0x0000000000000000000000000000000000000001",
    "outcome": 2,
    "numShares": 0,
    "numSharesAdjusted": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketId": "0x0000000000000000000000000000000000000001",
    "outcome": 3,
    "numShares": 0,
    "numSharesAdjusted": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketId": "0x0000000000000000000000000000000000000001",
    "outcome": 4,
    "numShares": 0,
    "numSharesAdjusted": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketId": "0x0000000000000000000000000000000000000001",
    "outcome": 5,
    "numShares": 0,
    "numSharesAdjusted": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketId": "0x0000000000000000000000000000000000000001",
    "outcome": 6,
    "numShares": 0,
    "numSharesAdjusted": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketId": "0x0000000000000000000000000000000000000001",
    "outcome": 7,
    "numShares": 0,
    "numSharesAdjusted": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }
]

augur.trading.normalizePrice({
  minPrice: "0",
  maxPrice: "2",
  price: "0.4",
});
// example output:
"0.2"

augur.trading.placeTrade({
  amount: "10",
  limitPrice: "2",
  minPrice: "1",
  maxPrice: "3",
  numTicks: "10000",
  tickSize: "0.0001",
  _direction: 0,
  _market: "0x8092bdf939e23a0e926021ffce5a062d0f598d1f",
  _outcome: 0,
  doNotCreateOrders: false,
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function(result) { console.log(result); },
  onSuccess: function(result) { console.log(result); },
  onFailed: function(result) { console.log(result); }
});
// This function does not return a value, but it calls the function `augur.trading.tradeUntilAmountIsZero`, which logs information to the console as it places Orders. To see this output, refer to the JS sample code for `augur.trading.tradeUntilAmountIsZero`.

augur.trading.simulateTrade({
  orderType: 0,
  outcome: 0,
  shares: "3",
  shareBalances: ["0", "5"],
  tokenBalance: "0",
  userAddress: "0x111327d07fc17907b4db788e5adf2ed424addff6",
  minPrice: "0",
  maxPrice: "1",
  price: "0.7",
  marketCreatorFeeRate: "0",
  reportingFeeRate: "0.01",
  shouldCollectReportingFees: 1,
  singleOutcomeOrderBook: {
    buy: {
      BID_0: {
        amount: "2",
        fullPrecisionPrice: "0.7",
        sharesEscrowed: "2",
        owner: "0x2228238db7ad03e505840bd361dc2f521a72adbc",
      },
    },
    sell: {
      ASK_0: {
        amount: "2",
        fullPrecisionPrice: "0.7",
        sharesEscrowed: "2",
        owner: "0x2228238db7ad03e505840bd361dc2f521a72adbc",
      },
    },
  },
}, function (error, result) {
  console.log(result);
});
// example output:
{
  sharesFilled: "2",
  settlementFees: "0.006",
  worstCaseFees: "0.009",
  gasFees: "0",
  otherSharesDepleted: "3",
  sharesDepleted: "0",
  tokensDepleted: "0",
  shareBalances: ["0", "4"],
}

augur.trading.tradeUntilAmountIsZero({
  _price: "0.5",
  _fxpAmount: "10",
  numTicks: "10000",
  tickSize: "0.0001",
  _direction: 0,
  _market: "0x8092bdf939e23a0e926021ffce5a062d0f598d1f",
  _outcome: 0,
  doNotCreateOrders: false,
  meta: {
    signer: [252, 111, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 25],
    accountType: "privateKey"
  },
  onSent: function(result) { console.log(result); },
  onSuccess: function(result) { console.log(result); },
  onFailed: function(result) { console.log(result); }
});
// This function does not return a value, but it does log information to the console as it places Orders:
"tradeUntilAmountIsZero: "
{
  "_price": "0.5",
  "_fxpAmount": "10",
  "numTicks": "10000",
  "tickSize": "0.0001",
  "_direction": 0,
  "_market": "0x8092bdf939e23a0e926021ffce5a062d0f598d1f",
  "_outcome": 0,
  "doNotCreateOrders": false
}
"cost: 5 ETH"
undefined
{
  "hash": "0x0aaf4bd7dc1a022c0530d41dc97e286108d505dc5e7ad81ae3d33e0bb2bded14",
  "callReturn": null
}
"Order Created: "
{
  "marketId": "0x8092bdf939e23a0e926021ffce5a062d0f598d1f",
  "blockNumber": 15104,
  "transactionHash": "0x0aaf4bd7dc1a022c0530d41dc97e286108d505dc5e7ad81ae3d33e0bb2bded14",
  "logIndex": 2,
  "outcome": 0,
  "shareToken": "0xaa21ca0187d97dc3d4411e3fa5a7f34b81c85e74",
  "orderCreator": "0x913da4198e6be1d5f5e4a40d0667f70c0b5430eb",
  "orderState": "OPEN",
  "tradeGroupId": "0x0",
  "orderType": "buy",
  "price": "0.5",
  "amount": "10",
  "fullPrecisionPrice": "0.5",
  "fullPrecisionAmount": "10",
  "tokensEscrowed": "5",
  "sharesEscrowed": "0",
  "orderId": "0xd34bcfcdd831f54edb0b4f6982f03005fab01bf8ec1d8570bad2144f916e3d9"
}
"convertTradeLogToTransaction CreateOrder"
{
  "0x8092bdf939e23a0e926021ffce5a062d0f598d1f": {
    "0": [
      {
        "marketId": "0x8092bdf939e23a0e926021ffce5a062d0f598d1f",
        "blockNumber": 15104,
        "transactionHash": "0x0aaf4bd7dc1a022c0530d41dc97e286108d505dc5e7ad81ae3d33e0bb2bded14",
        "logIndex": 2,
        "outcome": 0,
        "shareToken": "0xaa21ca0187d97dc3d4411e3fa5a7f34b81c85e74",
        "orderCreator": "0x913da4198e6be1d5f5e4a40d0667f70c0b5430eb",
        "orderState": "OPEN",
        "tradeGroupId": "0x0",
        "orderType": "buy",
        "price": "0.5",
        "amount": "10",
        "fullPrecisionPrice": "0.5",
        "fullPrecisionAmount": "10",
        "tokensEscrowed": "5",
        "sharesEscrowed": "0",
        "orderId": "0xd34bcfcdd831f54edb0b4f6982f03005fab01bf8ec1d8570bad2144f916e3d9"
      }
    ]
  }
}
"constructTradingTransaction: CreateOrder"
{
  "marketId": "0x8092bdf939e23a0e926021ffce5a062d0f598d1f",
  "blockNumber": 15104,
  "transactionHash": "0x0aaf4bd7dc1a022c0530d41dc97e286108d505dc5e7ad81ae3d33e0bb2bded14",
  "logIndex": 2,
  "outcome": 0,
  "shareToken": "0xaa21ca0187d97dc3d4411e3fa5a7f34b81c85e74",
  "orderCreator": "0x913da4198e6be1d5f5e4a40d0667f70c0b5430eb",
  "orderState": "OPEN",
  "tradeGroupId": "0x0",
  "orderType": "buy",
  "price": "0.5",
  "amount": "10",
  "fullPrecisionPrice": "0.5",
  "fullPrecisionAmount": "10",
  "tokensEscrowed": "5",
  "sharesEscrowed": "0",
  "orderId": "0xd34bcfcdd831f54edb0b4f6982f03005fab01bf8ec1d8570bad2144f916e3d9"
}
"res:"
{
  "marketId": "0x8092bdf939e23a0e926021ffce5a062d0f598d1f",
  "blockNumber": 15104,
  "transactionHash": "0x0aaf4bd7dc1a022c0530d41dc97e286108d505dc5e7ad81ae3d33e0bb2bded14",
  "logIndex": 2,
  "outcome": 0,
  "shareToken": "0xaa21ca0187d97dc3d4411e3fa5a7f34b81c85e74",
  "orderCreator": "0x913da4198e6be1d5f5e4a40d0667f70c0b5430eb",
  "orderState": "OPEN",
  "tradeGroupId": "0x0",
  "orderType": "buy",
  "price": "0.5",
  "amount": "10",
  "fullPrecisionPrice": "0.5",
  "fullPrecisionAmount": "10",
  "tokensEscrowed": "5",
  "sharesEscrowed": "0",
  "orderId": "0xd34bcfcdd831f54edb0b4f6982f03005fab01bf8ec1d8570bad2144f916e3d9"
}
"starting amount:  10"
"amount remaining: 0"
"updated estimated cost: null"
"tradeUntilAmountIsZero: "
{
  "_price": "0.5",
  "_fxpAmount": "0",
  "numTicks": "10000",
  "tickSize": "0.0001",
  "_direction": 0,
  "_market": "0x8092bdf939e23a0e926021ffce5a062d0f598d1f",
  "_outcome": 0,
  "doNotCreateOrders": false,
  "estimatedCost": null
}
null
```
### augur.trading.calculateProfitLoss(p)

Calculates realized and unrealized profit/loss for trades in a single [Outcome](#outcome). Note: buy/sell labels are from taker's point of view.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.trades`**  (Array.&lt;Object>) Trades for a single Outcome {type: string, amount: string, price: string, maker: boolean}.
    * **`p.lastPrice`**  (string) &lt;optional> Price of this Outcome's most recent trade, as a base-10 string (default: 0).

#### **Returns:**

* (<a href="#ProfitLoss">ProfitLoss</a>) Realized and unrealized P/L.

### augur.trading.claimMarketsTradingProceeds(p)

Similar to the function `augur.trading.claimTradingProceeds`, but attempts to collect trading profits in Ether from a user's outstanding [Shares](#share) in multiple [Finalized Markets](#finalized-market) instead of a single Finalized Market.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.markets`**  (Array.&lt;string>) Array of [Market](#market) addresses for which to claim proceeds.
    * **`p._shareHolder`**  (string) User address that holds the shares, as a 20-byte hexadecimal string.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when each transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when all transactions are sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when any of the transactions fail.

#### **Returns:**

* (Array.<string>) Array of Market addresses from which trading proceeds were attempted to be claimed, as 20-byte hexadecimal strings.

### augur.trading.claimTradingProceeds(p)

Attempts to collect trading profits in Ether from a user's outstanding [Shares](#share) in a single [Finalized Market](#finalized-market).

This function will fail if:

* `p._market` is not Finalized.
* The [Post-Finalization Waiting Period](#post-finalization-waiting-period) has not passed.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p._market`**  (string) [Market](#market) address for which to claim proceeds.
    * **`p._shareHolder`**  (string) Ethereum address that holds the shares, as a 20-byte hexadecimal string.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when each transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when all transactions are sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when any of the transactions fail.

#### **Returns:**

* This function does not return a value.

### augur.trading.denormalizePrice(p)

Rescales a price to its display range [minPrice, maxPrice]: displayPrice = normalizedPrice*(maxPrice - minPrice) + minPrice.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.minPrice`**  (BigNumber|string) This [Market's](#market) minimum possible price, as a BigNumber or base-10 string.
    * **`p.maxPrice`**  (BigNumber|string) This Market's maximum possible price, as a BigNumber or base-10 string.
    * **`p.normalizedPrice`**  (BigNumber|string) The price to be denormalized, as a BigNumber or base-10 string.

#### **Returns:**

* (string) Price rescaled to [minPrice, maxPrice], as a base-10 string.

### augur.trading.filterByPriceAndUserSortByPrice(p)

Accepts a [SingleOutcomeOrderBookSide](#SingleOutcomeOrderBookSide) object, and filters out all [Orders](#order) where the owner is `p.userAddress` or the price is below `p.price` (for [Bid Orders](#bid-order)) or above `p.price` (for [Ask Orders](#ask-order)). Returns the remaining Orders sorted by `p.price`. Bid Orders are sorted descendingly, Ask Orders are sorted ascendingly.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.singleOutcomeOrderBookSide`**  (<a href="#SingleOutcomeOrderBookSide">SingleOutcomeOrderBookSide</a>) Bid Orders or Ask Orders for a particular [Market](#market) and [Outcome](#outcome).
    * **`p.orderType`**  (number) Order type (0 for "buy", 1 for "sell").
    * **`p.price`**  (string) Limit price for this Order (i.e. the worst price the user will accept), as a base-10 string.
    * **`p.userAddress`**  (string) User's Ethereum address to filter from Orders, as a 20-byte hexadecimal string.

#### **Returns:**

* (Array.&lt;<a href="#Order">Order</a>>) Array of filtered and sorted Orders.

<!-- Add links to section -->
### augur.trading.getBetterWorseOrders(p, callback)

Returns the IDs of the [Orders](#order) for a given [Outcome](#outcome) that have a better and worse price than the specified `price`. If no better/worse Orders exist, null will be returned. This function should be called prior to calling `augur.api.CreateOrder.publicCreateOrder` in order to get the `_betterOrderId` and `_worseOrderId` parameters that it accepts. (`_betterOrderId` and `_worseOrderId` are used to optimize the sorting of Orders on the [Order Book](#order-book).)

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.marketId`**  (string) Contract address of the [Market](#market) for which to retrieve the better/worse Orders, as a 20-byte hexadecimal string.
    * **`p.outcome`**  (string) Market Outcome for which to find better/worse Orders.
    * **`p.orderType`**  (string) Desired type of Order. Valid values are "buy" and "sell".
    * **`p.price`**  (number) Price point at which to find better/worse Orders.
* **`callback`** (function) Called when better/worse Orders have been retrieved.

#### **Returns:**

* (<a href="#BetterWorseOrders">BetterWorseOrders</a>) Object containing the better/worse Order IDs, as hexadecimal strings.

### augur.trading.getOrders(p, callback)

Returns a list of orders in a given Universe or Market.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) &lt;optional> Contract address of the Universe from which to retrieve orders, as a 20-byte hexadecimal string. Either this parameter or the marketId must be specified.
    * **`p.marketId`**  (string) &lt;optional> Contract address of the Market from which to retrieve Orders, as a 20-byte hexadecimal string. Either this parameter or the Universe must be specified.
    * **`p.outcome`**  (number) &lt;optional> Market Outcome to filter results by. Valid values are in the range [0,7].
    * **`p.creator`**  (string) &lt;optional> Ethereum address of the Order creator, as a 20-byte hexadecimal string.
    * **`p.orderState`**  (<a href="#ORDER_STATE">ORDER_STATE</a>) &lt;optional> State of orders by which to filter results. Valid values are "ALL", "CANCELLED", "CLOSED", & "OPEN".
    * **`p.earliestCreationTime`**  (number) &lt;optional> Earliest timestamp, in seconds, at which to truncate order results. (This timestamp is when the block on the Ethereum blockchain containing the transfer was created.)
    * **`p.latestCreationTime`**  (number) &lt;optional> Latest timestamp, in seconds, at which to truncate order results. (This timestamp is when the block on the Ethereum blockchain containing the transfer was created.)
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the orders.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort orders in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of orders to return.
    * **`p.offset`**  (string) &lt;optional> Number of orders to truncate from the beginning of the results.
* **`callback`** (function) Called when the requested orders for this Market/Universe have been received and parsed.

#### **Returns:**

* (<a href="#SingleOutcomeOrderBookSide">SingleOutcomeOrderBookSide</a>) One side of the Order Book (buy or sell) for the specified Market/Universe and Outcome.

### augur.trading.getPositionInMarket(p, callback)

Gets the number of [Shares](#share) held by a specific Ethereum address for each [Outcome](#outcome) of a [Market](#market).

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.address`**  (string) Address for which to look up Share balances.
    * **`p.market`**  (string) Market for which to look up Share balances.
    * **`p.tickSize`**  (string) [Tick](#tick) size (interval) for this Market.
* **`callback`** (function) Called when the requested Market position has been received and parsed.

#### **Returns:**

* (Array.&lt;string>) Number of Shares for each Outcome of this Market.

<!-- TODO: rename `maker` to `creator` in returned result in JS example -->
### augur.trading.getUserTradingHistory(p, callback)

Returns information about the trades a specific user has made.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.account`**  (string) Ethereum address of the user for which to retrieve trading history, as a 20-byte hexadecimal string.
    * **`p.universe`**  (string) &lt;optional> Contract address of the [Universe](#universe) in which to look up the trading history, as a 20-byte hexadecimal string. Either this parameter or the [Market](#market) ID must be specified.
    * **`p.marketId`**  (string) &lt;optional> Contract address of the Market in which to look up the trading history, as a 20-byte hexadecimal string. Either this parameter or the Universe must be specified.
    * **`p.outcome`**  (string) [Outcome](#outcome) of the [Share](#share) being bought/sold.
    * **`p.orderType`**  (string) Type of trade. Valid values are "buy" and "sell".
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the trading history.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the trading history in descending order by `sortBy` field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of trading history reports to return.
    * **`p.offset`**  (string) &lt;optional> Number of trading history reports to truncate from the beginning of the results.
* **`callback`** (function) Called when trading history has been received and parsed.

#### **Returns:**

* (Array.&lt;<a href="#UserTrade">UserTrade</a>>) Array of the user's trades, keyed by Universe/Market ID.

### augur.trading.getUserTradingPositions(p, callback)

Returns the trading positions held by a specific user.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.account`**  (string) Ethereum address of the user for which to retrieve trading positions, as a 20-byte hexadecimal string.
    * **`p.universe`**  (string) &lt;optional> Contract address of the [Universe](#universe) in which to look up the trading positions, as a 20-byte hexadecimal string. Either this parameter or the Market ID must be specified.
    * **`p.marketId`**  (string) &lt;optional> Contract address of the [Market](#market) in which to look up the trading positions, as a 20-byte hexadecimal string. Either this parameter or the Universe must be specified.
    * **`p.outcome`**  (string) [Outcome](#outcome) of the [Share](#share) held for the Market.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the trading positions.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the trading positions in descending order by `sortBy` field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of trading positions reports to return.
    * **`p.offset`**  (string) &lt;optional> Number of trading positions reports to truncate from the beginning of the results.
* **`callback`** (function) Called when the trading positions have been received and parsed.

#### **Returns:**

* (Array.&lt;<a href="#UserTradePosition">UserTradePosition</a>>) Array of the user's trading positions.

### augur.trading.normalizePrice(p)

Rescales a price to lie on [0, 1]: normalizedPrice = (price - minPrice) / (maxPrice - minPrice).

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.minPrice`**  (BigNumber|string) This [Market's](#market) minimum possible price, as a BigNumber or base-10 string.
    * **`p.maxPrice`**  (BigNumber|string) This Market's maximum possible price, as a BigNumber or base-10 string.
    * **`p.normalizedPrice`**  (BigNumber|string) The price to be normalized, as a BigNumber or base-10 string.

#### **Returns:**

* (string) Price rescaled to [0, 1], as a base-10 string.

### augur.trading.placeTrade(p)

* Places an [Order](#order) for a certain number of [Shares](#share) in a particular [Outcome](#outcome) of a [Market](#market).

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.amount`**  (string) Number of Shares to trade, as a base-10 string.
    * **`p.limitPrice`**  (string) Display (non-normalized) limit price for this trade, as a base-10 string.
    * **`p.minPrice`**  (string) The [Minimum Display Price](#minimum-display-price) (non-normalized) for this Market, as a base-10 string.
    * **`p.maxPrice`**  (string) The [Maximum Display Price](#maximum-display-price) (non-normalized) for this Market, as a base-10 string.
    * **`p.numTicks`**  (string) The number of [Ticks](#tick) for this market.
    * **`p.tickSize`**  (string) The [Tick](#tick) size (interval) for this Market.
    * **`p._direction`**  (number) Order type (0 for "buy", 1 for "sell").
    * **`p._market`**  (string) Ethereum contract address of the Market in which to trade, as a 20-byte hexadecimal string
    * **`p._outcome`**  (number) Outcome ID to trade, must be an integer value in between 0 and 7.
    * **`p.estimatedCost`** (string) &lt;optional> Total cost (in ETH) of this trade, as a base-10 string.
    * **`p._tradeGroupId`**  (string) &lt;optional> ID logged with each trade transaction, as a hexadecimal string. (This is generally just used by Augur's UI to group trades client-side.)
    * **`p.doNotCreateOrders`**  (boolean) &lt;optional> If set to true, this trade will only take existing Orders off the [Order Book](#order-book), not create new ones (default: false).
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called when the first trading transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called when the full trade completes successfully.
    * **`p.onFailed`**  (function) Called if any part of the trade fails.

#### **Returns:**

* This function does not return a value.

### augur.trading.simulateTrade(p, callback)

Determines the sequence of makes/takes that will be executed to [Fill](#fill-order) the specified [Order](#order), and returns the user's projected balances and fees paid after this sequence is completed. Note: This function automatically normalizes [Share](#share) prices, so "display prices" can be passed in directly for `minPrice`, `maxPrice`, and `price`.

#### **Parameters:**

* **`p`** (Object) Trade simulation parameters.
    * **`p.orderType`**  (number) Order type (0 for "buy", 1 for "sell").
    * **`p.outcome`**  (number)  [Outcome](#outcome) ID to trade, must be an integer value on [0, 7].
    * **`p.shareBalances`**  (string[])  Number of Shares the user owns of each Outcome in ascending order, as an array of base-10 strings.
    * **`p.tokenBalance`**  (string)  Number of tokens (e.g., wrapped Ether) the user owns, as a base-10 string.
    * **`p.userAddress`**  (string)  The user's Ethereum address, as a 20-byte hexadecimal string.
    * **`p.minPrice`**  (string)  This [Market's](#market) minimum possible price, as a base-10 string.
    * **`p.maxPrice`**  (string)  This Market's maximum possible price, as a base-10 string.
    * **`p.price`**  (string|null)  Limit price for this Order (i.e. the worst price the user will accept), as a base-10 string.
    * **`p.shares`**  (string) Number of Shares to trade, as a base-10 string.
    * **`p.marketCreatorFeeRate`**  (string) The fee rate charged by the Market creator (e.g., pass in "0.01" if the fee is 1%), as a base-10 string.
    * **`p.marketOrderBook`**  (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>) The full [Order Book](#order-book) (buy and sell) for this Market and Outcome.
    * **`p.shouldCollectReportingFees`**  (boolean) &lt;optional> False if reporting fees are not collected; this is rare and only occurs in disowned Markets (default: true).

#### **Returns:**

* ([SimulatedTrade](#SimulatedTrade)) Projected fees paid, shares and tokens spent, and final balances after the trade is complete.

### augur.trading.tradeUntilAmountIsZero(p, callback)

If `p.doNotCreateOrders` is set to `true`, this function will place trades until all [Orders](#order) equal to or better than the specified price limit are removed from the [Order Book](#order-book).

If `p.doNotCreateOrders` is set to `false`, this function will place trades until the user's request has been fulfilled. This is done by attempting to trade until all Orders equal to or better than the specified limit price are removed from the Order Book and then creating a new Order with whatever amount is remaining in the user's request.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p._price`**  (string) Normalized limit price for this trade, as a base-10 string.
    * **`p._fxpAmount`**  (string) Number of [Shares](share) to trade, as a base-10 string.
    * **`p.numTicks`**  (string) The number of [Ticks](#tick) for this [Market](#market).
    * **`p.tickSize`**  (string) The Tick size (interval) for this Market.
    * **`p._direction`**  (number) [Order](#order) type (0 for "buy", 1 for "sell").
    * **`p._market`**  (string) Market in which to trade, as a hex string.
    * **`p._outcome`**  (number) [Outcome](#outcome) ID to trade, must be an integer value on [0, 7].
    * **`p.estimatedCost`** (string) &lt;optional> Total cost (in ETH) of this trade, as a base-10 string.
    * **`p._tradeGroupId`**  (string) &lt;optional> ID logged with each trade transaction, as a hexadecimal string. (This is generally just used by Augur's UI to group trades client-side.)
    * **`p.doNotCreateOrders`**  (boolean) &lt;optional> If set to true, this trade will only take existing orders off the book, not create new ones (default: false).
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called when the first trading transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called when the full trade completes successfully.
    * **`p.onFailed`**  (function) Called if any part of the trade fails.

#### **Returns:**

* This function does not return a value.