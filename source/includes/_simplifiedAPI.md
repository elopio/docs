Simplified API
===========
<aside class="notice">The Simplified API section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

<aside class="warning">The Simplified API provides an easy way to make transactions and query for information within Augur. This API is simplified in the sense that single requests to this API can be used to fetch a large amount of data, without the need for complicated RPC batch queries. However, many of the functions in this API require a connection to an <a href="#augur-node">Augur Node</a>, which has been indicated in their descriptions. Please read the <a href="#augur-node">Augur Node</a> section before using these functions.</aside>

All optional parameters are listed in brackets. Most of the Simplified API functions contain optional parameters for specifying which results to return and how to sort them.  For example, `sortBy` should be a string corresponding to the name of a particular field to sort by, and `isSortDescending` should be a boolean value for whether to sort the results in descending order. `limit` should be an integer greater than 0 that represents the maximum number of results to return. `offset` should be an integer greater than 0. It is used when only a portion of the total query results is desired, and it represents the record number at which to start returning results.

Accounts Functions
-----------------
```javascript
augur.accounts.getAccountTransferHistory({
  account: "0x0000000000000000000000000000000000000b0b",
  token: null,
  isSortDescending: false
}, function (error, accountTransferHistory) { /* ... */ })
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
```
### augur.accounts.getAccountTransferHistory({ account[, token, earliestCreationTime, latestCreationTime, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the token transfers made to or from the required `account` parameter, which is a string containing the account address. The optional string parameter `token` can be used to restrict the results to a particular token name. `earliestCreationTime` and `latestCreationTime` are integers representing Unix timestamps for when the transfer took place (that is, when the block on the Ethereum blockchain containing the transfer was created).

<!--
Create-Market Functions
----------------
```javascript
var extraInfo = {
  longDescription: "One Market to rule them all, One Market to bind them, One Market to bring them all, and in the darkness bind them.",
  tags: ["Ancient evil", "Large flaming eyes"],
};
augur.createMarket.createBinaryMarket({
  universe: "0x6eabb9367012c0a84473e1e6d7a7ce39a54d77bb",
  _description: "Will this market be the One Market?",
  _endTime: 2345678901,
  _feePerEthInWei: "0x4321",
  _denominationToken: "0x74e88699f5d33f516500c3d9a2430f5e6ffb0689",
  _designatedReporterAddress: "0x01114f4bda09ed6c6715cf0baf606b5bce1dc96a",
  _topic: "TOPIC",
  _extraInfo: extraInfo,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) },
});
```
-->
Markets Functions
----------------
```javascript
augur.markets.getCategories({
  universe: "0x000000000000000000000000000000000000000b",
  sortBy: "popularity",
  isSortDescending: true
}, function (error, categoriesInfo) { /* ... */ })
// example output:
[
  { category: "finance", popularity: 12345 },
  { category: "politics", popularity: 5000 },
  { category: "ethereum", popularity: 1000 },
  { category: "augur", popularity: 500 },
]

// NOTE: This function has not be implemented yet, so the format of the returned data is still pending.
augur.markets.getDisputableMarkets({ 
  reportingWindow: "0x1000000000000000000000000000000000000000"
}, function (error, disputableMarkets) { /* ... */ })
// example output: coming soon

augur.markets.getMarketPriceHistory({
  marketID: "0x0000000000000000000000000000000000000001",
  sortBy: null,
  isSortDescending: null,
  limit: null,
  offset: null,
}, function (error, marketPriceHistory) { /* ... */ })
// example output:
{
  0: [{
    price: 5.5,
    timestamp: 1506474500,
  }],
}

augur.markets.getMarkets({
  universe: "0x000000000000000000000000000000000000000b"
}, function (error, marketsMatched) { /* ... */ })
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
}, function (error, marketsInfo) { /* ... */ })
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
}, function (error, marketsAwaitingReporting) { /* ... */ })
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
}, function (error, marketsClosingInDateRange) { /* ... */ })
// example output:
[
  "0x0000000000000000000000000000000000000001",
]

augur.markets.getMarketsCreatedByUser({
  universe: "0x000000000000000000000000000000000000000b",
  creator: "0x0000000000000000000000000000000000000b0b",
}, function (error, marketsCreatedByUser) { /* ... */ })
// example output:
[
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000011",
]

augur.markets.getMarketsInCategory({
  universe: "0x000000000000000000000000000000000000000b",
  category: "augur",
}, function (error, marketsInCategory) { /* ... */ })
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
}, function (error, marketsInfo) { /* ... */ })
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
    reportingState: "DESIGNATED_REPORTING",
    reportingWindow: "0x1000000000000000000000000000000000000000",
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
      description: "outcome 0",
    }, {
      id: 1,
      volume: 100,
      price: 0.125,
      description: "outcome 1",
    }, {
      id: 2,
      volume: 100,
      price: 0.125,
      description: "outcome 2",
    }, {
      id: 3,
      volume: 100,
      price: 0.125,
      description: "outcome 3",
    }, {
      id: 4,
      volume: 100,
      price: 0.125,
      description: "outcome 4",
    }, {
      id: 5,
      volume: 100,
      price: 0.125,
      description: "outcome 5",
    }, {
      id: 6,
      volume: 100,
      price: 0.125,
      description: "outcome 6",
    }, {
      id: 7,
      volume: 100,
      price: 0.125,
      description: "outcome 7",
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
    reportingState: "DESIGNATED_REPORTING",
    reportingWindow: "0x1000000000000000000000000000000000000000",
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
      description: "outcome 0",
    }, {
      id: 1,
      volume: 1000,
      price: 0.5,
      description: "outcome 1",
    }]
  }
]
```
### augur.markets.getCategories({ universe[, sortBy, isSortDescending, limit, offset] }[, callback]) 

Returns a list of all [Market](#market) categories in the required parameter `universe`, which is a string containing the address of a given [Universe](#universe).

<!-- TODO: Add JS example results -->
### augur.markets.getDisputableMarkets({ reportingWindow[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) in a specific [Reporting Window](#reporting-window) that are able to be disputed, along with the value of the [Dispute Bond](#dispute-bond) needed to dispute each Market's result. The required `reportingWindow` parameter is a string containing the address of a Reporting Window.

### augur.markets.getMarketPriceHistory({ marketID }[, callback])

Returns the prices and timestamps of [Market](#market) [Outcomes](#outcome) over time. The `marketID` parameter can be specified as a string containing a specific Market's address in order to restrict the results to a particular Market.

### augur.markets.getMarkets({ universe[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns an array of all [Markets](#markets) in a given [Universe](#universe). `universe` is a string containing the address of a Universe. 

### augur.markets.getMarketsAwaitingDesignatedReporting({ universe[, designatedReporter, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) in a [Universe](#universe) that are waiting for a [Designated Report](#designated-report) to be submitted. `universe` is a string containing the address of a given Universe. `designatedReporter` is an optional string parameter that can be used to restrict the results to [Markets](#market) having a specific [Designated Reporter](#designated-reporter) address.

### augur.markets.getMarketsAwaitingReporting({ [universe, reportingWindow, reportingState, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) in a particular [Universe](#universe) or [Reporting Window](#reporting-window) that are waiting for a [Designated Report](#designated-report) to be submitted or waiting for the [Reporting Phase](#reporting-phase) to end. Either `universe` or `reportingWindow` must be specified as a string containing an address. `reportingState` is a string where valid values are "DESIGNATED\_REPORTING", "FIRST\_REPORTING", or "LAST\_REPORTING".

### augur.markets.getMarketsClosingInDateRange({ universe, earliestClosingTime, latestClosingTime[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) closing between `earliestClosingTime` and `latestClosingTime` in the specified `universe`. `earliestClosingTime` and `latestClosingTime` are integers representing Unix timestamps, and `universe` is a string containing the address of a given [Universe](#universe).

### augur.markets.getMarketsCreatedByUser({ universe, creator[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) created by a specific user, as well as the total amount of fees earned so far by that user. `universe` and `creator` are strings representing the addresses of the [Universe](#universe) and [Market Creator](#market-creator).

### augur.markets.getMarketsInCategory({ universe, category[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the addresses of the [Markets](#market) in a specific category. `universe` is a string containing the address of a particular [Universe](#universe), and `category` is a string representing the category name.

### augur.markets.getMarketsInfo({ marketIDs }[, callback])

Returns information about [Markets](#market) that are stored on-contract. `marketIDs` is an array of strings containing Market addresses. The returned result includes basic information about the markets as well as information about each market [Outcome](#outcome). It does not include [Order Book](#order-book) information; however the function `augur.trading.getOrders` can be used to get information about [Orders](#orders) for the specified Market.

Reporting Functions
-------------
```javascript
augur.reporting.getReportingHistory({
  reporter: "0x0000000000000000000000000000000000000021",
  universe: "0x000000000000000000000000000000000000000b",
}, function (error, reportingHistory) { /* ... */ })
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
      marketReportingState: "DESIGNATED_REPORTING",
      reportingWindow: "0x1000000000000000000000000000000000000000",
      payoutNumerators: [0, 2],
      amountStaked: 17,
      stakeToken: "0x0000000000000000001000000000000000000001",
      isCategorical: false,
      isScalar: false,
      isIndeterminate: false,
      isSubmitted: true,
    }],
    "0x0000000000000000000000000000000000000019": [{
      transactionHash: "0x0000000000000000000000000000000000000000000000000000000000000D03",
      logIndex: 0,
      creationBlockNumber: 1400052,
      blockHash: "0x1400052",
      creationTime: 1506474515,
      marketID: "0x0000000000000000000000000000000000000019",
      marketReportingState: "FIRST_REPORTING",
      reportingWindow: "0x1000000000000000000000000000000000000000",
      payoutNumerators: [1, 1],
      amountStaked: 229,
      stakeToken: "0x0000000000000000001000000000000000000003",
      isCategorical: false,
      isScalar: false,
      isIndeterminate: false,
      isSubmitted: true,
    }],
  }
}

augur.reporting.getReportingSummary({ 
  reportingWindow: "0x1000000000000000000000000000000000000000"
}, function (error, allStakeTokens) { /* ... */ })
// example output: 
{
  "AWAITING_FINALIZATION": 1,
  "DESIGNATED_REPORTING": 8,
  "FIRST_REPORTING": 2,
  "FINALIZED": 1,
}

// NOTE: This function has not be implemented yet, so the format of the returned data is still pending.
augur.reporting.getReportingWindowsWithUnclaimedFees({ 
  universe: "0x000000000000000000000000000000000000000b",
  account: "0x0000000000000000000000000000000000000021"
}, function (error, reportingWindowsWithUnclaimedFees) { /* ... */ })
// example output: coming soon

augur.reporting.getStakeTokens({ 
  universe: "0x000000000000000000000000000000000000000b",
  account: "0x0000000000000000000000000000000000000021",
  stakeTokenState: "UNFINALIZED",
}, function (error, allStakeTokens) { /* ... */ })
// example output: 
{
  "0x0000000000000000001000000000000000000001": {
    stakeToken: "0x0000000000000000001000000000000000000001",
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
    winningToken: null,
    claimed: false,
    reportingState: "FIRST_REPORTING",
  },
}
```
### augur.reporting.getReportingHistory({ reporter[, universe, marketID, reportingWindow, earliestCreationTime, latestCreationTime, sortBy, isSortDescending, limit, offset] }[, callback])

Returns information about the [Reports](#report) submitted by a particular user. For [Reporting Windows](#reporting-window) that have ended, this includes the [Final Outcome](#final-outcome) of the [Market](#market), whether the user's [Report](#report) matched that Final Outcome, how much [REP](#rep) the user gained or lost from redistribution, and how much the user earned in [Reporting Fees](#reporting-fee). `reporter` is a string containing the address of a given [Reporter](#reporter). Either `universe`, `marketID`, or `reportingWindow` must be specified as a string paramter containing the address of a [Universe](#universe), Market, or Reporting Window. `earliestCreationTime` and `latestCreationTime` are integers representing Unix timestamps for when the user submitted the report (that is, when the block on the Ethereum blockchain containing the report was created).

### augur.reporting.getReportingSummary({ reportingWindow }[, callback])

Returns the number of Markets in various Reporting Phases, including "DESIGNATED\_REPORTING", "FIRST\_REPORTING", "LAST\_REPORTING", "AWAITING_FINALIZATION" (when a Market has been reported on and is in a [Dispute Phase](#dispute-phase)), "FORKING" (for the Market that has Forked), "AWAITING\_FORK\_MIGRATION" (for Markets that are waiting for a Forked Market to resolve), and "FINALIZED".

<!-- TODO: Verify description once function is completed. (Make sure it matches returned result.) Add JS example results -->
### augur.reporting.getReportingWindowsWithUnclaimedFees({ universe, account }[, callback])

Returns the [Reporting Windows](#reporting-window) where a user has not claimed his or her [Reporting Fees](#reporting-fee). `universe` and `account` are strings containing the address of a [Universe](#universe) and the address of a user.

### augur.reporting.getStakeTokens({ universe, account[, stakeTokenState] }[, callback]) 

Returns the Stake Tokens owned by a a user that are either unclaimed or are in [Markets](#market) that have not been [Finalized](#finalized-market). `universe` and `account` are strings containing the address of a [Universe](#universe) and the address of a user. `stakeTokenState` is a string containing "ALL", "UNCLAIMED", or "UNFINALIZED".

Trading Functions
-----------
```javascript
augur.trading.getBetterWorseOrders({
  marketID: "0x0000000000000000000000000000000000000001",
  outcome: 0,
  orderType: "buy",
  price: 0.65,
}, function (error, betterWorseOrders) { /* ... */ })
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
}, function (error, orders) { /* ... */ })
// example output:
{
  "0x0000000000000000000000000000000000000001": {
    0: {
      buy: {
        "0x1000000000000000000000000000000000000000000000000000000000000000": {
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
}, function (error, userTradingHistory) { /* ... */ })
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
}, function (error, userTradingPositions) { /* ... */ })
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
<!-- TODO: Update JS returned data; update description for case where no order IDs are found (returns 0?)  -->
### augur.trading.getBetterWorseOrders({ marketID, outcome, orderType, price }[, callback])

Returns the IDs of the [Orders](#order) for [Outcome](#outcome) `outcome` that have a better and worse price than one specified by `price`. If no better/worse orders, null will be returned. This function should be called prior to calling `augur.api.CreateOrder.publicCreateOrder` in order to get the `_betterOrderId` and `_worseOrderId` parameters that it accepts. (`_betterOrderId` and `_worseOrderId` are used to optimize the sorting of Orders on the [Order Book](#order-book).) `marketID` is a string parameter containing the address of the Market. `outcome` is an integer denoting a specific [Outcome](#outcome) in the Market. `orderType` is a string with valid values of "buy" or "sell". `price` is a number containing the desired price. 

### augur.trading.getOrders({ [universe, marketID, outcome, orderType, creator, orderState, earliestCreationTime, latestCreationTime, sortBy, isSortDescending, limit, offset] }[, callback])

Returns a list of [Orders](#orders) in a given [Universe](#universe) or [Market](#market). Either `universe` or `marketID` is required as a string parameter containing the address of a Universe or Market. `outcome` is an integer denoting a specific [Outcome](#outcome) in the Market, `orderType` is a string (i.e., "buy" or "sell"), `creator` is a string containing a user account address, and `orderState` is a string (i.e., "ALL", "CANCELLED", "CLOSED", or "OPEN"). `earliestCreationTime` and `latestCreationTime` are integers representing Unix timestamps for when the order state got changed (that is, when the block on the Ethereum blockchain containing the changing of the order's state was created).

<!-- TODO: rename `maker` to `creator` in returned result in JS example -->
### augur.trading.getUserTradingHistory({ account[, universe, marketID, outcome, orderType, earliestCreationTime, latestCreationTime, sortBy, isSortDescending, limit, offset] }[, callback])

Returns information about the trades a particular user has made. `account` is a string parameter containing the address of a user. Either `universe` or `marketID` is required as a string parameter containing the address of a [Universe](#universe) or [Market](#market). `outcome` is an integer denoting a specific [Outcome](#outcome) in the Market, and `orderType` is a string (i.e., "buy" or "sell"). `earliestCreationTime` and `latestCreationTime` are integers representing Unix timestamps for when the trade was made (that is, when the block on the Ethereum blockchain containing the trade was created).

### augur.trading.getUserTradingPositions({ account[, universe, marketID, outcome, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the trading positions held by a particular user. `account` is a string parameter containing the address of a user. Either `universe` or `marketID` is required as a string parameter containing the address of a [Universe](#universe) or [Market](#market). `outcome` is an integer denoting a specific [Outcome](#outcome) in the Market.