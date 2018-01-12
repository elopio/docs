Events API
===========
<aside class="notice">The Events section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

```javascript
// Listen for Events emitted by the Augur contracts
// contractAddresses is a JSON object containing the name and address of the Augur contracts.
var contractAddresses = {
  ReputationToken: "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e",
 /* ... */
};
// Events API is a JSON object containing the event labels, and information about each event
var eventsAPI = {
  Approval: {
    address: "0x9308cf21b5a11f182f9707ca284bbb71bb84f893",
    /* ... */
  },
  /* ... */
}
// Both contractAddress and eventsAPI can be found in successful connection object received from calling the augur.connect() function. The contractAddresses can be found in connectionObject.contracts and the events API can be found at connectionObject.api.events.

// Start listening for Events
augur.filters.listen(contractAddresses, eventsAPI, {
    approval: function (event) { /* ... */ },
    block: function (blockhash) { /* ... */ },
    burn: function (event) { /* ... */ },
    cancelOrder: function (event) { /* ... */ },
    completeSets: function (event) { /* ... */ },
    depositEther: function (event) { /* ... */ },
    initiateWithdrawEther: function (event) { /* ... */ },
    makeOrder: function (event) { /* ... */ },
    mint: function (event) { /* ... */ },
    takeOrder: function (event) { /* ... */ },
    transfer: function (event) { /* ... */ },
    withdrawEther: function (event) { /* ... */ },
  },
  function (filters) { console.log('Listening to filters:', filters); }
);

// Stop listening for events and delete (uninstall) filters
augur.filters.ignore();

/**
 * Search for historical events.
 */
var myAccountAddress = "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b";
var myFriendsAddress = "0x6c15291028d082e1b9358e19f15c83b9c54f2ba1";

// Look up Transfers from my account to my friends account.
var params = {
  label: "Transfer",
  filter: { from: myAccountAddress, to: myFriendsAddress }
};

augur.logs.getLogs(params, function (err, logs) { /* ... */ });

// Logs will look something like this:
logs = [{
  blockNumber: 6200,
  removed: false,
  from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
  to: "0x6c15291028d082e1b9358e19f15c83b9c54f2ba1",
  value: "1000000000000",
  timestamp: 1502305142,
  transactionHash: "0xba9c4044153059f584ddc8ea61d89c4cfb5421fe81c014baf90b8918a1622028"
}];

// now only look for Transfers between block 2000 and 6000.
params = {
  label: "Transfer",
  filter: {
    from: myAccountAddress,
    to: myFriendsAddress,
    toBlock: 6000,
    fromBlock: 2000
  }
};

augur.logs.getLogs(params, function (err, logs) { /* ... */ });

// in this case we have no logs in those blocks for the filters specified
// so an empty array is returned.
logs = [];

// lets add the aux object to our params.
// In this case we are going to look for all Transfer transactions from myAccountAddress
// to myFriendsAddress, starting from block 2000 all the way to the latest block.
// We would also like to have the logs object organized by from field, in this case just myAccountAddress as we specified that as the only from address to look for.
// We also would like to add an extra field to each log called "anotherField"
// its value should always be "testing"
params = {
  label: "Transfer",
  filter: {
    from: myAccountAddress,
    to: myFriendsAddress,
    toBlock: "latest",
    fromBlock: 2000
  },
  aux: {
    index: "from",
    extraField: { name: "anotherField", value: "testing" }
  }
};

augur.logs.getLogs(params, function (err, logs) { /* ... */ });

// The transactions are organized by from address and this time they have an added extra field
logs = {
  "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b": [{
    blockNumber: 6200,
    removed: false,
    from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
    to: "0x6c15291028d082e1b9358e19f15c83b9c54f2ba1",
    value: "1000000000000",
    timestamp: 1502305142,
    transactionHash: "0xba9c4044153059f584ddc8ea61d89c4cfb5421fe81c014baf90b8918a1622028",
    anotherField: "testing"
  },
  {
    blockNumber: 6400,
    removed: false,
    from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
    to: "0x6c15291028d082e1b9358e19f15c83b9c54f2ba1",
    value: "100000000000000",
    timestamp: 1502306213,
    transactionHash: "0x13c40b9ed0fd124e9bfcae2bd41e6dc8163d4ac0012c69c0efe57f502ecf81ea",
    anotherField: "testing"
  }]
};

// finally, we can merge more than one search by adding the mergedLogs object to the aux object.
params = {
  label: "Transfer",
  filter: {
    from: myAccountAddress,
    to: myFriendsAddress,
    toBlock: 6300,
    fromBlock: 5000
  },
  aux: {
    index: "from",
    extraField: { name: "firstSearch", value: true },
    mergedLogs: {}
  }
};

augur.logs.getLogs(params, function (err, logs) {
  // the first set of logs have been returned, change the params and
  // call again to merge these calls together
  params.filter.toBlock = 'latest';
  params.filter.fromBlock = 6300;
  params.aux.extraField = null;

  augur.logs.getLogs(params, function (err, logs) {
    // logs will look something like this, only the first searches logs have the extra field.
    logs = {
      "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b": [{
        blockNumber: 6200,
        removed: false,
        from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
        to: "0x6c15291028d082e1b9358e19f15c83b9c54f2ba1",
        value: "1000000000000",
        timestamp: 1502305142,
        transactionHash: "0xba9c4044153059f584ddc8ea61d89c4cfb5421fe81c014baf90b8918a1622028",
        firstSearch: true
      },
      {
        blockNumber: 6400,
        removed: false,
        from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
        to: "0x6c15291028d082e1b9358e19f15c83b9c54f2ba1",
        value: "100000000000000",
        timestamp: 1502306213,
        transactionHash: "0x13c40b9ed0fd124e9bfcae2bd41e6dc8163d4ac0012c69c0efe57f502ecf81ea",
      }]
    };
  });
});
// (Note: for efficiency, the mergedLogs field in aux is mutated by getLogs.  If this
// bothers you, feel free to use lodash's merge, ES6/7 spread, etc. to accomplish the same thing.
// Also, the tears of functional programmers are delicious. ;)
```
There are a variety of "events" emitted by the Augur contracts.  Each event is triggered by a user doing something on Augur: submitting a report, closing a [Market](#market), filling an [Open Order](#open-order), etc.

<aside class="notice">The events API described here should not be confused with the (entirely unrelated) concept of "events" that Reporters Report on.  We think that the concepts are sufficiently different that the context should always make it clear which kind of "event" is being referred to.  In the event that you encounter an ambiguity, please drop us a note at <a href="mailto:hugs@augur.net">hugs@augur.net</a>, or just violently rage at us on <a href="https://www.reddit.com/r/augur">Reddit</a> or <a href="https://twitter.com/AugurProject">Twitter</a>!</aside>

The augur.js events API includes event listeners, which notify you of events happening now, and a search function for historical events ("logs"):

- *Event listeners* ("filters") can be turned on using the `augur.filters.listen` function.  `augur.filters.listen` accepts four arguments: a JSON object containing the contracts and their addresses, a JSON object containing information about each event, an object with event labels as keys (see table below), and callbacks for these events as values.  Each callback has a single argument, an object containing the "Data" fields shown in the table below.
- *Event search* can be accomplished with the `augur.logs.getLogs` function.  `augur.logs.getLogs` can search for any combination of an event's *indexed* data fields (enumerated in the table below), and also accepts optional upper- and lower-bound block numbers to further narrow down the search.

The following table shows the events that can be passed to `augur.filters.listen`.  All of these events are optional; if you don't need some of these events for your application, don't include the event in your call to `augur.filters.listen`.  In this table, "Contract" refers to the Ethereum contract on which the event is defined ([source code](https://github.com/AugurProject/augur-core) / [contract addresses](https://github.com/AugurProject/augur-contracts)), "Data (indexed)" refers to fields included in the event log that are searchable using the `augur.logs.getLogs` function.

Label                     | Contract                                                                                   | Event Description | Data (indexed) | Data (non-indexed)
------------------------- | ------------------------------------------------------------------------------------------ | ----------------- | -------------- | ------------------
<a name="Approval"></a>Approval                  | [ERC20](https://github.com/AugurProject/augur-core/blob/master/source/contracts/libraries/token/ERC20.sol) | `spender` has been approved to spend `value` amount of ERC20 tokens on behalf of `owner`. | owner, spender | value
<a name="Burn"></a>Burn                      | [VariableSupplyToken](https://github.com/AugurProject/augur-core/blob/master/source/contracts/libraries/token/VariableSupplyToken.sol) | `value` amount of `target`'s tokens have been burned (i.e., completely destroyed) .  | target | value
<a name="DisputeCrowdsourcerCompleted"></a>DisputeCrowdsourcerCompleted | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | The Ethereum contract address `disputeCrowdsourcer` for `market` in `universe` filled the [Dispute Bond](#dispute-bond) required to [Challenge](#challenge) `market`'s [Tentative Outcome](#tentative-outcome). | universe, market | disputeCrowdsourcer 
<a name="DisputeCrowdsourcerContribution"></a>DisputeCrowdsourcerContribution | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | The Ethereum address `reporter` [Staked](#dispute-stake) `amountStaked` [REP](#rep) on the [Outcome](#outcome) for `disputeCrowdsourcer` in `market` of `universe`. | universe, reporter, market | disputeCrowdsourcer, amountStaked
<a name="DisputeCrowdsourcerCreated"></a>DisputeCrowdsourcerCreated | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | The Ethereum contract address `disputeCrowdsourcer` with [Payout Set](#payout-set) `payoutNumerators` and [Dispute Bond](#dispute-bond) size `size` was created in `market` of `universe`. | universe, market | disputeCrowdsourcer, payoutNumerators, size
<a name="FeeWindowCreated"></a>FeeWindowCreated          | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | The Ethereum contract address `feeWindow` has created a new [Fee Window](#fee-window) (with ID `id` and running from `startTime` to `endTime`) in `universe`. | universe | feeWindow, startTime, endTime, id
<a name="MarketCreated"></a>MarketCreated             | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | `marketCreator` has created a `marketType` `market` with `outcomes` and `topic` in `universe` for a price of `marketCreationFee` and a price range of `minPrice` to `maxPrice`. Additional information about `market` can be found in `description` and `extraInfo`. | topic, universe, marketCreator | description, extraInfo, market, outcomes, marketCreationFee, minPrice, maxPrice, marketType
<a name="InitialReportSubmitted"></a>InitialReportSubmitted | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | `reporter` has submitted an Initial Report for the `market` in `universe` with `amountStaked` REP  staked on the Outcome `payoutNumerators`. If `reporter` is the Designated Reporter (as opposed to the First Public Reporter), `isDesignatedReporter` is set to true. | universe, reporter, market | amountStaked, isDesignatedReporter, payoutNumerators
<a name="MarketFinalized"></a>MarketFinalized           | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | The Outcome of `market` in `universe` is now considered final. | universe, market |  
<a name="Mint"></a>Mint                      | [VariableSupplyToken](https://github.com/AugurProject/augur-core/blob/master/source/contracts/libraries/token/VariableSupplyToken.sol) | `value` amount of brand new tokens were created for `target`. | target | value
<a name="OrderCanceled"></a>OrderCanceled             | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | `sender`'s Order (with ID `orderId` and type `orderType`) for the Share Token at address `shareToken` was canceled in `universe`, and `sender` was refunded either `tokenRefund` in ETH or `sharesRefund` Share Tokens. | universe, shareToken, sender | orderId, orderType, tokenRefund, sharesRefund
<a name="OrderCreated"></a>OrderCreated              | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | `creator` placed an `orderType` Order with `orderId` in `universe` for `amount` of `shareToken` at `price` in the Trade Group `tradeGroupId`. `creator` put up either `moneyEscrowed` or `sharesEscrowed`. | creator, universe, shareToken  | orderType, amount, price, moneyEscrowed, sharesEscrowed, tradeGroupId, orderId
<a name="OrderFilled"></a>OrderFilled               | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | Order `orderId` in `universe` for Share Token at address `shareToken` was filled by `filler` in Trade Group `tradeGroupId`. The Order Creator escrowed `numCreatorShares` or `numCreatorTokens` in ETH, and `filler` put up `numFillerShares` or `numFillerTokens` in ETH. `marketCreatorFees` and `reporterFees` (denominated in attoETH) were spent to pay the Market Creator and Reporters. | universe, shareToken | filler, orderId, numCreatorShares, numCreatorTokens, numFillerShares, numFillerTokens, marketCreatorFees, reporterFees, tradeGroupId
<a name="RegistryAddition"></a>RegistryAddition      | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | The Ethereum contract address `addition` was added to Augur's registry, which is an array of all of the contracts Augur uses. The contract has a 32-byte `key` (for looking up the contract in Augur's registry), a 20-byte `commitHash`, and a 32-byte `bytecodeHash` | &nbsp; | key, addition, commitHash, bytecodeHash
<a name="TokensBurned"></a>TokensBurned              | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | Burned (i.e., completely destroyed) `amount` of `target`'s [attotokens](#atto-prefix) (at address `token`) in `universe`. | universe, token, target | amount
<a name="TokensMinted"></a>TokensMinted              | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | Created `amount` brand new [attotokens](#atto-prefix) (at address `token`) in `universe` for `target`. | universe, token, target | amount
<a name="TokensTransferred"></a>TokensTransferred         | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | `value` amount of the token at address `token` in `universe` has been transfered between account `from` and `to`.  | universe, token, from | to, value
<a name="TradingProceedsClaimed"></a>TradingProceedsClaimed    | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | `sender` has collected trading profits from the Share Token at address `shareToken` in Finalized Market `market` in `universe`. `sender` had `numShares` Share Tokens, `numPayoutTokens` paid out, and a balance of `finalTokenBalance`. | universe, shareToken, sender | market, numShares, numPayoutTokens, finalTokenBalance
<a name="Transfer"></a>Transfer | [ERC20Basic](https://github.com/AugurProject/augur-core/blob/master/source/contracts/libraries/token/ERC20Basic.sol) | Transferred `value` [attotokens](#atto-prefix) between accounts `from` and `to`. | from, to | value
<a name="UniverseCreated"></a>UniverseCreated           | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | `childUniverse` has been created from `parentUniverse`. | parentUniverse, childUniverse | 
<a name="UniverseForked"></a>UniverseForked            | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | A [Market](#market) in `universe` has had its [Tentative Outcome](#tentative-outcome) [Challenged](#challenge) with a [Dispute Bond](#dispute-bond) greater than the [Fork Threshold](#fork-threshold), which has caused `universe` to [Fork](#fork). | universe |
<a name="WhitelistAddition"></a>WhitelistAddition    | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | The Ethereum contract address `addition` was added to Augur's whitelisted contracts. (Many of the functions in Augur's Solidity smart contracts are only callable by contracts that have been whitelisted.) | &nbsp; | addition
<a name="WinningsRedeemed"></a>WinningsRedeemed     | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | The Ethereum contract address `reportingParticipant` has allowed `reporter` to redeem `amountRedeemed` [REP](#rep) in `market` of `universe` for the [Outcome](#outcome) `payoutNumerators`. `reporter` also received `reportingFeesReceived` in ETH. | universe, reporter, market | reportingParticipant, amountRedeemed, reportingFeesReceived, payoutNumerators

In addition to these on-contract events, `augur.filters.listen` also accepts a callback for the `block` listener, which fires whenever a new block arrives.  The argument passed to its callback is the hash (as a hex string) of the new block.

<aside class="success">Events are retrieved either via push notification (if connected via websocket) or by polling the Ethereum node (if using HTTP RPC).  If polling, augur.js will check for new events every 6 seconds.</aside>
