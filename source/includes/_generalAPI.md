General API
===========
<aside class="notice">The General API section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

The Augur API is split up into three sections, the [Simplified API](#simplified-api), the [Call API](#call-api), and the [Transaction API](#transaction-api). The [Simplified API](#simplified-api) is provided to get information about [Markets](#market) on the Augur network. The [Call](#call-api) and [Transaction](#transaction-api) API's are direct contract and method mapping to the `augur.api` object. The [Call API](#call-api) uses `eth_call` to make "get" requests for information stored on the blockchain. The [Transaction API](#transaction-api) uses `eth_sendTransaction` to make "set" requests to the blockchain in order to modify the blockchain's information in some way, like creating a new [Order](#order) on the [Order Book](#order-book). The [Call](#call-api) and [Transaction](#transaction-api) APIs will be covered in greater detail in their respective sections of these docs.

In this section we will go over how to import `augur.js` into your project and connect it to an Ethereum Node. This section will also cover market creation, initial market loading, and review the Simplified API methods.

Connect to an Ethereum Node
---------------------------
```javascript
// After installing, just require augur.js to use it.
var Augur = require('augur.js');
var augur = new Augur();

var options = {
  httpAddresses: [
    'http://127.0.0.1:8545', // local http address for Geth Node
    'https://eth9000.augur.net' // hosted http address for Augur Node
  ],
  wsAddresses: [
    'ws://127.0.0.1:8545', // local websocket address for Geth Node
    'wss://ws9000.augur.net', // hosted websocket address for Augur Node
  ],
};

// Attempt to connect to a local Ethereum node
// if that fails, fall back to our hosted node
augur.connect(options, function (vitals) { /* ... */ });
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
The Augur API is a set of JavaScript bindings for the methods encoded in Augur's [smart contracts](https://github.com/AugurProject/augur-core). The API method name, as well as its parameters as keys in the `params` object, are generally identical to those of the underlying smart contract method.

Augur's [core contracts](https://github.com/AugurProject/augur-core) exist on Ethereum's decentralized network. The various serialization, networking, and formatting tasks required to communicate with the Augur contracts from a web application are carried out by Augur's [middleware](#architecture).

[augur.js](https://github.com/AugurProject/augur.js) is the Augur JavaScript SDK, and is the user-facing component of the middleware. If you want to interact with the Augur contracts from a custom application, augur.js is the recommended way to do so. The easiest way to install augur.js is using [npm](https://www.npmjs.com/package/augur.js):

`$ npm install augur.js`

or if you prefer [yarn](https://yarnpkg.com/en/):

`$ yarn add augur.js`

To use the Augur API, `augur.js` must connect to an Ethereum node, which can be either remote (hosted) or local. To specify the connection endpoint, pass your RPC connection info to `augur.connect`. Augur will go through the list of potential connections provided by the `options` argument and attempt to connect to each in turn until one of the connections is successful or all attempts fail.

In the example we have set our first connection to try as `http://127.0.0.1:8545` which is our local geth node. If Augur is unable to connect to the local geth node, then Augur will go to the next provided address. In this case we have provided a single hosted node (`https://eth9000.augur.net`) as the only other attempt to make outside of the local geth node. If a connection is successfully established then a `vitals` object will be returned, otherwise an error message will be returned.

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
To load the basic [Market](#market) info for all markets, first call `augur.markets.getMarketsInfo({ marketIDs }, callback)`. You will likely need to chunk the results so that the request does not time out. More detailed market info (including prices) for each market in each chunk (page) is then loaded using `augur.markets.getMarketInfo({ _market }, callback)`. `getMarketInfo` does not return the full [Order Book](#order-book); to get the order book for a market, call `augur.trading.orderBook.getOrderBook({ _type, _market, _outcome, minPrice, maxPrice[, _startingOrderId, _numOrdersToLoad ]}, callback)`.

<aside class="notice">Cache nodes regularly call <code>augur.markets.getMarketsInfo({ marketIDs } callback)</code>. The first time <code>getMarketsInfo</code> is called, all markets should be loaded. Subsequent <code>getMarketsInfo</code> calls should only load markets created since the previous call.</aside>

Simplified API
--------------
```javascript
augur.accounts.getAccountTransferHistory({
  account: "0x0000000000000000000000000000000000000b0b",
  token: "0x7a305d9b681fb164dc5ad628b5992177dc66aec8",
  isSortDescending: false
}, function (accountTransferHistory) { /* ... */ })
// example output:
[
  {
    transactionHash: "0x00000000000000000000000000000000000000000000000000000000deadb33f",
    logIndex: 1,
    sender: "0x0000000000000000000000000000000000000b0b",
    recipient: "0x000000000000000000000000000000000000d00d",
    token: "0x7a305d9b681fb164dc5ad628b5992177dc66aec8",
    value: 47,
    blockNumber: 1400001,
  }
]

augur.markets.getCategories({
  universe: "0x000000000000000000000000000000000000000b",
  sortBy: "popularity",
  isSortDescending: true
}, function (categoriesInfo) { /* ... */ })
// example output:
[
  { category: "finance", popularity: 12345 },
  { category: "politics", popularity: 5000 },
  { category: "ethereum", popularity: 1000 },
  { category: "augur", popularity: 500 },
]

augur.markets.getMarketInfo({
  marketID: "0x0000000000000000000000000000000000000001"
}, function (marketInfo) { /* ... */ })
// example output:
{
  id: "0x0000000000000000000000000000000000000001",
  universe: "0x000000000000000000000000000000000000000b",
  type: "categorical",
  numOutcomes: 8,
  minPrice: 0,
  maxPrice: 1,
  cumulativeScale: "1",
  author: "0x0000000000000000000000000000000000000b0b",
  creationTime: 1506473474,
  creationBlock: 1400000,
  creationFee: 10,
  reportingFeeRate: 0.02,
  marketCreatorFeeRate: 0.01,
  marketCreatorFeesCollected: 0,
  category: "test category",
  tags: ["test tag 1", "test tag 2"],
  volume: 0,
  outstandingShares: 0,
  reportingState: null,
  reportingWindow: "0x1000000000000000000000000000000000000000",
  endDate: 1506573470,
  finalizationTime: null,
  description: "This is a categorical test market created by b0b.",
  extraInfo: null,
  designatedReporter: "0x0000000000000000000000000000000000000b0b",
  designatedReportStake: 10,
  resolutionSource: "http://www.trusted-third-party.com",
  numTicks: 10752,
  consensus: null,
  outcomes: [{
    id: 0,
    outstandingShares: 100,
    price: 0.125,
  }, {
    id: 1,
    outstandingShares: 100,
    price: 0.125,
  }, {
    id: 2,
    outstandingShares: 100,
    price: 0.125,
  }, {
    id: 3,
    outstandingShares: 100,
    price: 0.125,
  }, {
    id: 4,
    outstandingShares: 100,
    price: 0.125,
  }, {
    id: 5,
    outstandingShares: 100,
    price: 0.125,
  }, {
    id: 6,
    outstandingShares: 100,
    price: 0.125,
  }, {
    id: 7,
    outstandingShares: 100,
    price: 0.125,
  }],
}

augur.markets.getMarketPriceHistory({
  marketID: "0x0000000000000000000000000000000000000001",
  sortBy: null,
  isSortDescending: null, 
  limit: null, 
  offset: null
}, function (marketPriceHistory) { /* ... */ })
// example output:
{
  0: [{
    price: 5.5,
    timestamp: 1506474500,
  }],
}

augur.markets.getMarkets({
  universe: "0x000000000000000000000000000000000000000b"
}, function (marketsMatched) { /* ... */ })
// example output:
[
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000003",
  "0x0000000000000000000000000000000000000011",
]

augur.markets.getMarketsAwaitingDesignatedReporting({
  sortBy: "volume",
  isSortDescending: false,
}, function (marketsInfo) { /* ... */ })
// example output:
[
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000003",
]

augur.markets.getMarketsClosingInDateRange({
  earliestClosingTime: 1506573450,
  latestClosingTime: 1506573470,
  universe: "0x000000000000000000000000000000000000000b",
  limit: 10,
}, function (marketsClosingInDateRange) { /* ... */ })
// example output:
[
  "0x0000000000000000000000000000000000000001",
]

augur.markets.getMarketsCreatedByUser({
  creator: "0x0000000000000000000000000000000000000b0b",
}, function (marketsCreatedByUser) { /* ... */ })
// example output:
[
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000011",
]

augur.markets.getMarketsInCategory({
  category: "augur",
}, function (marketsInCategory) { /* ... */ })
// example output:
[
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000003",
  "0x0000000000000000000000000000000000000011",
]

augur.markets.getMarketsInfo({
  universe: null,
  marketIDs: [
    "0x0000000000000000000000000000000000000001",
    "0x0000000000000000000000000000000000000002",
  ],
}, function (marketsInfo) { /* ... */ })
// example output:
[
  {
    id: "0x0000000000000000000000000000000000000001",
    universe: "0x000000000000000000000000000000000000000b",
    type: "categorical",
    numOutcomes: 8,
    minPrice: 0,
    maxPrice: 1,
    cumulativeScale: "1",
    author: "0x0000000000000000000000000000000000000b0b",
    creationTime: 1506473474,
    creationBlock: 1400000,
    creationFee: 10,
    reportingFeeRate: 0.02,
    marketCreatorFeeRate: 0.01,
    marketCreatorFeesCollected: 0,
    category: "test category",
    tags: ["test tag 1", "test tag 2"],
    volume: 0,
    outstandingShares: 0,
    reportingState: null,
    reportingWindow: "0x1000000000000000000000000000000000000000",
    endDate: 1506573470,
    finalizationTime: null,
    description: "This is a categorical test market created by b0b.",
    extraInfo: null,
    designatedReporter: "0x0000000000000000000000000000000000000b0b",
    designatedReportStake: 10,
    resolutionSource: "http://www.trusted-third-party.com",
    numTicks: 10752,
    consensus: null,
    outcomes: [{
      id: 0,
      outstandingShares: 100,
      price: 0.125,
    }, {
      id: 1,
      outstandingShares: 100,
      price: 0.125,
    }, {
      id: 2,
      outstandingShares: 100,
      price: 0.125,
    }, {
      id: 3,
      outstandingShares: 100,
      price: 0.125,
    }, {
      id: 4,
      outstandingShares: 100,
      price: 0.125,
    }, {
      id: 5,
      outstandingShares: 100,
      price: 0.125,
    }, {
      id: 6,
      outstandingShares: 100,
      price: 0.125,
    }, {
      id: 7,
      outstandingShares: 100,
      price: 0.125,
    }],
  },
  {
    id: "0x0000000000000000000000000000000000000002",
    universe: "0x000000000000000000000000000000000000000b",
    type: "binary",
    numOutcomes: 2,
    minPrice: 0,
    maxPrice: 1,
    cumulativeScale: "1",
    author: "0x0000000000000000000000000000000000000b0b",
    creationTime: 1506480000,
    creationBlock: 1400100,
    creationFee: 10,
    reportingFeeRate: 0.02,
    marketCreatorFeeRate: 0.01,
    marketCreatorFeesCollected: 0,
    category: "test category",
    tags: ["test tag 1", "test tag 2"],
    volume: 0,
    outstandingShares: 0,
    reportingState: null,
    reportingWindow: "0x1000000000000000000000000000000000000000",
    endDate: 1506573480,
    finalizationTime: null,
    description: "This is a binary test market created by b0b.",
    extraInfo: null,
    designatedReporter: "0x0000000000000000000000000000000000000b0b",
    designatedReportStake: 10,
    resolutionSource: "http://www.trusted-third-party.com",
    numTicks: 10752,
    consensus: null,
    outcomes: [{
      id: 0,
      outstandingShares: 1000,
      price: 0.5,
    }, {
      id: 1,
      outstandingShares: 1000,
      price: 0.5,
    }],
  }
]

augur.reporting.getReportingHistory({
  reporter: "0x0000000000000000000000000000000000000021",
}, function (reportingHistory) { /* ... */ })
// example output:
{
  "0x000000000000000000000000000000000000000b": {
    "0x0000000000000000000000000000000000000002": [{
      marketID: "0x0000000000000000000000000000000000000002",
      reportingWindow: "0x1000000000000000000000000000000000000000",
      payoutNumerators: [0, 2],
      amountStaked: 17,
      reportingToken: "0x0000000000000000001000000000000000000001",
      isCategorical: false,
      isScalar: false,
      isIndeterminate: false,
      isSubmitted: true,
    }],
  }
}

augur.trading.getOpenOrders({
  marketID: "0x0000000000000000000000000000000000000001",
  outcome: null,
  orderType: "buy",
  creator: null,
}, function (openOrders) { /* ... */ })
// example output:
{
  "0x0000000000000000000000000000000000000001": {
    0: {
      buy: {
        "0x1000000000000000000000000000000000000000000000000000000000000000": {
          shareToken: "0x1000000000000000000000000000000000000000",
          owner: "0x0000000000000000000000000000000000000b0b",
          creationTime: 1506473500,
          creationBlockNumber: 1400001,
          price: 0.7,
          amount: 1,
          fullPrecisionPrice: 0.7,
          fullPrecisionAmount: 1,
          tokensEscrowed: 0.7,
          sharesEscrowed: 0,
          betterOrderID: null,
          worseOrderID: null,
        },
        "0x2000000000000000000000000000000000000000000000000000000000000000": {
          shareToken: "0x1000000000000000000000000000000000000000",
          owner: "0x000000000000000000000000000000000000d00d",
          creationTime: 1506473515,
          creationBlockNumber: 1400002,
          price: 0.6,
          amount: 2,
          fullPrecisionPrice: 0.600001,
          fullPrecisionAmount: 2,
          tokensEscrowed: 1.200002,
          sharesEscrowed: 0,
          betterOrderID: null,
          worseOrderID: null,
        },
      },
    },
    1: {
      buy: {
        "0x3000000000000000000000000000000000000000000000000000000000000000": {
          shareToken: "0x2000000000000000000000000000000000000000",
          owner: "0x000000000000000000000000000000000000d00d",
          creationTime: 1506473515,
          creationBlockNumber: 1400002,
          price: 0.6,
          amount: 2,
          fullPrecisionPrice: 0.6,
          fullPrecisionAmount: 2.0000001,
          tokensEscrowed: 1.20000006,
          sharesEscrowed: 0,
          betterOrderID: null,
          worseOrderID: null,
        },
      },
    },
  },
}

augur.trading.getUserTradingHistory({
  account: "0x000000000000000000000000000000000000d00d",
  marketID: "0x0000000000000000000000000000000000000001",
  outcome: 0,
  orderType: null,
  sortBy: null,
  isSortDescending: null,
  limit: null,
  offset: null,
}, function (userTradingHistory) { /* ... */ })
// example output:
[{
  type: "sell",
  price: 5.5,
  amount: 0.2,
  maker: false,
  settlementFees: 0,
  marketID: "0x0000000000000000000000000000000000000001",
  outcome: 0,
  shareToken: "0x1000000000000000000000000000000000000000",
  timestamp: 1506474500,
  tradeGroupID: null,
}]

augur.trading.getUserTradingPositions({
  account: "0x000000000000000000000000000000000000d00d",
  marketID: null,
  outcome: null,
  sortBy: null,
  isSortDescending: null,
  limit: null,
  offset: null,
}, function (userTradingPositions) { /* ... */ })
// example output:
[
  {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 0,
    "numShares": 0.2,
    "numSharesAdjustedForUserIntention": 0.2,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 11,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 1,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 2,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 3,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 4,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 5,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 6,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
  }, {
    "marketID": "0x0000000000000000000000000000000000000001",
    "outcome": 7,
    "numShares": 0,
    "numSharesAdjustedForUserIntention": 0,
    "realizedProfitLoss": 0,
    "unrealizedProfitLoss": 0,
  }
]
```
All of the methods in the Simplified API are getter methods that use an `eth_call` RPC request; for transactional requests (`eth_sendTransaction`), see the [Transaction API](#transaction-api) section below. This API is simplified in the sense that single requests to this API can be used to fetch a large amount of data, without the need for complicated RPC batch queries.

Optional parameters are listed in brackets. Most of the Simplified API functions contain optional parameters for specifying which results to return and how to sort them.  For example, `sortBy` should be a string corresponding to the name of a particular field to sort by, and `isSortDescending` should be a boolean value for whether to sort the results in descending order. `limit` should be an integer greater than 0 that represents the maximum number of results to return. `offset` should be an integer greater than 0. It is used when only a portion of the total query results is desired, and it represents the record number at which to start returning results.

### augur.accounts.getAccountTransferHistory({ account[, token, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the token transfers made to or from the required `account` parameter, which is a string containing the account address. The optional string parameter `token` can be used to restrict the results to a particular token name.

### augur.markets.getCategories({ universe[, sortBy, isSortDescending, limit, offset] }[, callback]) 

Returns a list of all [Market](#market) categories in the required parameter `universe`, which is a string containing the address of a given [Universe](#universe).

<!-- Query TBD
augur.markets.getDisputableMarkets({
  reportingWindow: "0xacac65a0f492a910082c02bc174d3b3b7f7f1234",
  sortBy: "",
  isSortDescending: false, 
  limit: 2, 
  offset: 0
}, function (disputableMarkets) { /* ... */ })
// example output:

### augur.markets.getDisputableMarkets({ reportingWindow[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) in a specific [Reporting Window](#reporting-window) that are able to be disputed, along with the value of the [Dispute Bond](#dispute-bond) needed to dispute each Market's result. The required `reportingWindow` parameter is a string containing the address of a Reporting Window.
-->

### augur.markets.getMarketInfo({ marketID }[, callback])

Returns information about a [Market](#market) (`marketID`) that is stored on-contract. The returned result includes basic information about the market as well as information about each market [Outcome](#outcome). It does not include [Order Book](#order-book) information; however the function `augur.trading.getOpenOrders` can be used to get information about [Open Orders](#open-orders) for the specified Market.

### augur.markets.getMarketPriceHistory({ [marketID, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the prices and timestamps of [Market](#market) [Outcomes](#outcome) over time. The `marketID` parameter can be specified as a string containing a specific Market's address in order to restrict the results to a particular Market.

### augur.markets.getMarkets({ [universe, sortBy, isSortDescending, limit, offset] }[, callback])

Returns an array of all [Markets](#markets). If `universe` is specified, this will restrict the results to only Markets within that Universe[#universe].

### augur.markets.getMarketsAwaitingDesignatedReporting({ [designatedReporter, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) in the current [Reporting Window](#reporting-window) that are waiting for a [Designated Report](#designated-report) to be submitted. `designatedReporter` is an optional string parameter that can be used to restrict the results to [Markets](#market) having a specific [Designated Reporter](#designated-reporter) address.

### augur.markets.getMarketsClosingInDateRange({ earliestClosingTime, latestClosingTime, universe[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) closing between `earliestClosingTime` and `latestClosingTime` in the specified `universe`. `earliestClosingTime` and `latestClosingTime` are integers representing timestamps, and `universe` is a string containing the address of a given [Universe](#universe).

### augur.markets.getMarketsCreatedByUser({ creator[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) created by a specific user, as well as the total amount of fees earned so far by that user. `creator` is a string representing the address of the [Market Creator](#market-creator).

### augur.markets.getMarketsInCategory({ category[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the addresses of the [Markets](#market) in a specific category. `category` is a string representing the category name.

### augur.markets.getMarketsInfo({ [marketIDs, sortBy, isSortDescending, limit, offset] }[, callback])

Returns information about [Markets](#market) (`marketIDs`) that are stored on-contract. The returned result includes basic information about the markets as well as information about each market [Outcome](#outcome). It does not include [Order Book](#order-book) information; however the function `augur.trading.getOpenOrders` can be used to get information about [Open Orders](#open-orders) for the specified Market.

### augur.reporting.getReportingHistory({ reporter[, marketID, universe, reportingWindow, sortBy, isSortDescending, limit, offset] }[, callback])

Returns information about a user's reporting history. For [Reporting Windows](#reporting-window) that have ended, this includes the [Final Outcome](#final-outcome) of the [Market](#market), whether the user's [Report](#report) matched that Final Outcome, how much [REP](#rep) the user gained or lost from redistribution, and how much the user earned in [Reporting Fees](#reporting-fee).

<!-- Query TBD

augur.reporting.getReportingSummary({
 reportingWindow: "0x235665a0f492a910082c02bc174d3b3b7f7f8992", 
 sortBy: "",
 isSortDescending: false, 
 limit: 2, 
 offset: 0
}, function (reportingSummary) { /* ... */ })
// example output:

### augur.reporting.getReportingSummary({ reportingWindow[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns various information about a given [Reporting Window](#reporting-window), including total number of [Markets](#market) up for reporting, total number of Markets up for [Dispute](#dispute), total number of Markets undergoing and/or resolved via each [Reporting Round](#reporting-round). `reportingWindow` is a string containing the address of a specific Reporting Window.
-->

<!-- Query TBD

augur.reporting.getReportingWindowsWithUnclaimedFees({
  account: "0xfafa65a0f492a910082c02bc174d3b3b7f7ffafa"
}, function (reportingWindowsWithUnclaimedFees) { /* ... */ })
// example output:

### augur.reporting.getReportingWindowsWithUnclaimedFees({ account }[, callback])

Returns the [Reporting Windows](#reporting-window) where a specific user has unclaimed [Reporting Fees](#reporting-fee). `account` is a string containing a user's address.
-->

### augur.trading.getOpenOrders({ [marketID, outcome, orderType, creator, sortBy, isSortDescending, limit, offset] }[, callback])

Returns a list of [Open Orders](#open-orders) (i.e., [Orders](#order) on the [Order Book](#order-book) that have not been [Filled](#fill-order)). The results returned can be restricted by specifying `marketID` as a string containing a Market address, `outcome` as an integer, `orderType` as a string (i.e., "buy" or "sell"), and `creator` as a string containing a user address.

### augur.trading.getUserTradingHistory({ account[, marketID, outcome, orderType, sortBy, isSortDescending, limit, offset] }[, callback])

Returns information about the trades a particular user has made. `account` is a string parameter containing the address of a user. The results returned can be restricted by specifying `marketID` as a string containing a Market address, `outcome` as an integer, `orderType` as a string (i.e., "buy" or "sell")

### augur.trading.getUserTradingPositions({ account[, marketID, outcome, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the trading positions held by a particular user. `account` is a string parameter containing the address of a user. The results returned can be restricted by specifying `marketID` as a string containing a Market address.
