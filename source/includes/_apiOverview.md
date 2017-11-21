API Overview
===========
<aside class="notice">The API Overview section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

Augur.js is a collection of JavaScript APIs that can be used to query for information about Augur's [Markets](#market) and interact with Augur's [smart contracts](https://github.com/AugurProject/augur-core). The documentation for augur.js is divided into four sections: the [Simplified API](#simplified-api), the [Call API](#call-api), the [Transaction API](#transaction-api), and the [Events API](#events-api). The Simplified API provides general information about Markets within Augur and requires a connection to an [Augur Node](#augur-node) (covered in the next section). The Call and Transaction APIs provide direct mappings to Augur's smart contract methods via the `augur.api` object. The Call API uses `eth_call` to make "get" requests for information stored on the blockchain. The Transaction API uses `eth_sendTransaction` to make "set" requests to the blockchain in order to modify the blockchain's information in some way, like creating a new [Order](#order) on the [Order Book](#order-book). The Events API enables listening for certain logged events and searching for them. The Call, Transaction, and Events APIs do not require a connection to an Augur Node. Each of these four components of augur.js is covered in greater detail in their respective sections below.

In this section, we will go over how to import augur.js into your project and connect it to an Ethereum Node. This section will also cover market creation, and initial market loading.

Connect to an Ethereum Node
---------------------------
```javascript
// After installing, just require augur.js to use it.
var Augur = require('augur.js');
var augur = new Augur();

var ethereumNode = { 
  httpAddresses: [
    "http://127.0.0.1:8545", // local http address for Geth node
    "https://eth9000.augur.net" // hosted http address for Geth node
  ],
  wsAddresses: [
    "ws://127.0.0.1:8546", // local websocket address for Geth node
    "wss://ws9000.augur.net" // hosted websocket address for Geth node
  ]
  // ipc addresses can also be specified as:
  // ipcAddresses: [ ... ]
};
var augurNode = "ws://127.0.0.1:9001"; // local websocket address for Augur Node

// Attempt to connect to a local Ethereum node
// if that fails, fall back to our hosted node
augur.connect({ ethereumNode, augurNode }, function (vitals) { /* ... */ });
// example vitals object:
vitals = {
  networkID: '9000',
  blockNumber: '0xf69b5',
  coinbase: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  gasPrice: 18000000000,
  api: {
    events: { /*...*/},
    functions: { /* ... */ },
  },
  contracts: { /* ... */ },
  rpc: { /* ... */ },
};
```
The Augur API is a set of JavaScript bindings for the methods encoded in Augur's smart contracts. The API method name, as well as its parameters as keys in the `params` object, are generally identical to those of the underlying smart contract method.

Augur's [core contracts](https://github.com/AugurProject/augur-core) exist on Ethereum's decentralized network. The various serialization, networking, and formatting tasks required to communicate with the Augur contracts from a web application are carried out by Augur's [middleware](#architecture).

[augur.js](https://github.com/AugurProject/augur.js) is the Augur JavaScript SDK, and is the user-facing component of the middleware. If you want to interact with the Augur contracts from a custom application, augur.js is the recommended way to do so. The easiest way to install augur.js is using [npm](https://www.npmjs.com/package/augur.js):

`$ npm install augur.js`

or if you prefer [yarn](https://yarnpkg.com/en/):

`$ yarn add augur.js`

To use the Augur API, `augur.js` must connect to an Ethereum node, which can be either remote (hosted) or local. To specify the connection endpoint, pass your RPC connection info to `augur.connect`. Augur will go through the list of potential connections provided by the `options` argument and attempt to connect to each in turn until one of the connections is successful or all attempts fail. The Ethereum node may have multiple http, websocket, or ipc addresses specified as arrays. The Augur Node, however, can only have one websocket address specified.

In the example, we have set our first connection to try as `http://127.0.0.1:8545` which is our local Geth node. If a connection to the local Geth node cannot be established, the next provided address will be tried. In this case, we have provided a single hosted node (`https://eth9000.augur.net`) as another attempt to make after the local Geth node. If a connection is successfully established, then a `vitals` object will be returned, otherwise an error message will be returned.

Connect to an Augur Node
----------
```javascript
var Augur = require("augur.js");
var augur = new Augur();

var ethereumNode = { http: "http://127.0.0.1:8545", ws: "ws://127.0.0.1:8546" };
var augurNode = "ws://127.0.0.1:9001";

augur.connect({ ethereumNode, augurNode }, (err) => {
  // do stuff
});
```
Anyone wishing to use the augur.js Simplified API to query for information about Augur's markets will need to connect to an Augur Node. An Augur Node is a standalone application that scans the Ethereum blockchain for all Augur event logs and stores them to a SQLite or PostgresSQL database.

The Simplified API runs queries against an Augur Node database rather than directly looking up information on the Ethereum blockchain because retrieving information from the blockchain can be a slow and difficult process, especially when sorting is required. For example, to run a query for all [Markets](#markets) created by a specific user, sorted by volume and limited to 100 results, would require scanning the blockchain for all Market creation events by that user, saving the results locally, querying each Market to obtain its volume, saving those results locally, and then sorting the Markets created by the user and discarding everything after the first 100 results. This would require a large number of RPC requests and would take a long time to complete.

To alleviate this problem, the Simplified API executes a query by submitting an RPC request to an Augur Node that is either running locally or remotely (hosted). The Augur Node then runs the request against its database and returns the result. This allows queries to be run on Augur much more quickly than would otherwise be possible.

To set up an Augur Node, follow the instructions described in the [Augur Node GitHub repository](https://github.com/AugurProject/augur-node#augur-node). (Alternatively, if the websocket address of a hosted Augur Node are already known, this address can be specified in the JS code without the need to set up an Augur Node.) Once the desired Augur Node is running, a connection to it can be established by specifying the websocket address as shown by the JavaScript sample code on the right.


Accounts
--------
```javascript
/**
 * Create an account using the augur.accounts.register method.
 */
var password = "thisismysupersecurepassword";

augur.accounts.register(password, function (account) {
  console.log("Account:", account);
});

// output
account = {
  address: "0x0ab5f1a15f2462eba143ecc8e1733f44dfe019bf",
  derivedKey: <Buffer ...>,
  keystore: { /* ... */ },
  privateKey: <Buffer ...>
};

var keystore = account.keystore;

augur.accounts.login(keystore, password, function (account) {
  console.log("Account:", account);
});

// output
account = {
  address: "0x0ab5f1a15f2462eba143ecc8e1733f44dfe019bf",
  derivedKey: <Buffer ...>,
  keystore: { /* ... */ },
  privateKey: <Buffer ...>
};

// Augur.js does not store any account data. Augur.js simply returns the important information. You can use the privateKey Buffer returned to sign your transactions.
```
Augur.js includes a trustless account management system. The purpose of the accounts system is to allow people to use Augur without needing to run an Ethereum node themselves, as running a full Ethereum node can be resource-intensive.

To use the account system, the user specifies a password. Everything else is done automatically for the user. The only requirement for the password is that it be at least 6 characters long.

A private key (+ derived public key and address) is automatically generated for the user.  A secret key derived from the password using PBKDF2, along with a random 128-bit initialization vector, is used to encrypt the private key (using AES-256). Nothing is stored by Augur.js, the account object will be returned to the callback provided or simply returned if no callback is provided.

The Augur UI will handle your account information for you, but if you are using Augur.js on it's own you will need to manage the account yourself. Augur.js doesn't sign any transactions for you if you aren't using the Augur UI.

If you want to use the Augur.js API directly, you will need to sign any transaction that will modify information on the blockchain (non-call transactions). All transactions take a `_signer` parameter which should be set to the sending account's `privateKey` Buffer or a function that will sign a transaction (hardware wallet).

<aside class="notice">Since the user's account key is an ordinary Ethereum private key, the user's key (and therefore their funds and Reputation) can be used with any Ethereum node. Therefore, although the accounts system is managed using an ordinary web server, since the user's funds are neither tied to nor controlled by our server, the accounts are still decentralized in the ways that (in our opinion) matter.</aside>

Numbers
-------

Before we move on, lets quickly talk about Numbers. There are three acceptable ways to pass numerical inputs to the Augur API:

- primitive JS numbers (e.g., `1010101`): ok for integers, but use strings for floating point numbers (see below)

- stringified numbers (e.g., `"1010101"`)

- hexadecimal strings (e.g., `"0xf69b5"`)

Floating-point (decimal) values should be passed to augur.js as strings (e.g., instead of `0.07`, use `"0.07"`), for reasons described in [enormous detail](http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html) elsewhere.

<aside class="notice"><b>All numerical parameters passed to augur.js must be either base 10 (decimal) or base 16 (hexadecimal).</b> Do <b>not</b> use the base 10<sup>18</sup> fixed-point representation that Augur uses internally for decimals!  augur.js handles all fixed-point conversions for you.  Do <b>not</b> send the Loch Ness monster 3.50 &times; 10<sup>18</sup> ETH.  (Probably don't even give him 3.50, but that's a debate for another time.)</aside>

Initial market loading
----------------------
To get a list of all [Markets](#market), first call `augur.markets.getMarkets({ universe[, sortBy, isSortDescending, limit, offset] }[, callback])`. More detailed market info (including prices) for each market can then be loaded using `augur.markets.getMarketsInfo({ marketIDs }[, callback])`. `getMarketsInfo` does not return the [Open Orders](#order-book) for the Market; to get the Open Orders, call `augur.trading.getOrders({ [universe, marketID, outcome, orderType, creator, orderState, earliestCreationTime, latestCreationTime, sortBy, isSortDescending, limit, offset] }[, callback])`.
