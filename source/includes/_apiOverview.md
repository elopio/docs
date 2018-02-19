API Overview
===========
<aside class="notice">The API Overview section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

[augur.js](https://github.com/AugurProject/augur.js) is a JavaScript SDK that is the developer-facing component of Augur's [middleware](#architecture). It can be used to query for information about Augur's [Markets](#market) and interact with Augur's [smart contracts](https://github.com/AugurProject/augur-core), and it is the recommended way to interact with Augur from a custom application. 

The documentation for augur.js is divided into four sections: the [Simplified API](#simplified-api), the [Call API](#call-api), the [Transaction API](#transaction-api), and the [Events API](#events-api). The Simplified API provides general information about Markets within Augur, and many of its functions require a connection to an [Augur Node](#augur-node). The Call and Transaction APIs provide direct mappings to Augur's smart contract functions via the `augur.api` object. The Call API uses `eth_call` to make "get" requests for information stored on the blockchain. The Transaction API uses `eth_sendTransaction` to make "set" requests to the blockchain in order to modify the blockchain's information in some way, like creating a new [Order](#order) on the [Order Book](#order-book). The Events API enables listening for certain logged events and searching for them. The Call, Transaction, and Events APIs do not require a connection to an Augur Node. Each of these four components of augur.js is covered in greater detail in their respective sections below.

This API Overview section explains how to add augur.js to a custom application and how to connect it to an Ethereum node and an Augur Node. It also covers how augur.js' account management works, how numbers should be used in augur.js, how to load and create Markets, and debugging options.

<!-- TODO: Consider moving this to the Architecture section. -->
Augur Node
---------------------------
Anyone wishing to query for information about Augur's markets will need to use the augur.js [Simplified API](#simplified-api), which requires a connection to an Augur Node for many of its functions. Augur Node is a standalone application that scans the Ethereum blockchain for all Augur event logs and stores them to a SQLite or PostgresSQL database.

Instead of looking up information about Augur-related events directly on the Ethereum blockchain, the Simplified API sends query requests to an Augur Node's database. This is because looking up information directly on the blockchain can be a slow and difficult process, especially when sorting is required. For example, to run a query for all [Markets](#markets) created by a specific user, sorted by volume and limited to 100 results, would require scanning the blockchain for all Market creation events by that user, saving the results locally, querying each Market to obtain its volume, saving those results locally, and then sorting the Markets created by the user and discarding everything after the first 100 results. This would require a large number of RPC requests and would take a long time to complete.

To alleviate this problem, the Simplified API executes queries by submitting an RPC request to an Augur Node that is either running locally or remotely (hosted). The Augur Node then runs the request against its database and returns the result. This allows queries to be run on Augur much more quickly than would otherwise be possible.

To set up a local Augur Node, follow the instructions described in the [Augur Node GitHub repository](https://github.com/AugurProject/augur-node#augur-node). Once a local Augur Node is running (or if the websocket address of a hosted node is known), a connection to it can be established by specifying the websocket address as shown by the JavaScript sample code in the [Getting Started with augur.js](#getting-started-with-augur-js) section.

<!-- TODO: add link to augur.connect function in Simplified API section once its complete. -->
Getting Started with augur.js
---------------------------
```javascript
// After installing, just require augur.js to use it.
var Augur = require('augur.js');
var augur = new Augur();

var ethereumNode = { 
  httpAddresses: [
    "http://127.0.0.1:8545", // local http address for Geth node
    "rinkeby.ethereum.nodes.augur.net" // hosted http address for Geth node on the Rinkeby test network
  ],
  wsAddresses: [
    "ws://127.0.0.1:8546", // local websocket address for Geth node
    "wss://websocket-rinkeby.ethereum.nodes.augur.net" // hosted websocket address for Geth node on the Rinkeby test network
  ]
  // ipc addresses can also be specified as:
  // ipcAddresses: [ ... ]
};
// To connect to a hosted Augur Node instead of a local Augur Node, substitute its websocket address below.
var augurNode = "ws://127.0.0.1:9001"; // local websocket address for an Augur Node

// Attempt to connect to a local Ethereum node
// If that fails, fall back to the hosted Ethereum node
augur.connect({ ethereumNode, augurNode }, function (vitals) { /* ... */ });
// example vitals object:
vitals = {
  networkID: '9000',
  blockNumber: '0xf69b5',
  coinbase: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  gasPrice: 18000000000,
  api: {
    events: { ... },
    functions: { ... },
  },
  contracts: { ... },
  rpc: { ... },
};
```
The easiest way to install augur.js is using [npm](https://www.npmjs.com/package/augur.js):

`$ npm install augur.js`

Alternatively, this can be done using [yarn](https://yarnpkg.com/en/package/augur.js), as follows:

`$ yarn add augur.js`

Once augur.js has been installed, it will need to be connected to an Ethereum node and an [Augur Node](#augur-node). These can be running locally or remotely (hosted). 

To connect to the desired Ethereum node and Augur node, call the function `augur.connect` as shown to the right. Upon doing so, augur.js will iterate through the list of addresses provided in the `ethereumNode` argument and attempt to connect to each one until a successful connection is established or all attempts have failed. The Ethereum node may have multiple HTTP, websocket, or IPC addresses specified as arrays. Similarly, augur.js will attempt to use the `augurNode` parameter to connect to an Augur Node. However, `augurNode` may only be specified as a single-address string, not as an object containing an array of addresses.

The Augur development team hosts an Augur Node and Ethereum node on the Ethereum test network, Rinkeby. They host Augur Nodes and Ethereum nodes on private chains using Clique ([Geth's](https://geth.ethereum.org/) Proof-of-Authority protocol) and Aura ([Parity's](https://www.parity.io/) Proof-of-Authority protocol). The addresses for these hosted nodes are as follows:

**Rinkeby Augur Node (WSS):** wss://rinkeby.augur.nodes.augur.net <br />
**Rinkeby Ethereum Node (HTTPS):** https://rinkeby.ethereum.nodes.augur.net <br />
**Rinkeby Ethereum Node (WSS):** wss://websocket-rinkeby.ethereum.nodes.augur.net <br />

**Clique Augur Node (WSS):** wss://clique.augur.nodes.augur.net <br />
**Clique Ethereum Node (HTTPS):** https://clique.ethereum.nodes.augur.net <br />

**Aura Augur Node (WSS):** wss://aura.augur.nodes.augur.net <br />
**Aura Ethereum Node (HTTPS):** https://aura.ethereum.nodes.augur.net <br />

The statuses of these hosted nodes can be viewed at [stats.augur.net](stats.augur.net).

In the example on the right, the first connection that will be tried is `http://127.0.0.1:8545`, which is a local Ethereum node being run using the Geth client. If a connection to the local Geth node cannot be established, the next provided address will be tried. In this case, we have provided a single hosted node on the Ethereum Rinkeby test network (`rinkeby.ethereum.nodes.augur.net`) as another attempt to make after the local Geth node. If a connection is successfully established, then the `vitals` object will be returned; otherwise an error message will be returned.

Accounts
--------
```javascript
/**
 * Create an account using the augur.accounts.register method.
 */
var newAccount;
var registerParameters = { password: "thisismysupersecurepassword" };

augur.accounts.register(registerParameters, function (error, account) {
  console.log("Error:", error);
  console.log("Account:", account);
  newAccount = account;
});

// output
Error: null
Account: {
  address: "0xb1baa74babc22bad068e3055846fb76becad7da2",
  derivedKey: Uint8Array(32) [121, 199, 100, 210, 236, 254, 150, 229, 159, 182, 49, 89, 198, 158, 135, 200, 242, 108, 111, 245, 143, 135, 3, 216, 223, 48, 95, 214, 7, 112, 106, 246],
  keystore: {
    address: "0xb1baa74babc22bad068e3055846fb76becad7da2",
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
  privateKey: Uint8Array(32) [145, 195, 95, 10, 39, 106, 79, 107, 240, 160, 184, 204, 214, 23, 139, 203, 213, 38, 245, 16, 225, 209, 165, 144, 201, 130, 146, 88, 46, 20, 169, 10]
}

/**
 * Log into an account using the augur.accounts.login method.
 */
var loginParameters = { keystore: newAccount.keystore, password: registerParameters.password };

augur.accounts.login(loginParameters, function (error, account) {
  console.log("Error:", error);
  console.log("Account:", account);
});

// output
Error: null
Account: {
  address: undefined,
  derivedKey: Uint8Array(32) [121, 199, 100, 210, 236, 254, 150, 229, 159, 182, 49, 89, 198, 158, 135, 200, 242, 108, 111, 245, 143, 135, 3, 216, 223, 48, 95, 214, 7, 112, 106, 246],
  keystore: {
    address: "0xb1baa74babc22bad068e3055846fb76becad7da2",
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
  privateKey: Uint8Array(32) [145, 195, 95, 10, 39, 106, 79, 107, 240, 160, 184, 204, 214, 23, 139, 203, 213, 38, 245, 16, 225, 209, 165, 144, 201, 130, 146, 88, 46, 20, 169, 10]
}

// augur.js does not store any account data. augur.js simply returns the important information. You can use the privateKey Buffer returned to sign your transactions.
```
augur.js includes a trustless account management system. The purpose of the accounts system is to allow people to use Augur without needing to run an Ethereum node themselves, as running a full Ethereum node can be resource-intensive.

To use the account system, the user specifies a password. Everything else is done automatically for the user. The only requirement for the password is that it be at least 6 characters long.

A private key (+ derived public key and address) is automatically generated for the user.  A secret key derived from the password using PBKDF2, along with a random 128-bit initialization vector, is used to encrypt the private key (using AES-256). Nothing is stored by augur.js. The account object will be returned to the callback provided or simply returned if no callback is provided.

The Augur UI will handle your account information for you, but if you are using augur.js on its own you will need to manage the account yourself. augur.js doesn't sign any transactions for you if you aren't using the Augur UI.

If you want to use the augur.js API directly, you will need to sign any transaction that will modify information on the blockchain (non-call transactions). All transactions take a `_signer` parameter which should be set to the sending account's `privateKey` Buffer or a function that will sign a transaction (hardware wallet).

<aside class="notice">Since the user's account key is an ordinary Ethereum private key, the user's key (and therefore their funds and Reputation) can be used with any Ethereum node. Therefore, although the accounts system is managed using an ordinary web server, since the user's funds are neither tied to nor controlled by our server, the accounts are still decentralized in the ways that (in our opinion) matter.</aside>

Numbers
-------
<b>It is strongly recommended that all numerical input values be passed into Call API functions and Transaction API functions as hexadecimal strings.</b> This is because these API functions are direct bindings to the functions on Augur's smart contracts, and passing in very large numbers in decimal form can result in the functions on Augur's smart contracts failing.

The Simplified API functions, however, automatically convert numerical input values to hexadecimal form before passing them into Augur's smart contracts.

For the Simplified API, there are three acceptable ways to pass numerical inputs:

- primitive JS numbers (e.g., `1010101`): ok for integers, but use strings for floating point numbers (see below)

- stringified numbers (e.g., `"1010101"`)

- hexadecimal strings (e.g., `"0xf69b5"`)

Floating-point (decimal) values should be passed to augur.js as strings (e.g., instead of `0.07`, use `"0.07"`), for reasons described in [enormous detail](http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html) elsewhere.

<aside class="notice"><b>All numerical parameters passed to augur.js must be either base 10 (decimal) or base 16 (hexadecimal).</b> Do <b>not</b> use the base 10<sup>18</sup> fixed-point representation that Augur uses internally for decimals!  augur.js handles all fixed-point conversions for you.</aside>

Initial Market Loading
----------------------
To get a list of all [Markets](#market), first call `augur.markets.getMarkets({ universe[, sortBy, isSortDescending, limit, offset] }[, callback])`. More detailed market info (including prices) for each market can then be loaded using `augur.markets.getMarketsInfo({ marketIDs }[, callback])`. `getMarketsInfo` does not return the [Open Orders](#order-book) for the Market; to get the Open Orders, call `augur.trading.getOrders({ [universe, marketID, outcome, orderType, creator, orderState, earliestCreationTime, latestCreationTime, sortBy, isSortDescending, limit, offset] }[, callback])`.

Debugging Options
----------------------------
```javascript
augur.rpc.setDebugOptions({ broadcast: true });
augur.api.Universe.getCurrentFeeWindow();
// example output:
packaged: {
  from: "0x56ddb80fe4e5aa05182d794526ab1eff78c90688", 
  to: "0xa1d76546015cfe50183497ca65fcbd5c656f7813", 
  data: "0x6235eef3", 
  gas: "0x2fd618", 
  returns: "address"
}
Blockchain RPC to http://127.0.0.1:8545 via SyncTransport with payload: 
{
  "id":429,
  "jsonrpc":"2.0",
  "method":"eth_call",
  "params":[{"from":"0x56ddb80fe4e5aa05182d794526ab1eff78c90688",
             "to":"0xa1d76546015cfe50183497ca65fcbd5c656f7813",
             "data":"0x6235eef3",
             "gas":"0x2fd618"},
             "latest"]
}
"0x54d134699764375417e4b5dda1e2ac62f62e9725"

augur.rpc.setDebugOptions({ connect: true });
augur.connect({ 'ethereumNode': { http: "http://rinkeby.augur.net:8545", ws: "ws://rinkeby.augur.net:8546" }, 'augurNode': "ws://127.0.0.1:9001"}, function (err, vitals) { console.log(err); console.log(vitals); });
// example output:
connecting to augur node... 
{ 
  augurNode: "ws://127.0.0.1:9001",
  ethereumNode: { http: "http://rinkeby.augur.net:8545", ws: "ws://rinkeby.augur.net:8546" }
}
connecting to ethereum node... 
{ 
  augurNode: "ws://127.0.0.1:9001",
  ethereumNode: { http: "http://rinkeby.augur.net:8545", ws: "ws://rinkeby.augur.net:8546" }
}
connected to augur
Web3: not connected
Sync: http://rinkeby.augur.net:8545
HTTP: http://rinkeby.augur.net:8545
WS: ws://rinkeby.augur.net:8546
IPC: not connected
connected to ethereum
{
  augurNode: "ws://127.0.0.1:9001",
  ethereumNode: {
    abi: {events: {...}, functions: {...}},
    blockNumber: "0x133773",
    coinbase: null,
    contracts: {...},
    gasPrice: 20000000000,
    networkID: "4",
    rpc: {...}
  }
}

augur.rpc.setDebugOptions({ tx: true });
augur.api.Universe.getOrCacheMarketCreationCost({
  onSent: function (result) {...},
  onSuccess: function (result) {...},
  onFailed: function (result) {...}
});
// example output:
payload transact: 
{
  constant: false,  
  from: "0x40485264986740c8fb3d11e814bd94cf86012d29"
  name: "getOrCacheMarketCreationCost"
  params: [],
  returns: "uint256",
  send: true,
  to: "0xa282b625053e80da550098fdb325a8ece6dfe8ac"
}
callReturn: 10000000006000000
txHash: 0x26f731c948568d9c0a4983fa134431f3fba0c68248f95d35536c9157cafa785a
```
The function `augur.rpc.setDebugOptions` allows several debugging options to be enabled:

* `broadcast` - When set to true, this enables printing of verbose, low-level information related to sending/receiving transactions, such as the transaction JSON that gets sent out over the wire, incoming eth_subscription messages, etc.

* `connect` - When set to true, this enables printing of the result of the initial connection of ethrpc to the underlying Ethereum node, as well as which endpoints are connected, on which protocols

* `tx` - When set to true, this enables printing of information related to transaction construction/submission/confirmation. This information includes the intermediate "transaction" object with human-readable parameters, the (best-guess) return value fetched from the follow-up eth_call when a transaction gets resubmitted, and the transaction hash once the transaction is submitted.
