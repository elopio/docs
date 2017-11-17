Simplified API
===========
<aside class="notice">The Simplified API section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

<aside class="warning">The Simplified API provides an easy way to query for information on accounts, markets, trading, and reporting within Augur. However, in order to use this API, a connection to an <a href="#augur-node">Augur Node</a> is required. Please read the <a href="#augur-node">Augur Node</a> section before using the Simplified API.</aside>

All of the methods in the Simplified API are getter methods that use an `eth_call` RPC request; for transactional requests (`eth_sendTransaction`), see the [Transaction API](#transaction-api) section below. This API is simplified in the sense that single requests to this API can be used to fetch a large amount of data, without the need for complicated RPC batch queries.

All optional parameters are listed in brackets. Most of the Simplified API functions contain optional parameters for specifying which results to return and how to sort them.  For example, `sortBy` should be a string corresponding to the name of a particular field to sort by, and `isSortDescending` should be a boolean value for whether to sort the results in descending order. `limit` should be an integer greater than 0 that represents the maximum number of results to return. `offset` should be an integer greater than 0. It is used when only a portion of the total query results is desired, and it represents the record number at which to start returning results.

Accounts API
-----------------
```javascript
augur.accounts.getAccountTransferHistory({
  account: "0x0000000000000000000000000000000000000b0b",
  token: "0x7a305d9b681fb164dc5ad628b5992177dc66aec8",
  isSortDescending: false
}, function (error, accountTransferHistory) { /* ... */ })
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
```
### augur.accounts.getAccountTransferHistory({ account[, token, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the token transfers made to or from the required `account` parameter, which is a string containing the account address. The optional string parameter `token` can be used to restrict the results to a particular token name.

Markets API
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
  marketID: "0x0000000000000000000000000000000000000001"
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

// NOTE: This function has not be implemented yet, so the format of the returned data is still pending.
augur.markets.getMarketsAwaitingReporting({
  universe: "0x000000000000000000000000000000000000000b"
}, function (error, marketsAwaitingReporting) { /* ... */ })
// example output: coming soon

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
    reportingState: "DESIGNATED_REPORTING",
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
```
### augur.markets.getCategories({ universe[, sortBy, isSortDescending, limit, offset] }[, callback]) 

Returns a list of all [Market](#market) categories in the required parameter `universe`, which is a string containing the address of a given [Universe](#universe).

### augur.markets.getDisputableMarkets({ reportingWindow[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) in a specific [Reporting Window](#reporting-window) that are able to be disputed, along with the value of the [Dispute Bond](#dispute-bond) needed to dispute each Market's result. The required `reportingWindow` parameter is a string containing the address of a Reporting Window.

### augur.markets.getMarketPriceHistory({ marketID }[, callback])

Returns the prices and timestamps of [Market](#market) [Outcomes](#outcome) over time. The `marketID` parameter can be specified as a string containing a specific Market's address in order to restrict the results to a particular Market.

### augur.markets.getMarkets({ universe[, sortBy, isSortDescending, limit, offset] }[, callback])

Returns an array of all [Markets](#markets) in a given [Universe](#universe). `universe` is a string containing the address of a Universe. 

### augur.markets.getMarketsAwaitingDesignatedReporting({ universe[, designatedReporter, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the [Markets](#market) in a [Universe](#universe) that are waiting for a [Designated Report](#designated-report) to be submitted. `universe` is a string containing the address of a given Universe. `designatedReporter` is an optional string parameter that can be used to restrict the results to [Markets](#market) having a specific [Designated Reporter](#designated-reporter) address.

<!-- TODO: Confirm if this returns Markets awaiting Designated Report & update description accordingly. -->
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

Reporting API
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
      marketID: "0x0000000000000000000000000000000000000011",
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
### augur.reporting.getReportingHistory({ reporter[, universe, marketID, reportingWindow, sortBy, isSortDescending, limit, offset] }[, callback])

Returns information about the [Reports](#report) submitted by a particular user. For [Reporting Windows](#reporting-window) that have ended, this includes the [Final Outcome](#final-outcome) of the [Market](#market), whether the user's [Report](#report) matched that Final Outcome, how much [REP](#rep) the user gained or lost from redistribution, and how much the user earned in [Reporting Fees](#reporting-fee). `reporter` is a string containing the address of a given [Reporter](#reporter). Either `universe`, `marketID`, or `reportingWindow` must be specified as a string paramter containing the address of a [Universe](#universe), Market, or Reporting Window.

<!-- TODO: Verify description once function is completed. (Make sure it matches returned result.) -->
### augur.reporting.getReportingSummary({ reportingWindow }[, callback])

Returns the number of Markets in various Reporting Phases, including "DESIGNATED\_REPORTING", "FIRST\_REPORTING", "LAST\_REPORTING", "AWAITING_FINALIZATION" (when a Market has been reported on and is in a [Dispute Phase](#dispute-phase)), "FORKING" (for the Market that has Forked), "AWAITING\_FORK\_MIGRATION" (for Markets that are waiting for a Forked Market to resolve), and "FINALIZED".

<!-- Old description: Returns information about a given [Reporting Window](#reporting-window), including total number of [Markets](#market) up for reporting, total number of Markets up for [Dispute](#dispute), total number of Markets undergoing and/or resolved via each [Reporting Round](#reporting-round). `reportingWindow` is a string containing the address of a specific Reporting Window. -->

<!-- TODO: Verify description once function is completed. (Make sure it matches returned result.) -->
### augur.reporting.getReportingWindowsWithUnclaimedFees({ universe, account }[, callback])

Returns the [Reporting Windows](#reporting-window) where a user has not claimed his or her [Reporting Fees](#reporting-fee). `universe` and `account` are strings containing the address of a [Universe](#universe) and the address of a user.

### augur.reporting.getStakeTokens({ universe, account[, stakeTokenState] }[, callback]) 

Returns the Stake Tokens owned by a a user that are either unclaimed or are in [Markets](#market) that have not been [Finalized](#finalized-market). `universe` and `account` are strings containing the address of a [Universe](#universe) and the address of a user. `stakeTokenState` is a string containing "ALL", "UNCLAIMED", or "UNFINALIZED".

Trading API
-----------
```javascript
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
  marketID: "0x0000000000000000000000000000000000000001",
  outcome: 0,
  orderType: null,
  sortBy: null,
  isSortDescending: null,
  limit: null,
  offset: null,
}, function (error, userTradingHistory) { /* ... */ })
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
### augur.trading.getOrders({ [universe, marketID, outcome, orderType, creator, orderState, sortBy, isSortDescending, limit, offset] }[, callback])

Returns a list of [Orders](#orders) in a given [Universe](#universe) or [Market](#market). Either `universe` or `marketID` is required as a string parameter containing the address of a Universe or Market. `outcome` is an integer denoting a specific Outcome in the Market, `orderType` is a string (i.e., "buy" or "sell"), `creator` is a string containing a user account address, and `orderState` is a string (i.e., "ALL", "CANCELLED", "CLOSED", or "OPEN").

<!-- TODO: rename `maker` to `creator` in returned result in JS example -->
### augur.trading.getUserTradingHistory({ account[, universe, marketID, outcome, orderType, sortBy, isSortDescending, limit, offset] }[, callback])

Returns information about the trades a particular user has made. `account` is a string parameter containing the address of a user. Either `universe` or `marketID` is required as a string parameter containing the address of a [Universe](#universe) or [Market](#market). `outcome` is an integer denoting a specific Outcome in the Market, and `orderType` is a string (i.e., "buy" or "sell").

### augur.trading.getUserTradingPositions({ account[, universe, marketID, outcome, sortBy, isSortDescending, limit, offset] }[, callback])

Returns the trading positions held by a particular user. `account` is a string parameter containing the address of a user. Either `universe` or `marketID` is required as a string parameter containing the address of a [Universe](#universe) or [Market](#market). `outcome` is an integer denoting a specific Outcome in the Market.