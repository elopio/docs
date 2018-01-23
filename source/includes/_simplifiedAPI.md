Simplified API
===========
<aside class="notice">The Simplified API section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

<aside class="warning">The Simplified API provides an easy way to make transactions and query for information within Augur. This API is simplified in the sense that single requests to this API can be used to fetch a large amount of data, without the need for complicated RPC batch queries. However, many of the functions in this API require a connection to an <a href="#augur-node">Augur Node</a>, which has been indicated in their descriptions. Please read the <a href="#augur-node">Augur Node</a> section before using these functions.</aside>

Accounts Functions
-----------------
```javascript
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
    marketID: " 0x0000000000000000000000000000000000000001",
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
    marketID: " 0x0000000000000000000000000000000000000001",
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
    marketID: null,
    outcome: null,
  }
]

augur.accounts.importAccount({
  address: "0xacad0e04f71c7f202d546ab71b047410bce3277c",
  keystore: {
    address: "0xacad0e04f71c7f202d546ab71b047410bce3277c",
    crypto: {
      cipher: "aes-128-ctr",
      cipherparams: {
        iv: "1be316027cc38223635f54dced8fefb4"
      },
      ciphertext: "30722b1b8f84752813e67489a17e89fafcb768fcbdcee03e2aea220bc3e0173e",
      kdf: "pbkdf2",
      kdfparams: {
        c: 65536,
        dklen: 32,
        prf: "hmac-sha256",
        salt: "2d5e265588356263153d729f2b7151ffca65dba768b25ede61eb6475eff7cf01"
      },
      mac: "70bbd8c0324aba45404a8d67ae8af6ad0888654cb8e401a599f7f6b165261c59"
    },
    id: "3dd23c7f-74ab-4ce0-a3b1-30918e4f6cca",
    version: 3
  },
  password: "thisismysupersecurepassword"
}, function (error, account) {
  console.log(account);
});
// example output:
{
  address: "0xacad0e04f71c7f202d546ab71b047410bce3277c",
  derivedKey: Uint8Array(32) [121, 199, 100, 210, 236, 254, 150, 229, 159, 182, 49, 89, 198, 158, 135, 200, 242, 108, 111, 245, 143, 135, 3, 216, 223, 48, 95, 214, 7, 112, 106, 246],
  keystore: {
    address: "0xacad0e04f71c7f202d546ab71b047410bce3277c",
    crypto: {
      cipher: "aes-128-ctr",
      cipherparams: {
        iv: "1be316027cc38223635f54dced8fefb4"
      },
      ciphertext: "30722b1b8f84752813e67489a17e89fafcb768fcbdcee03e2aea220bc3e0173e",
      kdf: "pbkdf2",
      kdfparams: {
        c: 65536,
        dklen: 32,
        prf: "hmac-sha256",
        salt: "2d5e265588356263153d729f2b7151ffca65dba768b25ede61eb6475eff7cf01"
      },
      mac: "70bbd8c0324aba45404a8d67ae8af6ad0888654cb8e401a599f7f6b165261c59"
    },
    id: "3dd23c7f-74ab-4ce0-a3b1-30918e4f6cca",
    version: 3
  },
  privateKey: Uint8Array(32) [154, 195, 95, 10, 39, 106, 79, 107, 240, 160, 184, 204, 214, 23, 139, 203, 213, 38, 245, 16, 225, 209, 165, 144, 201, 130, 146, 88, 46, 20, 169, 10]
}

augur.accounts.login({
  keystore: {
    address: "0xacad0e04f71c7f202d546ab71b047410bce3277c",
    crypto: {
      cipher: "aes-128-ctr",
      cipherparams: {
        iv: "1be316027cc38223635f54dced8fefb4"
      },
      ciphertext: "30722b1b8f84752813e67489a17e89fafcb768fcbdcee03e2aea220bc3e0173e",
      kdf: "pbkdf2",
      kdfparams: {
        c: 65536,
        dklen: 32,
        prf: "hmac-sha256",
        salt: "2d5e265588356263153d729f2b7151ffca65dba768b25ede61eb6475eff7cf01"
      },
      mac: "70bbd8c0324aba45404a8d67ae8af6ad0888654cb8e401a599f7f6b165261c59"
    },
    id: "3dd23c7f-74ab-4ce0-a3b1-30918e4f6cca",
    version: 3
  },
  password: "thisismysupersecurepassword"
}, function (error, account) {
  console.log(account);
});
// example output:
{
  address: undefined,
  derivedKey: Uint8Array(32) [121, 199, 100, 210, 236, 254, 150, 229, 159, 182, 49, 89, 198, 158, 135, 200, 242, 108, 111, 245, 143, 135, 3, 216, 223, 48, 95, 214, 7, 112, 106, 246],
  keystore: {
    address: "0xacad0e04f71c7f202d546ab71b047410bce3277c",
    crypto: {
      cipher: "aes-128-ctr",
      cipherparams: {
        iv: "1be316027cc38223635f54dced8fefb4"
      },
      ciphertext: "30722b1b8f84752813e67489a17e89fafcb768fcbdcee03e2aea220bc3e0173e",
      kdf: "pbkdf2",
      kdfparams: {
        c: 65536,
        dklen: 32,
        prf: "hmac-sha256",
        salt: "2d5e265588356263153d729f2b7151ffca65dba768b25ede61eb6475eff7cf01"
      },
      mac: "70bbd8c0324aba45404a8d67ae8af6ad0888654cb8e401a599f7f6b165261c59"
    },
    id: "3dd23c7f-74ab-4ce0-a3b1-30918e4f6cca",
    version: 3
  },
  privateKey: Uint8Array(32) [154, 195, 95, 10, 39, 106, 79, 107, 240, 160, 184, 204, 214, 23, 139, 203, 213, 38, 245, 16, 225, 209, 165, 144, 201, 130, 146, 88, 46, 20, 169, 10]
}

augur.accounts.loginWithMasterKey({
  privateKey: [154, 195, 95, 10, 39, 106, 79, 107, 240, 160, 184, 204, 214, 23, 139, 203, 213, 38, 245, 16, 225, 209, 165, 144, 201, 130, 146, 88, 46, 20, 169, 10]
});
// example output:
{
  address: "0x0cad0e04f71c7f202d546ab71b047410bce3277c"
  derivedKey: Uint8Array(32) [201, 255, 60, 63, 53, 230, 36, 85, 169, 202, 221, 48, 231, 73, 203, 250, 107, 208, 201, 39, 72, 68, 185, 10, 218, 102, 13, 174, 197, 154, 41, 196]
  privateKey: Uint8Array(32) [154, 195, 95, 10, 39, 106, 79, 107, 240, 160, 184, 204, 214, 23, 139, 203, 213, 38, 245, 16, 225, 209, 165, 144, 201, 130, 146, 88, 46, 20, 169, 10]
}

augur.accounts.logout();
// This function does not produce any output.
```
### augur.accounts.getAccountTransferHistory(p, callback)

Returns the token transfers made to or from a specific Ethereum address. 

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.account`**  (string) Ethereum address of the account for which to get transfer history, as a hexadecimal string.
    * **`p.token`**  (string) &lt;optional> Contract address of the token contract by which to limit the history results, as a hexadecimal string.
    * **`p.earliestCreationTime`**  (number) &lt;optional> Earliest timestamp, in seconds, at which to truncate history results. (This timestamp is when the block on the Ethereum blockchain containing the transfer was created.)
    * **`p.latestCreationTime`**  (number) &lt;optional> Latest timestamp, in seconds, at which to truncate history results. (This timestamp is when the block on the Ethereum blockchain containing the transfer was created.)
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort transfer history.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort transfers in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of transfers to return.
    * **`p.offset`**  (string) &lt;optional> Number of transfers to truncate from the beginning of the history results.
* **`callback`** (function) Called after the account transfer history has been retrieved.

#### **Returns:**

* (Array.&lt;<a href="#AccountTransfer">AccountTransfer</a>>) Array representing the account's transfer history.

### augur.accounts.importAccount(p, callback)

Description pending.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.password`**  (string) Password for the account being imported.
    * **`p.address`**  (string) Ethereum address of the account being imported.
    * **`p.keystore`**  (<a href="#Keystore">Keystore</a>) Keystore object containing this account's encryption parameters.
* **`callback`** (function) Called after the account's private key has been successfully decrypted.

#### **Returns:**

*  (<a href="#Account">Account</a>) Logged-in account object.

### augur.accounts.login(p, callback)

Logs into an account using a password 

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.password`**  (string) Password for the account being imported.
    * **`p.address`**  (string) Ethereum address for this account, as a hexadecimal string.
    * **`p.keystore`**  (<a href="#Keystore">Keystore</a>) Keystore object containing this account's encryption parameters.
* **`callback`** (function) Called after the account has been successfully generated.

#### **Returns:**

*  (<a href="#Account">Account</a>) Logged-in account object.

### augur.accounts.loginWithMasterKey(p)

Logs into an account with a user-supplied plaintext private key.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.privateKey`** (buffer|string) The private key for this account, as a Buffer or a hexadecimal string.

#### **Returns:**

*  (<a href="#NoKeystoreAccount">NoKeystoreAccount</a>) Logged-in account object (note: does not have a keystore property).

### augur.accounts.logout()

Logs out the account that is currently logged in.

Assets Functions
----------------
```javascript
augur.assets.sendEther({
    etherToSend: "0.001", 
    from: "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
    to: "0x40485264986740c8fb3d11e814bd94cf86012d29", 
    meta: {
        signer: [251, 62, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 51],
        accountType: "privateKey"
    },
    onSent: function() {},
    onSuccess: function(result) {
        console.log(result);
    },
    onFailed: function() {},
});
// example output:
{
  "callReturn": null,
  "blockHash": "0xbb21d95d9370d88fd7a8259300476b1084cfcf54ae6c6684f77a60876107c980",
  "blockNumber": 1599690,
  "from": "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
  "gas": "0xcf08",
  "gasPrice": "0x4a817c800",
  "hash": "0xf6f476c21bb4084729d268eb1392ac9bfc1b543fb89862ad188e1c3f2db4f4b7",
  "input": "0x3af39c21",
  "nonce": "0x16",
  "to": "0x40485264986740c8fb3d11e814bd94cf86012d29",
  "transactionIndex": "0x0",
  "value": "0x38d7ea4c68000",
  "v": "0x2b",
  "r": "0x5239d5ccf324ed41125e63ef6b49ba5f79a4f27678a9caad0f1c480f36faa026",
  "s": "0x33184fe7ebaa3bdfc414c0147cb902d6a9d43033f3254996a3283cc990adb50f",
  "timestamp": 1516065072,
  "gasFees": "0.00042544"
}

augur.assets.sendReputation({
    universe: "0x1f732847fbbcc46ffe859f28e916d993b2b08831",
    reputationToSend: "0.001", 
    _to: "0x40485264986740c8fb3d11e814bd94cf86012d29", 
    meta: {
        signer: [251, 62, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 51],
        accountType: "privateKey"
    },
    onSent: function() {
    },
    onSuccess: function(result) {
        console.log(result);
    },
    onFailed: function() {
    },
});

// example output:
{
  "callReturn": null,
  "blockHash": "0xd4bbd077575d2006b19570d546a6f02a4ef45d4cb91d64286026431eda313fb8",
  "blockNumber": 1599717,
  "from": "0x8fa56abe36d8dc76cf85fecb6a3026733e0a12ac",
  "gas": "0x2fd618",
  "gasPrice": "0x4a817c800",
  "hash": "0x39618f9552bad2ee24ac48fa1b634ffbe1c33a157793cd458ae2cbe68c988cde",
  "input": "0xa9059cbb00000000000000000000000040485264986740c8fb3d11e814bd94cf86012d2900000000000000000000000000000000000000000000000000038d7ea4c68000",
  "nonce": "0x17",
  "to": "0x13fa2334966b3cb6263ed56148323014c2ece753",
  "transactionIndex": "0x2",
  "value": "0x0",
  "v": "0x2c",
  "r": "0xc2b1869475afa6e7b6ecc4965da765f71fee57cd8863c340e3ce02e0ee47ab37",
  "s": "0x4b6baec6d873fa784d70ac9867005204febdf6d1d0274dbf6f774971e01daf7b",
  "timestamp": 1516065477,
  "gasFees": "0.00146872"
}
```
### augur.assets.sendEther(p)

Sends Ether to a specified Ethereum address.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.etherToSend`**  (string) Amount of Ether to send, as a base-10 string.
    * **`p.from`**  (string) Ethereum address of the sender, as a hexadecimal string.
    * **`p.to`**  (string) Ethereum address of the recipient, as a hexadecimal string.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when the transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when the transaction is sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when the transaction fails.

### augur.assets.sendReputation(p)

Sends [REP](#rep) to a specified Ethereum address. This function will trigger a [`TokensTransferred` event](#TokensTransferred) if the REP is successfully sent.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.universe`**  (string) The universe of Reputation to use.
    * **`p.reputationToSend`**  (string) Amount of Reputation to send, as a base-10 string.
    * **`p._to`**  (string) Ethereum address of the recipient, as a hexadecimal string.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when the transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when the transaction is sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when the transaction fails.

Augur-Node Functions
--------------------
```javascript
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
// example output after TokensTransferred event coming soon

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
  function(result) {
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

* (<a href="ContractAddresses">ContractAddresses</a>) An object containing the version and contract addresses.

### augur.augurNode.submitRequest(method, params, callback)

Submits an RPC request to an [Augur Node](#augur-node).

This function will fail if:

A connection to an Augur Node has not been established.

#### **Parameters:**

* **`method`**  (string) Method name to call.
* **`params`**  (Object) Parameters to pass into `method`.
* **`callback`**  (function) Called once the Augur Node responds to the RPC request. 

#### **Returns:**

* (Data type can vary based on the method being called.) Result from the RPC request. 

### augur.augurNode.subscribeToEvent(eventName, subscriptionCallback, onComplete)

Listens to an [Augur Node](#augur-node) for when a specific [event](#event-types) occurs.

This function will fail if:

A connection to an Augur Node has not been established.

#### **Parameters:**

* **`eventName`**  (string) Type of event to listen for.
* **`subscriptionCallback`**  (function) Called whenever the Augur Node detects a new occurrence of the specified event. 
* **`onComplete`**  (function) Called after attempting to set up the subscription to listen on the Augur Node.

#### **Returns:**

* (string) ID of the subscription that was created.

### augur.augurNode.unsubscribeFromEvent(subscriptionId, callback)

Stops the specified subscription from listening on an Augur Node.

This function will fail if:

A connection to an Augur Node has not been established.

#### **Parameters:**

* **`subscriptionId`**  (string) Subscription ID to stop listening.
* **`callback`**  (function) Called after attempting to stop the subscription from listening on the Augur Node.

### augur.augurNode.unsubscribeFromAllEvents(callback)

Stops the current subscriptions from listening on an Augur Node.

This function will fail if:

A connection to an Augur Node has not been established.

#### **Parameters:**

* **`callback`**  (function) Called after attempting to stop all subscriptions from listening on the Augur Node.

Connect Function
----------------
```javascript
augur.connect({ 
  ethereumNode: {  
    httpAddresses: [
      "http://127.0.0.1:8545", // local http address for Geth node
      "https://rinkeby.ethereum.nodes.augur.net" // hosted http address for Geth node on the Ethereum Rinkeby test network
    ],
    wsAddresses: [
      "ws://127.0.0.1:8546", // local websocket address for Geth node
      "wss://ws9000.augur.net" // hosted websocket address for Geth node
    ]
  },
  augurNode: "ws://127.0.0.1:9001" // local websocket address for an Augur Node
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
    networkID: "4"
    rpc: { ... }
  }
}
```
### augur.connect(connectOptions, callback)

Connects augur.js to an Ethereum node and an [Augur Node](#augur-node).

#### **Parameters:**

* **`connectOptions`** ([ConnectOptions](#ConnectOptions)) Options used to connect to an Ethereum node and an Augur Node.
* **`callback`** (function) &lt;optional> Callback function.

Create-Market Functions
----------------
```javascript
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
    signer: [251, 62, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 51],
    accountType: "privateKey"
  },
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example output coming soon

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
    signer: [251, 62, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 51],
    accountType: "privateKey"
  },
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example output coming soon

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
    signer: [251, 62, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 51],
    accountType: "privateKey"
  },
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example output coming soon

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
```
### augur.createMarket.createBinaryMarket(p)

Creates a [Binary Market](#binary-market) in a specified [Universe](#universe). This function will trigger a [`MarketCreated` event](#MarketCreated) and [`TokensTransferred` event](#TokensTransferred) if the [Market](#market) is created successfully. 

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

### augur.createMarket.createCategoricalMarket(p)

Creates a [Categorical Market](#categorical-market) in a specified [Universe](#universe). This function will trigger a [`MarketCreated` event](#MarketCreated) and [`TokensTransferred` event](#TokensTransferred) if the [Market](#market) is created successfully. 

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

### augur.createMarket.createScalarMarket(p)

Creates a [Scalar Market](#scalar-market) in a specified [Universe](#universe). This function will trigger a [`MarketCreated` event](#MarketCreated) and [`TokensTransferred` event](#TokensTransferred) if the [Market](#market) is created successfully. 

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

### augur.createMarket.getMarketCreationCost(p, callback)

Retrieves the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) amount and total Ether required to create a new [Market](#market).

Note: This function will send a transaction if needed to create the current [Reporting Window](#reporting-window).

#### **Parameters:**

* **`p`** (Object) Parameters object. 
    * **`p.universe`**  (string) [Universe](#universe) on which to create this Market.
* **`callback`** (function) Called after the Market creation cost has been looked up.

#### **Returns:**

* (<a href="#MarketCreationCost">MarketCreationCost</a>) Costs of creating a new Market.

<!-- TODO: Add link to augur.createMarket.getMarketCreationCost -->
### augur.createMarket.getMarketCreationCostBreakdown(p, callback)

Similar to `augur.createMarket.getMarketCreationCost`, but provides more detail about the ether costs required to create a new [Market](#market). These Ether costs are broken down by the gas cost paid to the [First Public Reporter](#first-public-reporter) and the cost of the [Validity Bond](#validity-bond).

Note: This function will send a transaction if needed to create the current [Reporting Window](#reporting-window).

#### **Parameters:**

* **`p`** (Object) Parameters object. 
    * **`p.universe`**  (string) [Universe](#universe) on which to create this Market.
* **`callback`** (function) Called when all Market creation costs have been looked up.

#### **Returns:**

* (<a href="#MarketCreationCostBreakdown">MarketCreationCostBreakdown</a>) Cost breakdown for creating a new Market.

Generate-Contracts-API Function
-------------------------------
### augur.generateContractApi(functionsAbi)

Generates a set of JavaScript bindings for the Solidity ABIs passed in.

#### **Parameters:**

* **`functionsAbi`** (Object) Parameters object. 

#### **Returns:**

* (Array) Two-dimensional array of contracts APIs, keyed by contract name and function name.

Markets Functions
----------------
```javascript
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

// NOTE: This function has not be implemented yet, so the format of the returned data is still pending.
augur.markets.getDisputableMarkets({ 
  feeWindow: "0x1000000000000000000000000000000000000000"
}, function (error, result) { 
  console.log(result);
});
// example output: coming soon

augur.markets.getMarketPriceHistory({
  marketID: "0x0000000000000000000000000000000000000001",
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
    timestamp: 1506474500,
  }],
}

augur.markets.getMarkets({
  universe: "0x000000000000000000000000000000000000000b"
}, function (error, result) { 
  console.log(result);
});
// example output:
[
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000003",
  "0x0000000000000000000000000000000000000011",
]

augur.markets.getMarketsAwaitingDesignatedReporting({
  universe: "0x000000000000000000000000000000000000000b",
  sortBy: "volume",
  isSortDescending: false,
}, function (error, result) { 
  console.log(result);
});
// example output:
[
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000003",
  "0x0000000000000000000000000000000000000012",
  "0x0000000000000000000000000000000000000014",
  "0x0000000000000000000000000000000000000015",
  "0x0000000000000000000000000000000000000016",
  "0x0000000000000000000000000000000000000017",
]

augur.markets.getMarketsAwaitingReporting({
  universe: "0x000000000000000000000000000000000000000b"
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
  "0x0000000000000000000000000000000000000011"
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
  creator: "0x000000000000000000000000000000000000d00d",
}, function (error, result) { 
  console.log(result);
});
// example output:
[
  {
    creationTime: 1506480015,
    marketID: " 0x0000000000000000000000000000000000000003",
  }
]

augur.markets.getMarketsInCategory({
  universe: "0x000000000000000000000000000000000000000b",
  category: "augur",
}, function (error, result) { 
  console.log(result);
});
// example output:
[
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000003",
  "0x0000000000000000000000000000000000000011",
]

augur.markets.getMarketsInfo({
  marketIDs: [
    "0x0000000000000000000000000000000000000001",
    "0x0000000000000000000000000000000000000002",
  ],
}, function (error, result) { 
  console.log(result);
});
// example output:
[
  {
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
  {
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
  marketIDs: [
    "0x0000000000000000000000000000000000000001",
    "0x0000000000000000000000000000000000000002"
  ]
}, function(error, result) {
  console.log(result);
});
// example output:
[
  {
    marketID: "0x0000000000000000000000000000000000000001",
    unclaimedFee: 0,
  },
  {
    marketID: "0x0000000000000000000000000000000000000002",
    unclaimedFee: 0,
  }
]
```
### augur.markets.getCategories(p, callback)

Returns the Market Categories in a specific [Universe](#universe).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**
* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.universe`**  (string) Contract address of the Universe from which to retrieve the categories, as a hexadecimal string.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the categories.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort categories in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of categories to return.
    * **`p.offset`**  (string) &lt;optional> Number of categories to truncate from the beginning of the results.
* **`callback`** (function) Called after the categories have been retrieved.

#### **Returns:**
* (Array.&lt;<a href="#Category">Category</a>>) Array representing the categories in the specified Universe.

<!-- TODO: Add JS example results -->
### augur.markets.getDisputableMarkets(p, callback)

This function has not been implemented yet. Returns the [Markets](#markets) in a specific [Fee Window](#fee-window) that are able to be [Disputed](#dispute), along with the value of the [Dispute Bond](#dispute-bond) needed to Dispute each Market’s result.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.feeWindow`**  (string) Contract address of the Fee Window from which to retrieve the disputable Markets.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort Markets in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`p.offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* Description pending.

### augur.markets.getMarketPriceHistory(p, callback)

Returns the prices and timestamps of a specific [Market's](#market) [Outcomes](#outcomes) over time.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.marketID`**  (string) Market contract address for which to look up orders, as a hexadecimal string.
* **`callback`** (function) Called after the price time-series has been received and parsed.

#### **Returns:**

* (<a href="#MarketPriceTimeSeries">MarketPriceTimeSeries</a>) The Market's price time-series, keyed by outcome ID.

### augur.markets.getMarkets(p, callback)

Returns an array of [Markets](#market) in a specific [Universe](#universe).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.universe`**  (string) Contract address of the Universe from which to get transfer history.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`p.offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* (Array.&lt;string>) Array of Market addresses in the Universe, as hexadecimal strings. 

### augur.markets.getMarketsAwaitingDesignatedReporting(p, callback)

Returns the [Markets](#market) in a specific [Universe](#universe) that are waiting for a [Designated Report](#designated-report) to be submitted.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.universe`**  (string) Contract address of the Universe from which to retrieve Markets, as a hexadecimal string.
    * **`p.designatedReporter`**  (string) &lt;optional> Address of a specific [Designated Reporter](#designated-reporter) by which to filter the results, as a hexadecimal string.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`p.offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* (Array.&lt;string>) Array of Market contract addresses awaiting a Designated Report, as hexadecimal strings. 

### augur.markets.getMarketsAwaitingReporting(p, callback)

Returns the [Markets](#market) in a particular [Universe](#universe) or [Fee Window](#fee-window) that have not been [Finalized](#finalized-market). Either the Universe or Fee Window must be specified.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.universe`**  (string) &lt;optional> Contract address of the Universe from which to retrieve Markets, as a hexadecimal string. If this parameter is not specified, a Reporting Window must be specified instead.
    * **`p.feeWindow`**  (string) &lt;optional> Contract address of the Fee Window from which to retrieve the Markets, as a hexadecimal string. If this parameter is not specified, a Universe must be specified instead.
    * **`p.reportingState`**  (string) &lt;optional> Description pending.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`p.offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* (Array.&lt;string>) Array of market addresses awaiting a Designated Report, as hexadecimal strings.

### augur.markets.getMarketsClosingInDateRange(p, callback)

Returns the [Markets](#market) closing between a given time range in a specific [Universe](#universe).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.universe`**  (string) Contract address of the Universe from which to get the Markets, as a hexadecimal string.
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
  **Properties:**
    * **`p.universe`**  (string) Contract address of the [Universe](#universe) from which to get the Markets, as a hexadecimal string.
    * **`p.creator`**  (string) Ethereum address of the [Market Creator](#market-creator), as a hexadecimal string.
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
  **Properties:**
    * **`p.universe`**  (string) Contract address of the [Universe](#universe) from which to get the Markets, as a hexadecimal string.
    * **`p.category`**  (string) Name of the category from which to get the Markets.
    * **`p.sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`p.isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`p.limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`p.offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* (Array.&lt;string>) Array of Market addresses in the specified category, as hexadecimal strings.

<!-- TODO: Add link to augur.trading.getOrders -->
### augur.markets.getMarketsInfo(p, callback)

Returns information about [Markets](#markets) that are stored on-contract. The returned result includes basic information about the Markets as well as information about each Market [Outcome](#outcome). It does not include Order Book information; however the function `augur.trading.getOrders` can be used to get information about [Orders](#order) for the specified Market.

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.marketIDs`**  (Array.&lt;string>) Contract addresses of the Markets for which to get details, as hexadecimal strings.
* **`callback`** (function) Called after the Market info has been retrieved.

#### **Returns:**

* (Array.&lt;<a href="#MarketInfo">MarketInfo</a>>)

### augur.markets.getUnclaimedMarketCreatorFees

Returns the amount of unclaimed [Creator Fees](#creator-fee) in a set of [Markets](#market). Fees are only available on [Finalized Markets](#finalized-market).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.marketIDs`**  (Array.<string>) Contract addresses of the Markets for which to get unclaimed Creator Fees, as 16-byte hexadecimal values.
* **`callback`** (function) Called after the Market information has been retrieved.

#### **Returns:**

* (Array.&lt;<a href="#MarketCreatorFee">MarketCreatorFee</a>>)

Reporting Functions
-------------
```javascript
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
    marketID: "0x0000000000000000000000000000000000000011",
    payout0: 0,
    payout1: 2,
    payout2: null,
    payout3: null,
    payout4: null,
    payout5: null,
    payout6: null,
    payout7: null,
    isInvalid: false,
    amountStaked: 17,
    winning: null,
    claimed: false,
    reportingState: "FIRST_REPORTING",
  },
}

// NOTE: This function has not be implemented yet, so the format of the returned data is still pending.
augur.reporting.getFeeWindowsWithUnclaimedFees({ 
  universe: "0x000000000000000000000000000000000000000b",
  account: "0x0000000000000000000000000000000000000021"
}, function (error, result) { 
  console.log(result);
});
// example output: coming soon

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
      marketID: "0x0000000000000000000000000000000000000011",
      feeWindow: "0x1000000000000000000000000000000000000000",
      payoutNumerators: [0, 2],
      amountStaked: 17,
      crowdsourcerID: "0x0000000000000000001000000000000000000001",
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
      marketID: "0x0000000000000000000000000000000000000019",
      feeWindow: "0x1000000000000000000000000000000000000000",
      payoutNumerators: [1, 1],
      amountStaked: 229,
      crowdsourcerID: "0x0000000000000000001000000000000000000003",
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
  "AWAITING_FINALIZATION": 1,
  "DESIGNATED_REPORTING": 8,
  "FIRST_REPORTING": 2,
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

[Finalizes](#finalized-market) a [Market](#market), meaning it sets the winning [Payout Distribution Hash](#payout-distribution-hash) for the Market, redistributes [REP](#rep) Staked on non-winning [Outcomes](#outcome) to REP holders who Staked on the winning Outcome, and distributes the [Validity Bond](#validity-bond) based on whether the Market resolved as [Invalid](#invalid-outcome). Then, once the [Post-Finalization Waiting Period](post-finalization-waiting-period) has elapsed, users can [Settle](#settlement) their [Shares](#share). This transaction will trigger a [`MarketFinalized` event](#MarketFinalized) if the Market Finalized without any errors.

This transaction will fail if:

* The [Initial Report](#initial-report) has not been submitted.
* The [Fee Window](#fee-window) has not ended.
* The Market passed in is a Forked Market.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.market`**  (string) Address of the Market to Finalize, as a hex string.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when the transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when the transaction is sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when the transaction fails.

### augur.reporting.getDisputeTokens(p, callback)

Returns the Dispute Tokens owned by a specific user that are either unclaimed or are in [Markets](#market) that have not been [Finalized](#finalized-market).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.universe`**  (string) Contract address of the [Universe](#universe) in which to retrieve the Dispute Tokens, as a hexadecimal string.
    * **`p.account`**  (string) Contract address of the account for which to retrieve the Dispute Tokens, as a hexadecimal string.
    * **`p.disputeTokenState`**  (<a href="#DISPUTE_TOKEN_STATE">DISPUTE_TOKEN_STATE</a>) &lt;optional> Token state by which to filter results.
* **`callback`** (function) Called when reporting history has been received and parsed.

#### **Returns:**

* (Array.&lt;<a href="#DisputeToken">DisputeToken</a>>) Dispute Token details, keyed by Dispute Token ID.

<!-- TODO: Verify description once function is completed. (Make sure it matches returned result.) Add JS example results -->
### augur.reporting.getFeeWindowsWithUnclaimedFees(p, callback)

This function has not been implemented yet. Returns the [Fee Windows](#fee-window) where a specific user has unclaimed [Reporting Fees](#reporting-fee).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.universe`**  (string) Ethereum contract address of the Universe in which the Fee Windows exist, as a hexadecimal string.
    * **`p.account`**  (string) Ethereum address of the user who has unclaimed Reporting Fees, as a hexadecimal string.
* **`callback`** (function) Called after the Fee Windows have been retrieved.

#### **Returns:**

* Description pending.

### augur.reporting.getReportingHistory(p, callback)

Returns information about the [Reports](#report) submitted by a particular user. For [Fee Windows](#fee-window) that have ended, this includes the [Final Outcome](#final-outcome) of the [Market](#market), whether the user’s Report matched that Final Outcome, how much REP the user gained or lost from redistribution, and how much the user earned in [Reporting Fees](#reporting-fee).

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.reporter`**  (string) Ethereum address of the [Reporter](#reporter) for which to retrieve reporting history, as a hexadecimal string.
    * **`p.universe`**  (string) &lt;optional> Contract address of the [Universe](#universe) in which to look up the reporting history, as a hexadecimal string. Either this parameter, the Market ID, or the Reporting Window must be specified.
    * **`p.marketID`**  (string) &lt;optional> Contract address of the Market in which to look up the reporting history, as a hexadecimal string. Either this parameter, the Universe, or the Reporting Window must be specified.
    * **`p.feeWindow`**  (string) &lt;optional> Contract address of the Fee Window in which to look up the reporting history, as a hexadecimal string. Either this parameter, the Universe, or the Market ID must be specified.
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
  **Properties:**
    * **`p.feeWindow`**  (string) Contract address of the [Fee Window](#fee-window) for which to get the summary, as a hexadecimal string.
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
  _markets: [
    "0x0000000000000000000000000000000000000001",
    "0x0000000000000000000000000000000000000002",
  ],
  meta: {
    signer: [251, 62, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 51],
    accountType: "privateKey"
  },
  onSent: function() {},
  onSuccess: function(result) {
    console.log(result);
  },
  onFailed: function() {}
});
// example output coming soon

augur.trading.claimTradingProceeds({
  _market: "0x0000000000000000000000000000000000000001",
  meta: {
    signer: [251, 62, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 51],
    accountType: "privateKey"
  },
  onSent: function() {},
  onSuccess: function(result) {
    console.log(result);
  },
  onFailed: function() {}
});
// example output coming soon

augur.trading.denormalizePrice({
  minPrice: "0",
  maxPrice: "2",
  normalizedPrice: "0.2",
});
// example output:
"0.4"

augur.trading.getBetterWorseOrders({
  marketID: "0x0000000000000000000000000000000000000001",
  outcome: 0,
  orderType: "buy",
  price: 0.65,
}, function (error, result) { 
  console.log(result);
});
// example output: 
{
  betterOrderID: "0x1000000000000000000000000000000000000000000000000000000000000000",
  worseOrderID: "0x2000000000000000000000000000000000000000000000000000000000000000",
}

augur.trading.getOrders({
  marketID: "0x0000000000000000000000000000000000000001",
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
          orderID: "0x1000000000000000000000000000000000000000000000000000000000000000",
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
          orderID: "0x2000000000000000000000000000000000000000000000000000000000000000",
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
          orderID: "0x5000000000000000000000000000000000000000000000000000000000000000",
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
          orderID: "0x3000000000000000000000000000000000000000000000000000000000000000",
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
      marketID: "0x0000000000000000000000000000000000000001",
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
    marketID: "0x0000000000000000000000000000000000000001",
    outcome: 0,
    shareToken: "0x1000000000000000000000000000000000000000",
    timestamp: 1506474500,
    tradeGroupID: null,
  }
]

augur.trading.getUserTradingPositions({
  account: "0x000000000000000000000000000000000000d00d",
  universe: "0x000000000000000000000000000000000000000b",
  marketID: null,
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
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 0,
    "numShares": 0.2,
    "numSharesAdjustedForUserIntention": 0.2,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 11,
    "averagePrice": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 1,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 2,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 3,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 4,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 5,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 6,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
    "averagePrice": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 7,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
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
  tickSize: "0.0001",
  _direction: 0,
  _market: "MARKET_ADDRESS",
  _outcome: 2,
  _tradeGroupId: "0x1",
  doNotCreateOrders: false,
  meta: {
    signer: [251, 62, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 51],
    accountType: "privateKey"
  },
  onSent: function() {},
  onSuccess: function(result) {
      console.log(result);
  },
  onFailed: function() {},
});
// example output coming soon

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
  marketOrderBook: {
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
  _direction: 1,
  _market: "MARKET_ADDRESS",
  _outcome: 2,
  _tradeGroupId: "0x1",
  doNotCreateOrders: true,
  meta: {
      signer: [251, 62, 32, 94, 233, 213, 105, 71, 89, 162, 243, 247, 56, 81, 213, 103, 239, 75, 212, 240, 234, 95, 8, 201, 217, 55, 225, 0, 85, 109, 158, 51],
      accountType: "privateKey"
  },
  onSent: function() {},
  onSuccess: function(result) {
      console.log(result);
  },
  onFailed: function() {}
});
// example output coming soon
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
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when each transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when all transactions are sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when any of the transactions fail.

### augur.trading.claimTradingProceeds(p)

Attempts to collect trading profits in Ether from a user's outstanding [Shares](#share) in a single [Finalized Market](#finalized-market).

This function will fail if:

* `_market` is not Finalized.
* The [Post-Finalization Waiting Period](#post-finalization-waiting-period) has not passed.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p._market`**  (string) [Market](#market) address for which to claim proceeds.
    * **`p.meta`**  (<a href="#Meta">Meta</a>) &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called if/when each transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called if/when all transactions are sealed and confirmed.
    * **`p.onFailed`**  (function) Called if/when any of the transactions fail.

### augur.trading.denormalizePrice(p)

Rescales a price to its display range [minPrice, maxPrice]: displayPrice = normalizedPrice*(maxPrice - minPrice) + minPrice.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p.minPrice`**  (BigNumber|string) This [Market's](#market) minimum possible price, as a BigNumber or base-10 string.
    * **`p.maxPrice`**  (BigNumber|string) This Market's maximum possible price, as a BigNumber or base-10 string.
    * **`p.normalizedPrice`**  (BigNumber|string) The price to be denormalized, as a BigNumber or base-10 string.

#### **Returns:**

* (string) Price rescaled to [minPrice, maxPrice], as a base-10 string.

<!-- Add links to section -->
### augur.trading.getBetterWorseOrders(p, callback)

Returns the IDs of the [Orders](#order) for a given [Outcome](#outcome) that have a better and worse price than the specified `price`. If no better/worse Orders exist, null will be returned. This function should be called prior to calling `augur.api.CreateOrder.publicCreateOrder` in order to get the `_betterOrderId` and `_worseOrderId` parameters that it accepts. (`_betterOrderId` and `_worseOrderId` are used to optimize the sorting of Orders on the [Order Book](#order-book).)

This function will fail if:

* A connection to an [Augur Node](#augur-node) has not been established.

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`p.marketID`**  (string) Contract address of the [Market](#market) for which to retrieve the better/worse Orders, as a hexadecimal string.
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
  **Properties:**
    * **`p.universe`**  (string) &lt;optional> Contract address of the Universe from which to retrieve orders, as a hexadecimal string. Either this parameter or the marketID must be specified.
    * **`p.marketID`**  (string) &lt;optional> Contract address of the Market from which to retrieve Orders, as a hexadecimal string. Either this parameter or the Universe must be specified.
    * **`p.outcome`**  (number) &lt;optional> Market Outcome to filter results by. Valid values are in the range [0,7].
    * **`p.creator`**  (string) &lt;optional> Ethereum address of the Order creator, as a hexadecimal string.
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
  **Properties:**
    * **`p.account`**  (string) Ethereum address of the user for which to retrieve trading history, as a hexadecimal string.
    * **`p.universe`**  (string) &lt;optional> Contract address of the [Universe](#universe) in which to look up the trading history, as a hexadecimal string. Either this parameter or the [Market](#market) ID must be specified.
    * **`p.marketID`**  (string) &lt;optional> Contract address of the Market in which to look up the trading history, as a hexadecimal string. Either this parameter or the Universe must be specified.
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
  **Properties:**
    * **`p.account`**  (string) Ethereum address of the user for which to retrieve trading positions, as a hexadecimal string.
    * **`p.universe`**  (string) &lt;optional> Contract address of the [Universe](#universe) in which to look up the trading positions, as a hexadecimal string. Either this parameter or the Market ID must be specified.
    * **`p.marketID`**  (string) &lt;optional> Contract address of the [Market](#market) in which to look up the trading positions, as a hexadecimal string. Either this parameter or the Universe must be specified.
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
    * **`p.tickSize`**  (string) The [Tick](#tick) size (interval) for this Market.
    * **`p._direction`**  (number) Order type (0 for "buy", 1 for "sell").
    * **`p._market`**  (string) Market in which to trade, as a hex string.
    * **`p._outcome`**  (number) Outcome ID to trade, must be an integer value on [0, 7].
    * **`p._tradeGroupId`**  (string) &lt;optional> ID logged with each trade transaction (can be used to group trades client-side), as a hex string.
    * **`p.doNotCreateOrders`**  (boolean) &lt;optional> If set to true, this trade will only take existing Orders off the [Order Book](#order-book), not create new ones (default: false).
    * **`p.meta`**  (<a href="#Meta">Meta</a>) Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called when the first trading transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called when the full trade completes successfully.
    * **`p.onFailed`**  (function) Called if any part of the trade fails.

### augur.trading.simulateTrade(p, callback)

Determines the sequence of makes/takes that will be executed to [Fill](#fill-order) the specified [Order](#order), and returns the user's projected balances and fees paid after this sequence is completed. Note: This function automatically normalizes [Share](#share) prices, so "display prices" can be passed in directly for `minPrice`, `maxPrice`, and `price`.

#### **Parameters:**

* **`p`** (Object) Trade simulation parameters.
    * **`p.orderType`**  (number) Order type (0 for "buy", 1 for "sell").
    * **`p.outcome`**  (number)  [Outcome](#outcome) ID to trade, must be an integer value on [0, 7].
    * **`p.shareBalances`**  (string[])  Number of Shares the user owns of each Outcome in ascending order, as an array of base-10 strings.
    * **`p.tokenBalance`**  (string)  Number of tokens (e.g., wrapped Ether) the user owns, as a base-10 string.
    * **`p.userAddress`**  (string)  The user's Ethereum address, as a hexadecimal string.
    * **`p.minPrice`**  (string)  This [Market's](#market) minimum possible price, as a base-10 string.
    * **`p.maxPrice`**  (string)  This Market's maximum possible price, as a base-10 string.
    * **`p.price`**  (string)  Limit price for this Order (i.e. the worst price the user will accept), as a base-10 string.
    * **`p.shares`**  (string) Number of Shares to trade, as a base-10 string.
    * **`p.marketCreatorFeeRate`**  (string) The fee rate charged by the Market creator (e.g., pass in "0.01" if the fee is 1%), as a base-10 string.
    * **`p.marketOrderBook`**  (MarketOrderBook) The full [Order Book](#order-book) (buy and sell) for this Market and Outcome.
    * **`p.shouldCollectReportingFees`**  (boolean) &lt;optional> False if reporting fees are not collected; this is rare and only occurs in disowned Markets (default: true).
 
#### **Returns:**

* (SimulatedTrade) Projected fees paid, shares and tokens spent, and final balances after the trade is complete.

### augur.trading.tradeUntilAmountIsZero(p, callback)

* Description pending.

#### **Parameters:**

* **`p`** (Object) Parameters object.
    * **`p._price`**  (string) Display (non-normalized) limit price for this trade, as a base-10 string.
    * **`p._fxpAmount`**  (string) Number of [Shares](share) to trade, as a base-10 string.
    * **`p.numTicks`**  (string) The number of [Ticks](#tick) for this [Market](#market).
    * **`p.tickSize`**  (string) The Tick size (interval) for this Market.
    * **`p._direction`**  (number) [Order](#order) type (0 for "buy", 1 for "sell").
    * **`p._market`**  (string) Market in which to trade, as a hex string.
    * **`p._outcome`**  (number) [Outcome](#outcome) ID to trade, must be an integer value on [0, 7].
    * **`p._tradeGroupId`**  (string} &lt;optional> ID logged with each trade transaction (can be used to group trades client-side), as a hexadecimal string.
    * **`p.doNotCreateOrders`**  (boolean} &lt;optional> If set to true, this trade will only take existing orders off the book, not create new ones (default: false).
    * **`p.meta`**  (<a href="#Meta">Meta</a>} &lt;optional> Authentication metadata for raw transactions.
    * **`p.onSent`**  (function) Called when the first trading transaction is broadcast to the network.
    * **`p.onSuccess`**  (function) Called when the full trade completes successfully.
    * **`p.onFailed`**  (function) Called if any part of the trade fails.

Type Definitions
-------------
<!-- Add glossary links to section -->
<a name="Account"></a>
### Account  (Object)

#### **Properties:** 
* **`address`** (string) This account's Ethereum address, as a hexadecimal string.
* **`keystore`** (<a href="#Keystore">Keystore</a>) Keystore object containing this account's encryption parameters.
* **`privateKey`** (buffer) The private key for this account.
* **`derivedKey`** (buffer) The secret key (derived from the password) used to encrypt this account's private key.

<a name="AccountTransfer"></a>
### AccountTransfer  (Object)

#### **Properties:** 
* **`transactionHash`** (string) Hash returned by token transfer.
* **`logIndex`** (number) Number of the log index position in the Ethereum block containing the transfer.
* **`creationBlockNumber`** (number) Number of the Ethereum block containing the transfer.
* **`blockHash`** (string) Hash of the Ethereum block containing the transfer.
* **`creationTime`** (number) Timestamp, in seconds, when the Ethereum block containing the transfer was created.
* **`sender`** (string|null) Ethereum address of the token sender. If null, this indicates that new tokens were minted and sent to the user.
* **`recipient`** (string|null) Ethereum address of the token recipient. If null, this indicates that tokens were burned (i.e., destroyed).
* **`token`** (string) Contract address of the contract for the sent token, as a hexadecimal string.
* **`value`** (number) Quantity of tokens sent.
* **`symbol`** (string|null) Token symbol (if any).
* **`outcome`** (number|null) Market Outcome with which the token is associated (if any).
* **`marketID`** (string|null) Contract address of the Market in which the tranfer took place, as a hexadecimal string (if any).

<a name="AugurEventLog"></a>
### AugurEventLog  (Object)

#### **Properties:** 
* **`key`**  (string) A 32-byte hexadecimal value. Description pending.
* **`addition`**  (string) A 20-byte hexadecimal value. Description pending.
* **`commitHash`**  (string) A 20-byte hexadecimal value. Description pending.
* **`bytecodeHash`**  (string) A 32-byte hexadecimal value. Description pending.
* **`address`**  (string) A 20-byte hexadecimal value. Description pending.
* **`removed`**  (boolean) Whether the transaction this event was created from was removed from the Ethereum blockchain (due to an orphaned block) or never gotten to (due to a rejected transaction).
* **`transactionHash`**  (string) Hash of the transactions this log was created from, as a 32-byte hexadecimal value.
* **`transactionIndex`**  (number) Integer of the transaction's index position in the block.
* **`logIndex`**  (number) Integer of the log index position in the block.
* **`blockNumber`**  (number) Number of the block on the Ethereum blockchain where the event was logged.
* **`contractName`**  (string) Name of the Solidity contract in which the event is defined.
* **`eventName`**  (string) Name of the event type being logged.

<a name="BetterWorseOrders"></a>
### BetterWorseOrders  (Object)

#### **Properties:** 
* **`betterOrderID`** (string|null) ID of the order with the next best price over the specified order ID, as a hexadecimal string.
* **`worseOrderID`** (string|null) ID of the order with the next worse price over the specified order ID, as a hexadecimal string.

<a name="ContractAddresses"></a>
### ContractAddresses  (Object)

#### **Properties:** 
* **`version`** (string) Version of the contracts.
* **`addresses`** (Object) Object containing the 16-byte Ethereum contract addresses used by Augur, keyed by contract name.

<a name="Category"></a>
### Category  (Object)

#### **Properties:** 
* **`category`** (string) Name of a Category.
* **`popularity`** (number|string) Category popularity. (The exact method for calculating this value is still pending.)

<a name="CipherParams"></a>
### CipherParams  (Object)

#### **Properties:** 
* **`iv`** (string) Initialization vector used for this account, as a hexadecimal string.

<a name="ConnectOptions"></a>
### ConnectOptions  (Object)

#### **Properties:** 
* **`ethereumNode`** (<a href="#EthereumNode">EthereumNode</a>) Object containing information on how to connect to a desired Ethereum node, either locally or remotely (hosted).
* **`augurNode`** (string) &lt;optional> Websocket address of an [Augur Node](#augur-node).

<a name="EthereumNode"></a>
### EthereumNode  (Object)

#### **Properties:** 
* **`http`** (string|null) HTTP address of an Ethereum node.
* **`httpAddresses`** (Array.&lt;string>|null) Array of HTTP Ethereum node addresses. (Can be used instead of `http` to specify a list of HTTP addresses to iterate through until a connection is established.)
* **`ws`** (string|null) Websocket address of an Ethereum node.
* **`wsAddresses`** (Array.&lt;string>|null) Array of websocket Ethereum node addresses. (Can be used instead of `ws` to specify a list of websocket addresses to iterate through until a connection is established.)
* **`ipc`** (string|null) IPC address of an Ethereum node.
* **`ipcAddresses`** (Array.&lt;string>|null) Array of IPC Ethereum node addresses. (Can be used instead of `ipc` to specify a list of IPC addresses to iterate through until a connection is established.)
* **`networkID`** (string|null) Description pending.

<a name="ExtraInfo"></a>
### ExtraInfo  (Object)

#### **Properties:** 
* **`resolutionSource`** (string|null) Source that should be referenced when determining the Outcome of a Market.
* **`tags`** (Array.&lt;string>|null) Keywords used to tag the Market (maximum of 2).
* **`longDescription`** (string|null) Additional information not included in description of the Market.

<a name="Keystore"></a>
### Keystore  (Object)

#### **Properties:** 
* **`address`** (string) This account's Ethereum address, as a hexadecimal string.
* **`crypto`** (<a href="#KeystoreCrypto">KeystoreCrypto</a>) Parameters used to encrypt this account's private key.
* **`id`** (string) This account's UUID.
* **`version`** (number) Keystore version number (usually 3).

<a name="KeystoreCrypto"></a>
### KeystoreCrypto  (Object)

#### **Properties:** 
* **`cipher`** (string) The symmetric cipher used to encrypt this account's private key (usually aes-128-ctr).
* **`ciphertext`** (string) This account's encrypted private key, as a hexadecimal string.
* **`cipherparams`** (<a href="#CipherParams">CipherParams</a>) Object containing the initialization vector for this account.
* **`kdf`** (string) Key derivation function name (usually scrypt; pbkdf2 is also supported).
* **`kdfparams`** (<a href="#ScryptParams">ScryptParams</a>|<a href="#Pbkdf2Params">Pbkdf2Params</a>) Key derivation function parameters.
* **`mac`** (string) Message authentication code, as a hexadecimal string.

<a name="MarketCreationCost"></a>
### MarketCreationCost  (Object)

#### **Properties:** 
* **`designatedReportNoShowReputationBond`** (string) Amount of Reputation required to incentivize the designated reporter to show up and report, as a base-10 string.
* **`etherRequiredToCreateMarket`** (string) Sum of the Ether required to pay for Reporters' gas costs and the validity bond, as a base-10 string.

<a name="MarketCreationCostBreakdown"></a>
### MarketCreationCostBreakdown  (Object)

#### **Properties:** 
* **`designatedReportNoShowReputationBond`** (string) Amount of Reputation required to incentivize the designated reporter to show up and report, as a base-10 string.
* **`targetReporterGasCosts`** (string) Amount of Ether required to pay for the gas to Report on this Market, as a base-10 string.
* **`validityBond`** (string) Amount of Ether to be held on-contract and repaid when the Market is resolved with a non-Invalid Outcome, as a base-10 string.

<a name="MarketCreatorFee"></a>
### MarketCreatorFee  (Object)

#### **Properties:** 
* **`marketID`** (string) Address of a Market, as a hexadecimal string.
* **`unclaimedFee`** (number|string) Fee available to be claimed from the Market, by the Market Creator, in attoETH.

<a name="MarketInfo"></a>
### MarketInfo  (Object)

#### **Properties:** 
* **`id`** (string) Address of a Market, as a hexadecimal string.
* **`universe`** (string) Address of a Universe, as a hexadecimal string.
* **`marketType`** (string) Type of Market ("binary", "categorical", or "scalar").
* **`numOutcomes`** (number) Total possible Outcomes for the Market.
* **`minPrice`** (number) Minimum price allowed for a share on a Market, in ETH. For binary & categorical Markets, this is 0 ETH. For Scalar Markets, this is the bottom end of the range set by the Market creator.
* **`maxPrice`** (number) Maximum price allowed for a share on a Market, in ETH. For binary & categorical Markets, this is 1 ETH. For Scalar Markets, this is the top end of the range set by the Market creator.
* **`cumulativeScale`** (string) Difference between maxPrice and minPrice.
* **`author`** (string) Ethereum address of the creator of the Market, as a hexadecimal string.
* **`creationTime`** (number) Timestamp when the Ethereum block containing the Market creation was created, in seconds.
* **`creationBlock`** (number) Number of the Ethereum block containing the Market creation.
* **`creationFee`** (number) Fee paid by the Market Creator to create the Market, in ETH.
* **`reportingFeeRate`** (number) Percentage rate of ETH sent to the Reporting Window containing the Market whenever shares are settled. Reporting fees are later used to pay REP holders for Reporting on the Outcome of Markets.
* **`marketCreatorFeeRate`** (number) Percentage rate of ETH paid to the Market creator whenever shares are settled.
* **`marketCreatorFeesCollected`** (number|null) Amount of fees the Market creator collected from the Market, in ETH.
* **`category`** (string) Name of the category the Market is in.
* **`tags`** (Array.&lt;(string|null)>) Names with which the Market has been tagged.
* **`volume`** (number) Trading volume for this Outcome. (Method for calculating this is pending.)
* **`outstandingShares`** (number) Total shares in the Market. (Method for calculating this is pending.)
* **`reportingState`** (<a href="#REPORTING_STATE">REPORTING_STATE</a>|null) Reporting state name.
* **`feeWindow`** (string) Contract address of the Fee Window the Market is in, as a hexadecimal string.
* **`endDate`** (number) Timestamp when the Market event ends, in seconds.
* **`finalizationTime`** (number|null) Timestamp when the Market was finalized (if any), in seconds.
* **`description`** (string) Description of the Market.
* **`extraInfo`** (string|null) Stringified JSON object containing resolutionSource, tags, longDescription, and outcomeNames (for categorical Markets).
* **`designatedReporter`** (string) Ethereum address of the Market's designated report, as a hexadecimal string.
* **`designatedReportStake`** (number) Amount of ETH the designated reporter staked on the Outcome submitted in the designated report.
* **`resolutionSource`** (string|null) Reference source used to determine the Outcome of the Market event.
* **`numTicks`** (number) Number of possible prices, or ticks, between a Market's minimum price and maximum price.
* **`consensus`** (number|null) Consensus Outcome for the Market.
* **`outcomes`** (Array.&lt;<a href="#OutcomeInfo">OutcomeInfo</a>>) Array of OutcomeInfo objects.

<a name="MarketOrderBook"></a>
### MarketOrderBook  (Object)

#### **Properties:** 
* **`1`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 1 of this market.
* **`2`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 2 of this market.
* **`3`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 3 of this market.
* **`4`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 4 of this market.
* **`5`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 5 of this market.
* **`6`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 6 of this market.
* **`7`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 7 of this market.
* **`8`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 8 of this market.

<a name="MarketPriceTimeSeries"></a>
### MarketPriceTimeSeries  (Object)

#### **Properties:** 
* **`Price`** (<a href="#SingleOutcomePriceTimeSeries">SingleOutcomePriceTimeSeries</a>) time-series for a single Outcome, keyed by Outcome ID.

<a name="MarketsContractAddressRow"></a>
### MarketsContractAddressRow  (Object)

#### **Properties:** 
* **`creationTime`** (number) Unix time when the Market was created.
* **`marketID`** (string) Address of a Market, as a hexadecimal string.

<a name="Meta"></a>
### Meta  (Object)

Authentication metadata for raw transactions.

#### **Properties:** 
* **`signer`** (buffer|function) Private key buffer or a signing function provided by a hardware wallet, of the account initiating the transaction.
* **`accountType`** (string) Type of account that is signing the transaction (e.g. "privateKey").

<a name="NoKeystoreAccount"></a>
### NoKeystoreAccount  (Object)

#### **Properties:** 
* **`address`** (string) This account's Ethereum address, as a hexadecimal string.
* **`privateKey`** (buffer) The private key for this account.
* **`derivedKey`** (buffer) The secret key (derived from the password) used to encrypt this account's private key.

<a name="Order"></a>
### Order  (Object)

#### **Properties:** 
* **`orderID` ** (string) ID of the order, as a hexadecimal string.
* **`shareToken`** (string) Contract address of the share token for which the order was placed, as a hexadecimal string.
* **`transactionHash`** (string) Hash to look up the order transaction receipt.
* **`logIndex`** (number) Number of the log index position in the Ethereum block containing the order transaction.
* **`owner`** (string) The order maker's Ethereum address, as a hexadecimal string.
* **`creationTime`** (number) Timestamp, in seconds, when the Ethereum block containing the order transaction was created.
* **`creationBlockNumber`** (number) Number of the Ethereum block containing the order transaction.
* **`orderState`** (<a href="#ORDER_STATE">ORDER_STATE</a>) State of orders by which to filter results. Valid values are "ALL", "CANCELLED", "CLOSED", & "OPEN".
* **`price`** (number) Rounded display price, as a base-10 number.
* **`amount`** (number) Rounded number of shares to trade, as a base-10 number.
* **`fullPrecisionPrice`** (string) Full-precision (un-rounded) display price, as a base-10 number.
* **`fullPrecisionAmount`** (number) Full-precision (un-rounded) number of shares to trade, as a base-10 number.
* **`tokensEscrowed`** (number) Number of the order maker's tokens held in escrow, as a base-10 number.
* **`sharesEscrowed`** (number) Number of the order maker's shares held in escrow, as a base-10 number.

<a name="ORDER_STATE"></a>
### ORDER_STATE  (Object)

Serves as an enum for the state of an order.

#### **Properties:** 
* **`ALL`** (string) Order is open, closed, or cancelled. (If no order state is specified, this is the default value.)
* **`OPEN`** (string) Order is available to be filled.
* **`CLOSED`** (string) Order has been filled.
* **`CANCELED`** (string) Order has been canceled (although it may have been partially filled).

<a name="OutcomeInfo"></a>
### OutcomeInfo  (Object)

#### **Properties:** 
* **`id`** (number) Market Outcome ID
* **`volume`** (number) Trading volume for this Outcome. (Method for calculating this is pending.)
* **`price`** (number) Price of the Outcome.
* **`description`** (string|null) Description for the Outcome.

<a name="Pbkdf2Params"></a>
### Pbkdf2Params  (Object)

#### **Properties:** 
* **`dklen`** (number) Key length in bytes (usually 32).
* **`c`** (number) Number of PBKDF2 iterations used to derive the secret key.
* **`prf`** (string) Pseudorandom function used with PBKDF2 (usually hmac-sha256).
* **`salt`** (string) The dklen-byte salt used for this account, as a hexadecimal string.

<a name="ProfitLoss"></a>
### ProfitLoss  (Object)

#### **Properties:**
  * **`position`**  (string) Description pending.
  * **`meanOpenPrice`**  (string) Description pending.
  * **`realized`**  (string) Description pending.
  * **`queued`**  (string) Description pending.
  * **`unrealized`**  (string) Description pending.

<a name="Report"></a>
### Report  (Object)

#### **Properties:** 
* **`transactionHash`** (string) Hash to look up the reporting transaction receipt.
* **`logIndex`** (number) Number of the log index position in the Ethereum block containing the reporting transaction.
* **`creationBlockNumber`** (number) Number of the Ethereum block containing the reporting transaction.
* **`blockHash`** (string) Hash of the Ethereum block containing the reporting transaction.
* **`creationTime`** (number) Timestamp, in seconds, when the Ethereum block containing the reporting transaction was created.
* **`marketID`** (string) Contract address of the Market, as a hexadecimal string.
* **`feeWindow`** (string) Fee Window the Market is in currently.
* **`payoutNumerators`** (Array.&lt;number>) Array representing the payout set.
* **`amountStaked`** (number) Description the reporter has staked on the Outcome of their report.
* **`crowdsourcerID`** (string) Ethereum contract address of the Dispute Crowdsourcer, as a hexadecimal string.
* **`isCategorical`** (boolean) Whether the Market is a Categorical Market.
* **`isScalar`** (boolean) Whether the Market is a Scalar Market.
* **`isInvalid`** (boolean) Whether the Market is [Invalid](#invalid-outcome).
* **`isSubmitted`** (boolean) Description pending.

<a name="REPORTING_STATE"></a>
### REPORTING_STATE  (Object)

Serves as an enum for the state of a stake token.
#### **Properties:** 
* **`PRE_REPORTING`** (string) Market's end time has not yet come to pass.
* **`DESIGNATED_REPORTING`** (string) Market's end time has occurred, and it is pending a designated report.
* **`DESIGNATED_DISPUTE`** (string) Market's designated report has been submitted and is allowed to be disputed.
* **`AWAITING_NO_REPORT_MIGRATION`** (string) Either the disignated report was disputed, or the designated reporter failed to submit a report, and the market is waiting for the next reporting phase to begin.
* **`FIRST_REPORTING`** (string) Market's designated report was disputed, and users can place stake on Outcomes.
* **`FIRST_DISPUTE`** (string) Market's first report has been submitted and is allowed to be disputed.
* **`LAST_REPORTING`** (string) Market's first report was disputed, and users can place stake on Outcomes.
* **`LAST_DISPUTE`** (string) Market's first report has been submitted and is allowed to be disputed to cause a fork.
* **`FORKING`** (string) Market's last report was disputed, causing a fork. Users can migrate their REP to the Universe of their choice.
* **`FINALIZED`** (string) An Outcome for the Market has been determined.
* **`AWAITING_FORK_MIGRATION`** (string) Pending documentation. Possibly deprecated.
* **`AWAITING_FINALIZATION`** (string) Pending documentation. Possibly deprecated.

<a name="ScryptParams"></a>
### ScryptParams  (Object)

#### **Properties:** 
* **`dklen`** (number) Key length in bytes (usually 32).
* **`n`** (number) Number of scrypt iterations used to derive the secret key (usually 262144).
* **`p`** (number) Parallelization factor, determines relative CPU cost (usually 1).
* **`r`** (number) Block size factor used for scrypt's hash, determines relative memory cost (usually 8).
* **`salt`** (string) The dklen-byte salt used for this account, as a hexadecimal string.

<a name="SingleOutcomeOrderBook"></a>
### SingleOutcomeOrderBook  (Object)

#### **Properties:** 
 * **`buy`** (<a href="#SingleOutcomeOrderBookSide">SingleOutcomeOrderBookSide</a>) Buy orders (bids) indexed by order ID.
 * **`sell`** (<a href="#SingleOutcomeOrderBookSide">SingleOutcomeOrderBookSide</a>)  Sell orders (asks) indexed by order ID.

<a name="SingleOutcomeOrderBookSide"></a>
### SingleOutcomeOrderBookSide  (Object)

#### **Properties:** 
* **`Buy`**|**`Sell`** (<a href="#Order">Order</a>) bid or ask orders, indexed by order ID.

<a name="SingleOutcomePriceTimeSeries"></a>
### SingleOutcomePriceTimeSeries  (Object)

#### **Properties:** 
* **`Array`** (Array.&lt;<a href="#TimestampedPrice">TimestampedPrice</a>>) of timestamped price points for this Outcome.

<a name="DISPUTE_TOKEN_STATE"></a>
### DISPUTE_TOKEN_STATE  (Object)

Serves as an enum for the state of a Dispute Token.

#### **Properties:** 
* **`ALL`** (string) Dispute Token can be in any state. (If no Dispute Token state is specified, this is the default value.)
* **`UNFINALIZED`** (string) Dispute Token is in a Market that has not been Finalized.
* **`UNCLAIMED`** (string) Dispute Token is in a Finalized Market, was staked on the correct Outcome, and has not been claimed yet.

<a name="DisputeToken"></a>
### DisputeToken  (Object)

#### **Properties:** 
* **`disputeToken`** (string) Contract address of the Dispute Token, as a hexadecimal string.
* **`marketID`** (string) ID of the Market, as a hexadecimal string.
* **`payout0`** (number|null) Payout numerator 0 of the stake token's payout set.
* **`payout1`** (number|null) Payout numerator 1 of the stake token's payout set.
* **`payout2`** (number|null) Payout numerator 2 of the stake token's payout set. Set to null for binary and scalar Markets.
* **`payout3`** (number|null) Payout numerator 3 of the stake token's payout set. Set to null for binary and scalar Markets.
* **`payout4`** (number|null) Payout numerator 4 of the stake token's payout set. Set to null for binary and scalar Markets.
* **`payout5`** (number|null) Payout numerator 5 of the stake token's payout set. Set to null for binary and scalar Markets.
* **`payout6`** (number|null) Payout numerator 6 of the stake token's payout set. Set to null for binary and scalar Markets.
* **`payout7`** (number|null) Payout numerator 7 of the stake token's payout set. Set to null for binary and scalar Markets.
* **`isInvalid`** (boolean) Whether the Market was determined to be invalid.
* **`amountStaked`** (number) Amount the stake token owner has staked, in ETH.
* **`winning`** (number|null) Description pending.
* **`claimed`** (boolean) Whether the stake token has been claimed by the owner.
* **`reportingState`** (<a href="#REPORTING_STATE">REPORTING_STATE</a>) Reporting state of the Market.

<a name="TimestampedPrice"></a>
### TimestampedPrice  (Object)

#### **Properties:** 
* **`price`** (number) Display (non-normalized) price, as a base-10 number.
* **`timestamp`** (number) Unix timestamp for this price in seconds, as an integer.

<a name="UserTrade"></a>
### UserTrade  (Object)

#### **Properties:** 
* **`transactionHash`** (string) Hash to look up the trade transaction receipt.
* **`logIndex`** (number) Number of the log index position in the Ethereum block containing the trade transaction.
* **`type`** (string) Type of trade. Valid values are "buy" and "sell".
* **`price`** (number) Description pending.
* **`amount`** (number) Description pending.
* **`maker`** (boolean) Whether the specified user is the order maker (as opposed to filler).
* **`marketCreatorFees`** (number) Amount of fees paid to Market creator, in ETH.
* **`reporterFees`** (number) Amount of fees paid to reporters, in ETH.
* **`marketID`** (string) Contract address of the Market, as a hexadecimal string.
* **`outcome`** (number) Outcome being bought/sold.
* **`shareToken`** (string) Contract address of the share token that was bought or sold, as a hexadecimal string.
* **`timestamp`** (number) Description pending.
* **`tradeGroupID`** (number|null) Description pending.

<a name="UserTradePosition"></a>
### UserTradePosition  (Object)

#### **Properties:** 
* **`marketID`** (string) Contract address of the Market, as a hexadecimal string.
* **`outcome`** (number) Outcome of the shares the user owns.
* **`numShares`** (number) Quantity of shares currently owned by the user.
* **`numSharesAdjustedForUserIntention`** (number) Description pending.
* **`realizedProfitLoss`** (number) Description pending.
* **`unrealizedProfitLoss`** (number) Description pending.
* **`averagePrice`** (number) Description pending.

<a name="WebSocket"></a>
### WebSocket  (Object)

* **`binaryType`** (string) Description pending.
* **`bufferedAmount`** (number) Description pending.
* **`extensions`** (string) Description pending.
* **`onclose`** (function) Description pending.
* **`onerror`** (function) Description pending.
* **`onmessage`** (function) Description pending.
* **`onopen`** (function) Description pending.
* **`protocol`** (string) Description pending.
* **`readyState`** (number) Description pending.
* **`url`** (string) Description pending.

<a name="WsTransport"></a>
### WsTransport  (Object)

#### **Properties:** 
* **`address`** (string) WebSocket address to connect to.
* **`timeout`** (number) Description pending.
* **`messageHandler`** (function) Description pending.
* **`workQueue`** (Array) Description pending.
* **`awaitingPump`** (boolean) Description pending.
* **`connected`** (boolean) Description pending.
* **`backoffMilliseconds`** (number) Description pending.
* **`nextListenerToken`** (number) Description pending.
* **`reconnectListeners`** (Object) Description pending.
* **`disconnectListeners`** (Object) Description pending.
* **`webSocketClient`** (<a href="#WebSocket">WebSocket</a>) WebSocket client object.
