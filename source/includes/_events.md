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

Label                     | Contract                                                                                   | Event description | Data (indexed) | Data (non-indexed)
------------------------- | ------------------------------------------------------------------------------------------ | ----------------- | -------------- | ------------------
Approval                  | [ERC20](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/libraries/token/ERC20.sol) | Approved for the spender to spend a ERC20 token for an owner account | owner, spender | value
Burn                      | [VariableSupplyToken](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/libraries/token/VariableSupplyToken.sol) | Burned the target's tokens to completely destroy them | target | value
DesignatedReportSubmitted | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | A Designated Reporter for a Market has submitted a Report | universe, reporter, market | stakeToken, amountStaked, payoutNumerators
MarketCreated             | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | Someone created a new Market | universe, market, marketCreator | marketCreationFee, extraInfo
MarketFinalized           | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | A Market with a Proposed Outcome is now considered final | universe, market |  | 
Mint                      | [VariableSupplyToken](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/libraries/token/VariableSupplyToken.sol) | Created brand new tokens for target | target | value
OrderCanceled             | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | An Order for some Shares has been canceled | universe, shareToken, sender | orderId, orderType, tokenRefund, sharesRefund
OrderCreated              | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | An Order for a type of Share has been created | universe, shareToken, creator | orderId, tradeGroupId
OrderFilled               | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | An Order for some Shares has been filled | universe, shareToken | filler, orderId, numCreatorShares, numCreatorTokens, numFillerShares, numFillerTokens, settlementFees, tradeGroupId
ReportsDisputed           | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | Someone has put up a Dispute Bond for a Market's Report | universe, disputer, market | reportingPhase, disputeBondAmount
ReportSubmitted           | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | A Report has submitted a Report on a particular Outcome of a Market (either in the First Report Round or the Last Report Round) | universe, reporter, market | stakeToken, amountStaked, payoutNumerators
TokensBurned              | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | Burned the target's tokens to completely destroy them | universe, token, target | amount
TokensMinted              | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | Created brand new tokens for target | universe, token, target | amount
TokensTransferred         | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | Transferred tokens from one owner to another | universe, token, from | to, value
TradingProceedsClaimed    | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | A user has collected trading profits from outstanding Shares in a Finalized Market | universe, shareToken, sender | market, numShares, numPayoutTokens, finalTokenBalance
Transfer | [ERC20Basic](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/libraries/token/ERC20.sol) | Transferred tokens from one owner to another | from, to | value
UniverseCreated           | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | A Child Universe has been created from a Parent Universe | parentUniverse, childUniverse | 
UniverseForked            | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | A Market in the specified Universe has caused that Universe to Fork because its Proposed Outcome has been Disputed after a Last Report Round | universe |
WinningTokensRedeemed     | [Augur](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol) | A user has converted their winning Stake Tokens to REP | universe, reporter, market | stakeToken, amountRedeemed, reportingFeesReceived, payoutNumerators

In addition to these on-contract events, `augur.filters.listen` also accepts a callback for the `block` listener, which fires whenever a new block arrives.  The argument passed to its callback is the hash (as a hex string) of the new block.

<aside class="success">Events are retrieved either via push notification (if connected via websocket) or by polling the Ethereum node (if using HTTP RPC).  If polling, augur.js will check for new events every 6 seconds.</aside>
