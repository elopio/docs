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
var _type = 1; // 1 is "buy", 2 is "sell"
var _market = '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42';
var _outcome = 1;
var minPrice = "0.0";
var maxPrice = "1.0";
var _startingOrderId = "0x3c3958b3cad3fb693a6fdd013a615485ef42d824aaa3bd57734f5ec21567ebdc";
var _numOrdersToLoad = 3;

augur.trading.orderBook.getOrderBook({
  _type: _type,
  _market: _market,
  _outcome: _outcome,
  minPrice: minPrice,
  maxPrice: maxPrice,
  _startingOrderId: _startingOrderId,
  _numOrdersToLoad: _numOrdersToLoad
}, function (orderBook) { /* ... */ })
// example output:
{
  buy: {
    '0x3c3958b3cad3fb693a6fdd013a615485ef42d824aaa3bd57734f5ec21567ebdc': {
      amount: '15',
      block: 220,
      fullPrecisionAmount: '15',
      fullPercisionPrice: '0.1770833333333333',
      id: '0x3c3958b3cad3fb693a6fdd013a615485ef42d824aaa3bd57734f5ec21567ebdc',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '1',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.177',
      type: 'buy',
    },
    '0x1f4f112a9aa99282e306cb58abc95b5b46199f802bd36d68f1619ba98866963a': {
      amount: '10',
      block: 217,
      fullPrecisionAmount: '10',
      fullPercisionPrice: '0.1208333333333333',
      id: '0x1f4f112a9aa99282e306cb58abc95b5b46199f802bd36d68f1619ba98866963a',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '1',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.1208',
      type: 'buy',
    },
    '0x2b054b0c9ca2fcf22ee73ba14ae41da70c7039c1a5b8125a40b9f2b68a20080b': {
      amount: '10',
      block: 219,
      fullPrecisionAmount: '10',
      fullPercisionPrice: '0.0083333333333333',
      id: '0x2b054b0c9ca2fcf22ee73ba14ae41da70c7039c1a5b8125a40b9f2b68a20080b',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '1',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.0083',
      type: 'buy',
    }
  }
}

augur.trading.orderBook.getOrderBook({
  _type: _type,
  _market: _market,
  _outcome: _outcome,
  minPrice: minPrice,
  maxPrice: maxPrice,
  _startingOrderId: _startingOrderId,
  _numOrdersToLoad: _numOrdersToLoad
}, function (orderBookChunk) { /* ... */ }, function (completeSingleOutcomeOrderBookSide) { /* ... */ });
// example chunk output:
{
  buy: {
    '0x3c3958b3cad3fb693a6fdd013a615485ef42d824aaa3bd57734f5ec21567ebdc': {
      amount: '15',
      block: 220,
      fullPrecisionAmount: '15',
      fullPercisionPrice: '0.1770833333333333',
      id: '0x3c3958b3cad3fb693a6fdd013a615485ef42d824aaa3bd57734f5ec21567ebdc',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '1',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.177',
      type: 'buy',
    },
    '0x1f4f112a9aa99282e306cb58abc95b5b46199f802bd36d68f1619ba98866963a': {
      amount: '10',
      block: 217,
      fullPrecisionAmount: '10',
      fullPercisionPrice: '0.1208333333333333',
      id: '0x1f4f112a9aa99282e306cb58abc95b5b46199f802bd36d68f1619ba98866963a',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '1',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.1208',
      type: 'buy',
    },
    '0x2b054b0c9ca2fcf22ee73ba14ae41da70c7039c1a5b8125a40b9f2b68a20080b': {
      amount: '10',
      block: 219,
      fullPrecisionAmount: '10',
      fullPercisionPrice: '0.0083333333333333',
      id: '0x2b054b0c9ca2fcf22ee73ba14ae41da70c7039c1a5b8125a40b9f2b68a20080b',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '1',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.0083',
      type: 'buy',
    }
  }
}

augur.markets.getMarketInfo({ _market: _market }, function (market) { /* ... */ });
// example output:
{
  author: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  branchID: '0xf69b5',
  consensus: null,
  creationBlock: 213,
  creationFee: '9',
  creationTime: 1502248462,
  cumulativeScale: '1',
  description: 'What will be the status of the U.S. electoral college on January 1, 2020?~|>Unchanged from 2016|Undermined but still in existence (e.g., National Popular Vote bill)|Formally abolished',
  endDate: 1577952000,
  eventBond: '4.5',
  extraInfo: 'http://www.nationalpopularvote.com',
  id: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
  makerFee: '0.01',
  maxValue: '3',
  minValue: '1',
  network: '9000',
  numOutcomes: 3,
  outcomes: [{
    id: 1,
    outstandingShares: '310',
    price: '0.6020833333333333',
    sharesPurchased: '0',
  }, {
    id: 2,
    outstandingShares: '310',
    price: '0.4333333333333333',
    sharesPurchased: '0',
  }, {
    id: 3,
    outstandingShares: '310',
    price: '0.4333333333333333',
    sharesPurchased: '0',
  }],
  tags: ['politics', 'elections', 'US politics'],
  takerFee: '0.02',
  topic: 'politics',
  tradingFee: '0.02',
  tradingPeriod: 9131,
  type: 'categorical',
  volume: '1710.399999999999999985'
}

var marketIDs = [ market, '0xe095e00863aecd814003a739da97b54c2b6737bd'];
// const account = '0xb3f636cae9e8ad9795d14d3bdda3e382dba47c73';

augur.markets.batchGetMarketInfo({ marketIDs: marketIDs }, function (marketsInfo) { /* ... */ })
// example output:
{
  '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42': {
    author: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
    branchID: '0xf69b5',
    consensus: null,
    creationBlock: 213,
    creationFee: '9',
    creationTime: 1502248462,
    cumulativeScale: '1',
    description: 'What will be the status of the U.S. electoral college on January 1, 2020?~|>Unchanged from 2016|Undermined but still in existence (e.g., National Popular Vote bill)|Formally abolished',
    endDate: 1577952000,
    eventBond: '4.5',
    extraInfo: 'http://www.nationalpopularvote.com',
    id: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
    makerFee: '0.01',
    maxValue: '3',
    minValue: '1',
    network: '9000',
    numOutcomes: 3,
    outcomes: [{
      id: 1,
      outstandingShares: '310',
      price: '0.6020833333333333',
      sharesPurchased: '0',
    }, {
      id: 2,
      outstandingShares: '310',
      price: '0.4333333333333333',
      sharesPurchased: '0',
    }, {
      id: 3,
      outstandingShares: '310',
      price: '0.4333333333333333',
      sharesPurchased: '0',
    }],
    tags: ['politics', 'elections', 'US politics'],
    takerFee: '0.02',
    topic: 'politics',
    tradingFee: '0.02',
    tradingPeriod: 9131,
    type: 'categorical',
    volume: '1710.399999999999999985'
  },
  '0xe095e00863aecd814003a739da97b54c2b6737bd': {
    author: '0x9e12c5b6067c61c811add8fb55f5d6fcb88a9e5f',
    branchID: '0xf69b5',
    consensus: null,
    creationBlock: 5156,
    creationFee: '9',
    creationTime: 1502291695,
    cumulativeScale: '1',
    description: 'Will the Bitcoin (BTC) price be equal to or higher than USD 3,400 on 31 August 2017 as shown on www.coinmarketcap.com?',
    endDate: 1504130400,
    eventBond: '4.5',
    extraInfo: 'http://www.nationalpopularvote.com',
    id: '0xe095e00863aecd814003a739da97b54c2b6737bd',
    makerFee: '0.01',
    maxValue: '2',
    minValue: '1',
    network: '9000',
    numOutcomes: 2,
    outcomes: [{
      id: 1,
      outstandingShares: '2101.2',
      price: '0',
      sharesPurchased: '0',
    }, {
      id: 2,
      outstandingShares: '2101.2',
      price: '0.99',
      sharesPurchased: '0',
    }],
    resolutionSource: 'www.coinmarketcap.com',
    tags: ['Bitcoin', 'Cryptocurrency', null],
    takerFee: '0.02',
    topic: 'Bitcoin',
    tradingFee: '0.02',
    tradingPeriod: 8704,
    type: 'binary',
    volume: '8346'
  }
}

augur.markets.getMarketsInfo({
  marketIDs: [
    '0xfb9165a0f492a910082c02bc174d3b3b7f7f979e', '0x563b377a956c80d77a7c613a9343699ad6123911'
  ]
}, function (marketsInfo) { /* ... */ })
// example output:
{
  '0xfb9165a0f492a910082c02bc174d3b3b7f7f979e': {
    author: '0x9e12c5b6067c61c811add8fb55f5d6fcb88a9e5f',
    branchID: '0xf69b5',
    consensus: null,
    creationTime: 1502248542,
    description: 'Who will win the University of Georgia vs. University of Florida football game in 2017?~|>Georgia|Florida|Vanderbilt',
    endDate: 1509346800,
    id: '0xfb9165a0f492a910082c02bc174d3b3b7f7f979e',
    makerFee: '0.01',
    maxValue: '3',
    minValue: '1',
    numOutcomes: 3,
    tags: ['sports', 'college football', 'football'],
    takerFee: '0.02',
    topic: 'sports',
    tradingFee: '0.02',
    tradingPeriod: 8734,
    type: 'categorical',
    volume: '1195.999999999999999997'
  },
  '0x563b377a956c80d77a7c613a9343699ad6123911': {
    author: '0x9e12c5b6067c61c811add8fb55f5d6fcb88a9e5f',
    branchID: '0xf69b5',
    consensus: null,
    creationTime: 1502248522,
    description: "Will Augur's live release happen by the end of August, 2017?",
    endDate: 1504249200,
    id: '0x563b377a956c80d77a7c613a9343699ad6123911',
    makerFee: '0.010000000000000000125',
    maxValue: '2',
    minValue: '1',
    numOutcomes: 2,
    tags: ['Augur', 'release date', 'Ethereum'],
    takerFee: '0.030000000000000000375',
    topic: 'Augur',
    tradingFee: '0.026666666666666667',
    tradingPeriod: 8705,
    type: 'binary',
    volume: '29623.019599999999999969'
  }
}
```
All of the methods in the Simplified API are getter methods that use an `eth_call` RPC request; for transactional requests (`eth_sendTransaction`), see the [Transaction API](#transaction-api) section below. This API is simplified in the sense that single requests to this API can be used to fetch a large amount of data, without the need for complicated RPC batch queries.

### augur.trading.orderBook.getOrderBook({ \_type, \_market, \_outcome, minPrice, maxPrice[, \_startingOrderId, \_numOrdersToLoad ]}[, callback])

Retrieves a section of the [Order Book](#order-book) for a specified [Market](#market). The required params are `_market` which is the address of the Market we intend to get the order book for. The `_type` which is either set to `1` for getting the Buy side of the order book and `2` for the sell side. The `_outcome` which is an integer value between 1 and 8 for the [Outcome](#outcome) we want to get [Orders](#order) for. The `minPrice` and `maxPrice` are also required and should be set to the minimum and maximum possible prices for a [Share](#shares). The two optional parameters are `_startingOrderId` which defaults to `0x0`, which indicates "start from the best order", and `_numOrdersToLoad` which defaults to 0, or "load all orders". The `_startingOrderId` field should be the order ID from which to start walking towards the order book's tail, as a hexadecimal string. The `_numOrdersToLoad` is expected to be a whole number. The `getOrderBook` function generally should be used only to fetch a small chunk of orders. To fetch entire order book, typically `getOrderBookChunked` should be used instead.

### augur.trading.orderBook.getOrderBookChunked({ \_type, \_market, \_outcome, minPrice, maxPrice[, \_startingOrderId, \_numOrdersToLoad ]}[, onChunkCallback, onCompleteCallback ])

Very similar to the above `getOrderBook` function, however this function is designed for getting the entire side of an [Outcome's](#outcome) [Order Book](#order-book) for a specific [Market](#market) in chunks. The `getOrderBookChunked` function has a params object with the same keys as `getOrderBook` but they work slightly differently. The `_numOrdersToLoad` key is now the chunk size, which instead of defaulting to 0 or "load all orders", it defaults to the default chunk size: 100. The chunk size is simply the number of orders each request will attempt to bring back at a time. This function also has two callbacks, the `onChunkCallback` and the `onCompleteCallback`. The `onChunkCallback` will be triggered for each chunk of orders received where as the `onCompleteCallback` will only be called when the entire order book's single outcome side has been returned. In our example, we are looking to find all the buy orders for outcome 1 in our market, starting from the order ID `0x3c3958b3cad3fb693a6fdd013a615485ef42d824aaa3bd57734f5ec21567ebdc` in chunks of three orders per return to `onChunkCallback`.

### augur.markets.getMarketInfo({ \_market }[, callback])

Returns information about a [Market](#market) (`_market`) that is stored on-contract. Information returned will include all kinds of basic information about the market as well as information about each market [Outcome](#outcome). This information doesn't include the [Order Book](#order-book) however and you should use `getOrderBook` or `getOrderBookChunked` to get information about [Open Orders](#open-orders) for the specified Market.

### augur.markets.batchGetMarketInfo({ marketIDs }[, callback])

Retrieve a `marketsInfo` object for the [Market](#market) IDs in the array `marketIDs`. The `marketsInfo` object will contain `marketInfo` objects (see above for example) which are indexed by their respective market IDs. Much like the above `getMarketsInfo` the [Order Book](#order-book) will not be included in the market information returned and needs to be retrieved separately.

### augur.markets.getMarketsInfo({ marketIDs }[, callback])

This function returns a `marketsInfo` object containing `marketInfo` objects indexed by their respective market IDs similar to `batchGetMarketInfo`. This function does not return as much information about the markets as `batchGetMarketInfo`.
