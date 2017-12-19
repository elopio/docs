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
```
### augur.accounts.getAccountTransferHistory(p, callback) &rarr; {Array.&lt;<a href="#AccountTransfer">AccountTransfer</a>>}

Returns the token transfers made to or from a specific Ethereum address. 

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`account`**  (string) Ethereum address of the account for which to get transfer history, as a hexadecimal string.
    * **`token`**  (string) &lt;optional> Contract address of the token contract by which to limit the history results, as a hexadecimal string.
    * **`earliestCreationTime`**  (number) &lt;optional> Earliest timestamp, in seconds, at which to truncate history results. (This timestamp is when the block on the Ethereum blockchain containing the transfer was created.)
    * **`latestCreationTime`**  (number) &lt;optional> Latest timestamp, in seconds, at which to truncate history results. (This timestamp is when the block on the Ethereum blockchain containing the transfer was created.)
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort transfer history.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort transfers in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of transfers to return.
    * **`offset`**  (string) &lt;optional> Number of transfers to truncate from the beginning of the history results.
* **`callback`** (function) Called after the account transfer history has been retrieved.

#### **Returns:**

* Array representing the account's transfer history. (Array.&lt;<a href="#AccountTransfer">AccountTransfer</a>>)

Create-Market Functions
----------------
```javascript
// augur.createMarket.createBinaryMarket: Example JS code coming soon

// augur.createMarket.createCategoricalMarket: Example JS code coming soon

// augur.createMarket.createScalarMarket: Example JS code coming soon

// augur.createMarket.getMarketCreationCost: Example JS code coming soon

// augur.createMarket.getMarketCreationCostBreakdown: Example JS code coming soon
```
### augur.createMarket.createBinaryMarket(p)

Creates a [Binary Market](#binary-market) in a specified [Universe](#universe).

#### **Parameters:**

* **`p`** (Object) Parameters object. 
* **Properties:**
    * **`universe`**  (string) Universe in which to create a Binary Market.
    * **`_endTime`**  (number) Binary Market expiration timestamp, in seconds.
    * **`_feePerEthInWei`**  (string) &lt;optional> Amount of wei per ether settled that goes to the [Market Creator](#market-creator), as a base-10 string.
    * **`_denominationToken`**  (string) Ethereum address of the token used as this Market's currency.
    * **`_designatedReporterAddress`**  (string) Ethereum address of this Market's [Designated Reporter](#designated-reporter).
    * **`_topic`**  (string) The topic (category) to which this Market belongs, as a UTF8 string.
    * **`_description`**  (string) Description of the Market, as a UTF8 string.
    * **`_extraInfo`**  ([ExtraInfo](#ExtraInfo)) &lt;optional> Extra info which will be converted to JSON and logged to the chain in the [MarketCreated event](#MarketCreated).
    * **`meta`**  ([AuthenticationMetadata](#AuthenticationMetadata)) &lt;optional> Authentication metadata for raw transactions.
    * **`onSent`**  (function) Called if/when the createBinaryMarket transaction is broadcast to the network.
    * **`onSuccess`**  (function) Called if/when the createBinaryMarket transaction is sealed and confirmed.
    * **`onFailed`**  (function) Called if/when the createBinaryMarket transaction fails.

#### **Returns:**

Description pending.

### augur.createMarket.createCategoricalMarket(p)

Creates a [Categorical Market](#categorical-market) in a specified [Universe](#universe).

#### **Parameters:**

* **`p`** (Object) Parameters object. 
* **Properties:**
    * **`universe`**  (string) Universe in which to create a Categorical Market.
    * **`_endTime`**  (number) Categorical Market expiration timestamp, in seconds.
    * **`_feePerEthInWei`**  (string) &lt;optional> Amount of wei per ether settled that goes to the [Market Creator](#market-creator), as a base-10 string.
    * **`_denominationToken`**  (string) Ethereum address of the token used as this Market's currency.
    * **`_designatedReporterAddress`**  (string) Ethereum address of this Market's [Designated Reporter](#designated-reporter).
    * **`_numOutcomes`**  (number) Number of [Outcomes](#outcomes) this Market has, as an integer on [2, 8].
    * **`_topic`**  (string) The topic (category) to which this Market belongs, as a UTF8 string.
    * **`_description`**  (string) Description of the Market, as a UTF8 string.
    * **`_extraInfo`**  ([ExtraInfo](#ExtraInfo)) &lt;optional> Extra info which will be converted to JSON and logged to the chain in the [MarketCreated event](#MarketCreated).
    * **`meta`**  ([AuthenticationMetadata](#AuthenticationMetadata)) &lt;optional> Authentication metadata for raw transactions.
    * **`onSent`**  (function) Called if/when the createCategoricalMarket transaction is broadcast to the network.
    * **`onSuccess`**  (function) Called if/when the createCategoricalMarket transaction is sealed and confirmed.
    * **`onFailed`**  (function) Called if/when the createCategoricalMarket transaction fails.

#### **Returns:**

Description pending.

### augur.createMarket.createScalarMarket(p)

Creates a [Scalar Market](#scalar-market) in a specified [Universe](#universe).

#### **Parameters:**

* **`p`** (Object) Parameters object. 
* **Properties:**
    * **`universe`**  (string) Universe in which to create this market.
    * **`_endTime`**  (number) Market expiration timestamp, in seconds.
    * **`_feePerEthInWei`**  (string) &lt;optional> Amount of wei per ether settled that goes to the [Market Creator](#market-creator), as a base-10 string.
    * **`_denominationToken`**  (string) Ethereum address of the token used as this Market's currency.
    * **`_minPrice`**  (string) Minimum display (non-normalized) price for this Market, as a base-10 string.
    * **`_maxPrice`**  (string) Maximum display (non-normalized) price for this Market, as a base-10 string.
    * **`_designatedReporterAddress`**  (string) Ethereum address of this Market's [Designated Reporter](#designated-reporter).
    * **`_topic`**  (string) The topic (category) to which this Market belongs, as a UTF8 string.
    * **`_description`**  (string) Description of the Market, as a UTF8 string.
    * **`tickSize`**  (string) &lt;optional> The [Tick](#tick) size for this Market, as a base-10 string.
    * **`_extraInfo`**  (ExtraInfo) &lt;optional> Extra info which will be converted to JSON and logged to the chain in the [MarketCreated event](#MarketCreated).
    * **`meta`**  ([AuthenticationMetadata](#AuthenticationMetadata)) &lt;optional> Authentication metadata for raw transactions.
    * **`onSent`**  (function) Called if/when the createScalarMarket transaction is broadcast to the network.
    * **`onSuccess`**  (function) Called if/when the createScalarMarket transaction is sealed and confirmed.
    * **`onFailed`**  (function) Called if/when the createScalarMarket transaction fails.

#### **Returns:**

Description pending.

### augur.createMarket.getMarketCreationCost(p, callback) &rarr; {<a href="#MarketCreationCost">MarketCreationCost</a>}

Retrieves the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) amount and total ether required to create a new [Market](#market).

Note: This function will send a transaction if needed to create the current [Reporting Window](#reporting-window).

#### **Parameters:**

* **`p`** (Object) Parameters object. 
    * **`universe`**  (string) [Universe](#universe) on which to create this Market.
* **`callback`** (function) Called after the Market creation cost has been looked up.

#### **Returns:**

* Costs of creating a new Market. (<a href="#MarketCreationCost">MarketCreationCost</a>)

<!-- TODO: Add link to augur.createMarket.getMarketCreationCost -->
### augur.createMarket.getMarketCreationCostBreakdown(p, callback) &rarr; {<a href="#MarketCreationCostBreakdown">MarketCreationCostBreakdown</a>}

Similar to `augur.createMarket.getMarketCreationCost`, but provides more detail about the ether costs required to create a new [Market](#market). These ether costs are broken down by the gas cost paid to the [First Reporter](#first-reporter) and the cost of the [Validity Bond](#validity-bond).

Note: This function will send a transaction if needed to create the current Reporting Window.

#### **Parameters:**

* **`p`** (Object) Parameters object. 
* **Properties:**
    * **`universe`**  (string) [Universe](#universe) on which to create this Market.
* **`callback`** (function) Called when all Market creation costs have been looked up.

#### **Returns:**

* Cost breakdown for creating a new Market. (<a href="#MarketCreationCostBreakdown">MarketCreationCostBreakdown</a>)

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
  reportingWindow: "0x1000000000000000000000000000000000000000"
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
  creator: "0x0000000000000000000000000000000000000b0b",
}, function (error, result) { 
  console.log(result);
});
// example output:
[
  "0x0000000000000000000000000000000000000001",
  "0x0000000000000000000000000000000000000002",
  "0x0000000000000000000000000000000000000011",
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
      name: "outcome 0",
    }, {
      id: 1,
      volume: 1000,
      price: 0.5,
      name: "outcome 1",
    }]
  }
]
```
### augur.markets.getCategories(p, callback) &rarr; {Array.&lt;<a href="#Category">Category</a>>}

Returns the Market Categories in a specific [Universe](#universe).

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**
* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`universe`**  (string) Contract address of the Universe from which to retrieve the categories, as a hexadecimal string.
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the categories.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort categories in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of categories to return.
    * **`offset`**  (string) &lt;optional> Number of categories to truncate from the beginning of the results.
* **`callback`** (function) Called after the categories have been retrieved.

#### **Returns:**
* Array representing the categories in the specified Universe. (Array.&lt;<a href="#Category">Category</a>>)

<!-- TODO: Add JS example results -->
### augur.markets.getDisputableMarkets(p, callback)

This function has not been implemented yet. Returns the [Markets](#markets) in a specific [Reporting Window](#reporting-window) that are able to be disputed, along with the value of the [Dispute Bond](#dispute-bond) needed to dispute each Market’s result.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`reportingWindow`**  (string) Contract address of the Reporting Window from which to retrieve the disputable Markets.
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort Markets in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* Pending.

### augur.markets.getMarketPriceHistory(p, callback) &rarr; {<a href="#MarketPriceTimeSeries">MarketPriceTimeSeries</a>}

Returns the prices and timestamps of a specific [Market's](#market) [Outcomes](#outcomes) over time.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`marketID`**  (string) Market contract address for which to look up orders, as a hexadecimal string.
* **`callback`** (function) Called after the price time-series has been received and parsed.

#### **Returns:**

* The Market's price time-series, keyed by outcome ID. (<a href="#MarketPriceTimeSeries">MarketPriceTimeSeries</a>)

### augur.markets.getMarkets(p, callback) &rarr; {Array.&lt;string>}

Returns an array of [Markets](#market) in a specific [Universe](#universe).

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`universe`**  (string) Contract address of the Universe from which to get transfer history.
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* Array of Market addresses in the Universe, as hexadecimal strings. (Array.&lt;string>)

### augur.markets.getMarketsAwaitingDesignatedReporting(p, callback) &rarr; {Array.&lt;string>}

Returns the [Markets](#market) in a specific [Universe](#universe) that are waiting for a [Designated Report](#designated-report) to be submitted.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`universe`**  (string) Contract address of the Universe from which to retrieve Markets, as a hexadecimal string.
    * **`designatedReporter`**  (string) &lt;optional> Address of a specific [Designated Reporter](#designated-reporter) by which to filter the results, as a hexadecimal string.
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* Array of Market contract addresses awaiting a Designated Report, as hexadecimal strings. (Array.&lt;string>)

### augur.markets.getMarketsAwaitingReporting(p, callback) &rarr; {Array.&lt;string>}

Returns the [Markets](#market) in a particular [Universe](#universe) or [Reporting Window](#reporting-window) that are waiting for a [Designated Report](designated-report) to be submitted or waiting for the [Reporting Phase](#reporting-phase) to end. Either the Universe or Reporting Window must be specified.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`universe`**  (string) &lt;optional> Contract address of the Universe from which to retrieve Markets, as a hexadecimal string. If this parameter is not specified, a Reporting Window must be specified instead.
    * **`reportingWindow`**  (string) &lt;optional> Contract address of the Reporting Window from which to retrieve the markets, as a hexadecimal string. If this parameter is not specified, a Universe must be specified instead.
    * **`reportingState`**  (string) &lt;optional> Description pending.
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* Array of market addresses awaiting a Designated Report, as hexadecimal strings. (Array.&lt;string>)

### augur.markets.getMarketsClosingInDateRange(p, callback) &rarr; {Array.&lt;string>}

Returns the [Markets](#market) closing between a given time range in a specific [Universe](#universe).

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`universe`**  (string) Contract address of the Universe from which to get the Markets, as a hexadecimal string.
    * **`earliestClosingTime`**  (number) Earliest Market close timestamp at which to truncate Market results, in seconds.
    * **`latestClosingTime`**  (number) Latest Market close timestamp at which to truncate Market results, in seconds.
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* Array of closing Market addresses, as hexadecimal strings. (Array.&lt;string>)

### augur.markets.getMarketsCreatedByAccount(p, callback) &rarr; {Array.&lt;string>}

Returns the [Markets](#market) created by a specific user, as well as the total amount of fees earned so far by that user.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`universe`**  (string) Contract address of the [Universe](#universe) from which to get the Markets, as a hexadecimal string.
    * **`creator`**  (string) Ethereum address of the [Market Creator](#market-creator), as a hexadecimal string.
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* Array of Market addresses created by the specified user, as hexadecimal strings. (Array.&lt;string>)

### augur.markets.getMarketsInCategory(p, callback) &rarr; {Array.&lt;string>}

Returns the [Markets](#market) within a specific category.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`universe`**  (string) Contract address of the [Universe](#universe) from which to get the Markets, as a hexadecimal string.
    * **`category`**  (string) Name of the category from which to get the Markets.
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the Markets.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort the Markets in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of Markets to return.
    * **`offset`**  (string) &lt;optional> Number of Markets to truncate from the beginning of the results.
* **`callback`** (function) Called after the Markets have been retrieved.

#### **Returns:**

* Array of Market addresses in the specified category, as hexadecimal strings. (Array.&lt;string>)

<!-- TODO: Add link to augur.trading.getOrders -->
### augur.markets.getMarketsInfo(p) &rarr; {Array.&lt;<a href="#MarketInfo">MarketInfo</a>>}

Returns information about [Markets](#markets) that are stored on-contract. The returned result includes basic information about the Markets as well as information about each Market [Outcome](#outcome). It does not include Order Book information; however the function `augur.trading.getOrders` can be used to get information about [Orders](#order) for the specified Market.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`marketIDs`**  (Array.&lt;string>) Contract addresses of the Markets for which to get details, as hexadecimal strings.

#### **Returns:**

 (Array.&lt;<a href="#MarketInfo">MarketInfo</a>>)

Reporting Functions
-------------
```javascript
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

// NOTE: This function has not be implemented yet, so the format of the returned data is still pending.
augur.reporting.getReportingWindowsWithUnclaimedFees({ 
  universe: "0x000000000000000000000000000000000000000b",
  account: "0x0000000000000000000000000000000000000021"
}, function (error, result) { 
  console.log(result);
});
// example output: coming soon

augur.reporting.getStakeTokens({ 
  universe: "0x000000000000000000000000000000000000000b",
  account: "0x0000000000000000000000000000000000000021",
  stakeTokenState: "UNFINALIZED",
}, function (error, result) { 
  console.log(result);
});
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
<!-- Add glossary links to section-->
### augur.reporting.getReportingHistory(p, callback) &rarr; {Object}

Returns information about the reports submitted by a particular user. For [Reporting Windows](#reporting-window) that have ended, this includes the [Final Outcome](#final-outcome) of the [Market](#market), whether the user’s Report matched that Final Outcome, how much REP the user gained or lost from redistribution, and how much the user earned in reporting fees.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`reporter`**  (string) Ethereum address of the Reporter for which to retrieve reporting history, as a hexadecimal string.
    * **`universe`**  (string) &lt;optional> Contract address of the [Universe](#universe) in which to look up the reporting history, as a hexadecimal string. Either this parameter, the Market ID, or the Reporting Window must be specified.
    * **`marketID`**  (string) &lt;optional> Contract address of the Market in which to look up the reporting history, as a hexadecimal string. Either this parameter, the Universe, or the Reporting Window must be specified.
    * **`reportingWindow`**  (string) &lt;optional> Contract address of the Reporting Window in which to look up the reporting history, as a hexadecimal string. Either this parameter, the Universe, or the Market ID must be specified.
    * **`earliestCreationTime`**  (number) &lt;optional> Earliest timestamp, in seconds, at which to truncate history results. (This timestamp is when the block on the Ethereum blockchain containing the report submission was created.)
    * **`latestCreationTime`**  (number) &lt;optional> Latest timestamp, in seconds, at which to truncate history results. (This timestamp is when the block on the Ethereum blockchain containing the report submission was created.)
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the reporting history.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort the reporting history in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of reporting history reports to return.
    * **`offset`**  (string) &lt;optional> Number of reporting history reports to truncate from the beginning of the results.
* **`callback`** (function) Called when reporting history has been received and parsed.

#### **Returns:**

* Reporting history, keyed by Universe or Market ID. Each report is of type <a href="#Report">Report</a>. (Object)

### augur.reporting.getReportingSummary(p, callback) &rarr; {Object}

Returns the number of Markets in various reporting phases, including “DESIGNATED_REPORTING”, “FIRST_REPORTING”, “LAST_REPORTING”, “AWAITING_FINALIZATION” (when a Market has been reported on and is in a dispute phase), “FORKING” (for the Market that has forked), “AWAITING_FORK_MIGRATION” (for Markets that are waiting for a forked Market to resolve), and “FINALIZED”.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`reportingWindow`**  (string) Contract address of the Reporting Window for which to get the summary, as a hexadecimal string.
* **`callback`** (function) Called after the reporting summary has been retrieved.

#### **Returns:**

* Summary of the number of Markets in each reporting phase, keyed by reporting phase. (Object)

<!-- TODO: Verify description once function is completed. (Make sure it matches returned result.) Add JS example results -->
### augur.reporting.getReportingWindowsWithUnclaimedFees(p, callback)

This function has not been implemented yet. Returns the Reporting Windows where a specific user has unclaimed reporting fees.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`universe`**  (string) Contract address of the Universe in which the Reporting Windows exist, as a hexadecimal string.
    * **`account`**  (string) Ethereum address of the user who has unclaimed reporting fees, as a hexadecimal string.
* **`callback`** (function) Called after the Reporting Windows have been retrieved.

#### **Returns:**

* Pending.

### augur.reporting.getStakeTokens(p, callback) &rarr; {Array.&lt;<a href="#StakeToken">StakeToken</a>>}

Returns the stake tokens owned by a specific user that are either unclaimed or are in Markets that have not been finalized.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`universe`**  (string) Contract address of the Universe in which to retrieve the stake tokens, as a hexadecimal string.
    * **`account`**  (string) Contract address of the account for which to retrieve the stake tokens, as a hexadecimal string.
    * **`stakeTokenState`**  (<a href="#STAKE_TOKEN_STATE">STAKE_TOKEN_STATE</a>) &lt;optional> Token state by which to filter results.
* **`callback`** (function) Called when reporting history has been received and parsed.

#### **Returns:**

* Stake token details, keyed by stake token ID. (Array.&lt;<a href="#StakeToken">StakeToken</a>>)

Trading Functions
-----------
```javascript
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

// augur.trading.getPositionInMarket: Example JS code coming soon

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
```
<!-- Add glossary links to section -->
<!-- TODO: Update JS returned data; update description for case where no order IDs are found (returns 0?)  -->
### augur.trading.getBetterWorseOrders(p, callback) &rarr; {<a href="#BetterWorseOrders">BetterWorseOrders</a>}

Returns the IDs of the orders for a given Outcome that have a better and worse price than the specified price. If no better/worse orders exist, null will be returned. This function should be called prior to calling augur.api.CreateOrder.publicCreateOrder in order to get the _betterOrderId and _worseOrderId parameters that it accepts. (_betterOrderId and _worseOrderId are used to optimize the sorting of Orders on the Order Book.)

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`marketID`**  (string) Contract address of the Market for which to retrieve the better/worse orders, as a hexadecimal string.
    * **`outcome`**  (string) Market Outcome for which to find better/worse orders.
    * **`orderType`**  (string) Desired type of order. Valid values are "buy" and "sell".
    * **`price`**  (number) Price point at which to find better/worse orders.
* **`callback`** (function) Called when better/worse orders have been retrieved.

#### **Returns:**

* Object containing the better/worse order IDs, as hexidecimal strings. (<a href="#BetterWorseOrders">BetterWorseOrders</a>)

### augur.trading.getOrders(p, callback) &rarr; {<a href="#SingleOutcomeOrderBookSide">SingleOutcomeOrderBookSide</a>}

Returns a list of orders in a given Universe or Market.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`universe`**  (string) &lt;optional> Contract address of the Universe from which to retrieve orders, as a hexadecimal string. Either this parameter or the marketID must be specified.
    * **`marketID`**  (string) &lt;optional> Contract address of the Market from which to retrieve orders, as a hexadecimal string. Either this parameter or the Universe must be specified.
    * **`outcome`**  (number) &lt;optional> Market Outcome to filter results by. Valid values are in the range [0,7].
    * **`creator`**  (string) &lt;optional> Ethereum address of the order creator, as a hexadecimal string.
    * **`orderState`**  (<a href="#ORDER_STATE">ORDER_STATE</a>) &lt;optional> State of orders by which to filter results. Valid values are "ALL", "CANCELLED", "CLOSED", & "OPEN".
    * **`earliestCreationTime`**  (number) &lt;optional> Earliest timestamp, in seconds, at which to truncate order results. (This timestamp is when the block on the Ethereum blockchain containing the transfer was created.)
    * **`latestCreationTime`**  (number) &lt;optional> Latest timestamp, in seconds, at which to truncate order results. (This timestamp is when the block on the Ethereum blockchain containing the transfer was created.)
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the orders.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort orders in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of orders to return.
    * **`offset`**  (string) &lt;optional> Number of orders to truncate from the beginning of the results.
* **`callback`** (function) Called when the requested orders for this Market/Universe have been received and parsed.

#### **Returns:**

* One side of the order book (buy or sell) for the specified Market/Universe and Outcome. (<a href="#SingleOutcomeOrderBookSide">SingleOutcomeOrderBookSide</a>)

### augur.trading.getPositionInMarket(p) &rarr; {Array.&lt;string>}

Gets the number of Shares held by a specific Ethereum address for each Outcome of a Market.

#### **Parameters:**

* **`p`** (Object) Parameters object. 
* **Properties:**
    * **`address`**  (string) Address for which to look up share balances.
    * **`market`**  (string) Market for which to look up share balances.
    * **`tickSize`**  (string) Tick size (interval) for this Market.

#### **Returns:**

* Number of shares for each Outcome of this Market. (Array.&lt;string>)

<!-- TODO: rename `maker` to `creator` in returned result in JS example -->
### augur.trading.getUserTradingHistory(p, callback) &rarr; {Array.&lt;<a href="#UserTrade">UserTrade</a>>}

Returns information about the trades a specific user has made.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`account`**  (string) Ethereum address of the user for which to retrieve trading history, as a hexadecimal string.
    * **`universe`**  (string) &lt;optional> Contract address of the Universe in which to look up the trading history, as a hexadecimal string. Either this parameter or the Market ID must be specified.
    * **`marketID`**  (string) &lt;optional> Contract address of the Market in which to look up the trading history, as a hexadecimal string. Either this parameter or the Universe must be specified.
    * **`outcome`**  (string) Outcome of the share being bought/sold.
    * **`orderType`**  (string) Type of trade. Valid values are "buy" and "sell".
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the trading history.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort the trading history in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of trading history reports to return.
    * **`offset`**  (string) &lt;optional> Number of trading history reports to truncate from the beginning of the results.
* **`callback`** (function) Called when trading history has been received and parsed.

#### **Returns:**

* Array of the user's trades, keyed by Universe/Market ID. (Array.&lt;<a href="#UserTrade">UserTrade</a>>)

### augur.trading.getUserTradingPositions(p, callback) &rarr; {Array.&lt;<a href="#UserTradePosition">UserTradePosition</a>>}

Returns the trading positions held by a specific user.

Note: This function requires an [Augur Node connection](#connect-to-an-augur-node).

#### **Parameters:**

* **`p`** (Object) Parameters object.  
  **Properties:**
    * **`account`**  (string) Ethereum address of the user for which to retrieve trading positions, as a hexadecimal string.
    * **`universe`**  (string) &lt;optional> Contract address of the Universe in which to look up the trading positions, as a hexadecimal string. Either this parameter or the Market ID must be specified.
    * **`marketID`**  (string) &lt;optional> Contract address of the Market in which to look up the trading positions, as a hexadecimal string. Either this parameter or the Universe must be specified.
    * **`outcome`**  (string) Outcome of the share held for the Market.
    * **`sortBy`**  (string) &lt;optional> Field name by which to sort the trading positions.
    * **`isSortDescending`**  (boolean) &lt;optional> Whether to sort the trading positions in descending order by sortBy field.
    * **`limit`**  (string) &lt;optional> Maximum number of trading positions reports to return.
    * **`offset`**  (string) &lt;optional> Number of trading positions reports to truncate from the beginning of the results.
* **`callback`** (function) Called when the trading positions have been received and parsed.

#### **Returns:**

* Array of the user's trading positions. (Array.&lt;<a href="#UserTradePosition">UserTradePosition</a>>)

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

<a name="AuthenticationMetadata"></a>
### AuthenticationMetadata  (Object)

Authentication metadata for raw transactions.

#### **Properties:** 
* **`signer`** (buffer|function) Description pending.
* **`accountType`** (string) Description pending.

<a name="BetterWorseOrders"></a>
### BetterWorseOrders  (Object)

#### **Properties:** 
* **`betterOrderID`** (string|null) ID of the order with the next best price over the specified order ID, as a hexadecimal string.
* **`worseOrderID`** (string|null) ID of the order with the next worse price over the specified order ID, as a hexadecimal string.

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
* **`augurNode`** (string) Websocket address of an [Augur Node](#augur-node).

<a name="EthereumNode"></a>
### ExtraInfo  (Object)

#### **Properties:** 
* **`http`** (string|null) HTTP address of an Ethereum node.
* **`httpAddresses`** (Array.<string>|null) Array of HTTP Ethereum node addresses. (Can be used instead of `http` to specify a list of HTTP addresses to iterate through until a connection is established.)
* **`ws`** (string|null) Websocket address of an Ethereum node.
* **`wsAddresses`** (Array.<string>|null) Array of websocket Ethereum node addresses. (Can be used instead of `ws` to specify a list of websocket addresses to iterate through until a connection is established.)
* **`ipc`** (string|null) IPC address of an Ethereum node.
* **`ipcAddresses`** (Array.<string>|null) Array of IPC Ethereum node addresses. (Can be used instead of `ipc` to specify a list of IPC addresses to iterate through until a connection is established.)
* **`networkID`** (string) Description pending.

<a name="ExtraInfo"></a>
### ExtraInfo  (Object)

#### **Properties:** 
* **`resolutionSource`** (string|null) Source that should be referenced when determining the Outcome of a Market.
* **`tags`** (Array.<string>|null) Keywords used to tag the Market (maximum of 2).
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
* **`creationFee`** (string) Description pending.
* **`reportingFeeRate`** (number) Percentage rate of ETH sent to the Reporting Window containing the Market whenever shares are settled. Reporting fees are later used to pay REP holders for Reporting on the Outcome of Markets.
* **`marketCreatorFeeRate`** (number) Percentage rate of ETH paid to the Market creator whenever shares are settled.
* **`marketCreatorFeesCollected`** (number|null) Amount of fees the Market creator collected from the Market, in ETH.
* **`category`** (string) Name of the category the Market is in.
* **`tags`** (Array.&lt;(string|null)>) Names with which the Market has been tagged.
* **`volume`** (number) Trading volume for this Outcome. (Method for calculating this is pending.)
* **`outstandingShares`** (number) Total shares in the Market. (Method for calculating this is pending.)
* **`reportingState`** (<a href="#REPORTING_STATE">REPORTING_STATE</a>|null) Reporting state name.
* **`reportingWindow`** (string) Contract address of the Reporting Window the Market is in, as a hexadecimal string.
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

<a name="MarketPriceTimeSeries"></a>
### MarketPriceTimeSeries  (Object)

#### **Properties:** 
* **`Price`** (<a href="#SingleOutcomePriceTimeSeries">SingleOutcomePriceTimeSeries</a>) time-series for a single Outcome, keyed by Outcome ID.

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

<a name="Report"></a>
### Report  (Object)

#### **Properties:** 
* **`transactionHash`** (string) Hash to look up the reporting transaction receipt.
* **`logIndex`** (number) Number of the log index position in the Ethereum block containing the reporting transaction.
* **`creationBlockNumber`** (number) Number of the Ethereum block containing the reporting transaction.
* **`blockHash`** (string) Hash of the Ethereum block containing the reporting transaction.
* **`creationTime`** (number) Timestamp, in seconds, when the Ethereum block containing the reporting transaction was created.
* **`marketID`** (string) Contract address of the Market, as a hexadecimal string.
* **`marketReportingState`** (<a href="#REPORTING_STATE">REPORTING_STATE</a>) Reporting state of the Market.
* **`reportingWindow`** (string) Reporting window the market is in currently.
* **`payoutNumerators`** (Array.&lt;number>) Array representing the payout set.
* **`amountStaked`** (number) Description the reporter has staked on the Outcome of their report.
* **`stakeToken`** (string) Contract address of the stake token, as a hexadecimal string.
* **`isCategorical`** (boolean) Whether the Market is categorical.
* **`isScalar`** (boolean) Whether the Market is scalar.
* **`isIndeterminate`** (boolean) Description pending.
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

<a name="SingleOutcomeOrderBookSide"></a>
### SingleOutcomeOrderBookSide  (Object)

#### **Properties:** 
* **`Buy`**|**`Sell`** (<a href="#Order">Order</a>) bid or ask orders, indexed by order ID.

<a name="SingleOutcomePriceTimeSeries"></a>
### SingleOutcomePriceTimeSeries  (Object)

#### **Properties:** 
* **`Array`** (Array.&lt;<a href="#TimestampedPrice">TimestampedPrice</a>>) of timestamped price points for this Outcome.

<a name="STAKE_TOKEN_STATE"></a>
### STAKE_TOKEN_STATE  (Object)

Serves as an enum for the state of a stake token.

#### **Properties:** 
* **`ALL`** (string) Stake token can be in any state. (If no stake token state is specified, this is the default value.)
* **`UNFINALIZED`** (string) Stake token is in a Market that has not been finalized.
* **`UNCLAIMED`** (string) Stake token is in a finalized Market, was staked on the correct Outcome, and has not been claimed yet.

<a name="StakeToken"></a>
### StakeToken  (Object)

#### **Properties:** 
* **`stakeToken`** (string) Contract address of the stake token, as a hexidecimal string.
* **`marketID`** (string) ID of the Market, as a hexidecimal string.
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
* **`winningToken`** (number|null) Description pending.
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