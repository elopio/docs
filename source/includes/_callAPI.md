Call API
========
```javascript
// Call API Example
/**
* Check account 0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b's Cash balance.
*/
const address = "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b";
const params = { _owner: address };

// augur.api.<Contract Name>.<Contract Method>(<Params Object>, <Callback Function>);

// asynchronously -- STRONGLY RECOMMENDED --
augur.api.Cash.balanceOf(params, balance => console.log(balance));
// console prints 9999.495000000000000001

// synchronous -- possible but not recommended.
const money = augur.api.Cash.balanceOf(params);
// money = 9999.495000000000000001
```
<aside class="notice">The Call API section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

Augur's Call API is made up of "getter" methods that retrieve information from the blockchain (using Ethereum's `eth_call` RPC) but does not write information to the blockchain. The Call API is for more granular "gets" then the [Simplified API](#simplified-api) allows for. The Call API is directly mapped to the Augur Contracts and their publicly exposed methods. All Call API functions will accept two arguments, a `params` object with key/value pairs that match inputs for the contract method and a callback function. The Augur API functions are attached to the `augur.api` object and follow a pattern of `augur.api.<Contract Name>.<Contract Method>(<Params Object>, <Callback Function>)`. Although technically optional, the Augur Team **strongly recommends** using a callback. Without the callback the calls will be synchronous which can lock up your browser until they complete.

<aside class="warning">Synchronous HTTP RPC is generally not recommended, especially if augur.js is running in the browser. Synchronous RPC requests block the main JavaScript thread, which essentially freezes the browser!</aside>

Branch Call API
---------------
```javascript
// Branch Contract Call API Examples:
const branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5";
const _parentPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";

augur.api.Branch.getChildBranch({ branch, _parentPayoutDistributionHash }, function (childBranch) { /* ... */ })
// example output:
childBranch = "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c"

augur.api.Branch.getCurrentReportingWindow({ branch }, function (currReportingWindow) { /* ... */ })
// example output:
currReportingWindow = "578"

augur.api.Branch.getForkEndTime({ branch }, function (forkEndTime) { /* ... */ })
// example output:
forkEndTime = "1489855429";

augur.api.Branch.getForkingMarket({ branch }, function (forkingMarket) { /* ... */ })
// example output:
forkingMarket = "0x78f7b43150d27c464359e735781c16ac585f52a8";

augur.api.Branch.getNextReportingWindow({ branch }, function (nextReportingWindow) { /* ... */ })
// example output:
nextReportingWindow = "579"

augur.api.Branch.getParentBranch({ branch }, function (parentBranch) { /* ... */ })
// example output:
parentBranch = "0x63c59544b89cce1dd53b1b566862189b25adec41"

augur.api.Branch.getParentPayoutDistributionHash({ branch }, function (branchParentPayoutDistributionHash) { /* ... */ })
// example output:
branchParentPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Branch.getPreviousReportingWindow({ branch }, function (previousReportingWindow) { /* ... */ })
// example output:
previousReportingWindow = "577"

augur.api.Branch.getReportingPeriodDurationInSeconds({ branch }, function (reportingPeriodDuration) { /* ... */ })
// example output:
reportingPeriodDuration = "2592000";

const _endTime = 2524608000;
augur.api.Branch.getReportingWindowByMarketEndTime({ branch, _endTime, _hasAutomatedReporter: 0 }, function (reportingWindowByEndTime) { /* ... */ })
// example output:
reportingWindowByEndTime = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.Branch.getReportingWindowByTimestamp({ branch, _timestamp: endTime }, function (reportingWindowByTimestamp) { /* ... */ })
// example output:
reportingWindowByTimestamp = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.Branch.getReportingWindowId({ branch, _timestamp: new Date().getTime() }, function (reportingWindowId) { /* ... */ })
// example output:
reportingWindowId = "578";

augur.api.Branch.getReputationToken({ branch }, function (reputationTokenAddress) { /* ... */ })
// example output:
reputationTokenAddress = "0x2fb561b2bdbcd1ae1995bdd6aff6776d6f4292f2";

augur.api.Branch.getTopics({ branch }, function (topicsAddress) { /* ... */ })
// example output:
topicsAddress = "0x14f094c79a676c681e7cc490e775f73072e535ae";

augur.api.Branch.getTypeName({ branch }, function (typeName) { /* ... */ })
// example output:
typeName = "Branch";

const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.Branch.isContainerForMarket({ branch, _shadyTarget: market }, function (isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1";

const registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
augur.api.Branch.isContainerForRegistrationToken({ branch, _shadyTarget: registrationToken }, function (isContainerForRegistrationToken) { /* ... */ })
// example output:
isContainerForRegistrationToken = "1";

const reportingToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
augur.api.Branch.isContainerForReportingToken({ branch, _shadyTarget: reportingToken }, function (isContainerForReportingToken) { /* ... */ })
// example output:
isContainerForReportingToken = "1";

const reportingWindow = "578";
augur.api.Branch.isContainerForReportingWindow({ branch, _shadyTarget: reportingWindow }, function (isContainerForReportingWindow) { /* ... */ })
// example output:
isContainerForReportingWindow = "1";

const childBranch = "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c";
augur.api.Branch.isParentOf({ branch, _shadyChild: childBranch }, function (isParentOf) { /* ... */ })
// example output:
isParentOf = "1";
```
#### [Branch Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/branch.sol)

#### augur.api.Branch.getChildBranch({ branch, \_parentPayoutDistributionHash }[, callback])

Returns the child branch address of the specified `branch` address related to the `_parentPayoutDistributionHash` provided.

#### augur.api.Branch.getCurrentReportingWindow({ branch }[, callback])

Returns the current reporting window ID for the specified `branch`.

#### augur.api.Branch.getForkEndTime({ branch }[, callback])

Returns the timestamp for when the fork ends given a specified `branch`.

#### augur.api.Branch.getForkingMarket({ branch }[, callback])

Returns the address of the market that the specified `branch` is forking over.

#### augur.api.Branch.getNextReportingWindow({ branch }[, callback])

Returns the next reporting window ID for a specific `branch`.

#### augur.api.Branch.getParentBranch({ branch }[, callback])

Returns the parent branch address of the specified `branch`.

#### augur.api.Branch.getParentPayoutDistributionHash({ branch }[, callback])

Returns the parent branch's payout distribution hash given a `branch` address.

#### augur.api.Branch.getPreviousReportingWindow({ branch }[, callback])

Returns the previous reporting window ID for the specified `branch`.

#### augur.api.Branch.getReportingPeriodDurationInSeconds({ branch }[, callback])

Returns the specified `branch`'s reporting period duration in seconds.

#### augur.api.Branch.getReportingWindowByMarketEndTime({ branch, \_endTime, \_hasAutomatedReporter }[, callback])

Returns the reporting window address on the specific `branch` given an `_endTime` and if the market we are checking for has an automated reporter or not (`_hasAutomatedReporter`). `_hasAutomatedReporter` should be `0` for markets without an automated reporter, `1` for markets with an automated reporter.

#### augur.api.Branch.getReportingWindowByTimestamp({ branch, \_timestamp }[, callback])

Returns the reporting window address for a specific `branch` and provided `_timestamp`.

#### augur.api.Branch.getReportingWindowId({ branch, \_timestamp }[, callback])

Returns the reporting window ID for a specific `branch` and provided `_timestamp`. This is calculated by dividing the timestamp by the branch's reporting period duration in seconds.

#### augur.api.Branch.getReputationToken({ branch }[, callback])

Returns the reputation token address for a specific `branch`.

#### augur.api.Branch.getTopics({ branch }[, callback])

Returns the topics address for the specific `branch`.

#### augur.api.Branch.getTypeName({ branch }[, callback])

Returns the specified `branch`'s type name, which should always return "Branch".

#### augur.api.Branch.isContainerForMarket({ branch, \_shadyTarget }[, callback])

Returns wether the specific `branch` is a container for the `_shadyTarget` address provided. Returns `1` if true, `0` if false.

#### augur.api.Branch.isContainerForRegistrationToken({ branch, \_shadyTarget }[, callback])

Returns wether the specific `branch` is a container for the `_shadyTarget` address provided. Returns `1` if true, `0` if false.

#### augur.api.Branch.isContainerForReportingToken({ branch, \_shadyTarget }[, callback])

Returns wether the specific `branch` is a container for the `_shadyTarget` address provided. Returns `1` if true, `0` if false.

#### augur.api.Branch.isContainerForReportingWindow({ branch, \_shadyTarget }[, callback])

Returns wether the specific `branch` is a container for the `_shadyTarget` provided. Returns `1` if true, `0` if false.

#### augur.api.Branch.isParentOf({ branch, \_shadyChild }[, callback])

Returns wether the specific `branch` is a container for the `_shadyChild` branch address provided. Returns `1` if true, `0` if false.

Dispute Bond Token Call API
---------------------------
```javascript
// Dispute Bond Token Contract Call API Examples:
const disputeBondToken = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
const _address = "0x3d62bafc1791752393384b902f813da861ddedd9";

augur.api.DisputeBondToken.balanceOf({ disputeBondToken, _address }, function (balance) { /* ... */ })
// example output:
balance = "1"

augur.api.DisputeBondToken.getBondHolder({ disputeBondToken }, function (bondHolder) { /* ... */ })
// example output:
bondHolder = "0x3d62bafc1791752393384b902f813da861ddedd9"

augur.api.DisputeBondToken.getBondRemainingToBePaidOut({ disputeBondToken }, function (bondRemainingToBePaidOut) { /* ... */ })
// example output:
bondRemainingToBePaidOut = "1100000000000000000000"

augur.api.DisputeBondToken.getBranch({ disputeBondToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.DisputeBondToken.getDisputedPayoutDistributionHash({ disputeBondToken }, function (disputedPayoutDistributionHash) { /* ... */ })
// example output:
disputedPayoutDistributionHash = "0xff89be2020af3cb2ca244eb862df2d350300904a96039eb53cbacf380f13f21b"

augur.api.DisputeBondToken.getMarket({ disputeBondToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.DisputeBondToken.getReputationToken({ disputeBondToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.DisputeBondToken.getTotalSupply({ disputeBondToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "1"

augur.api.DisputeBondToken.getTypeName({ disputeBondToken }, function (typeName) { /* ... */ })
// example output:
typeName = "DisputeBondToken";
```
#### [Dispute Bond Token Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/disputeBondToken.sol)

#### augur.api.DisputeBondToken.balanceOf({ disputeBondToken, \_address }[, callback])

This transaction will return the balance for a specified `disputeBondToken` owned by the provided `_address`. Will always result in `1` or `0`.

#### augur.api.DisputeBondToken.getBondHolder({ disputeBondToken }[, callback])

This transaction will return the address of the bond holder for a specified `disputeBondToken`.

#### augur.api.DisputeBondToken.getBondRemainingToBePaidOut({ disputeBondToken }[, callback])

This transaction will return the amount, denoted in attotokens, of bond remaining to be paid out for a specified `disputeBondToken`.

#### augur.api.DisputeBondToken.getBranch({ disputeBondToken }[, callback])

This transaction will return the branch that a specified `disputeBondToken`'s market lives on.

#### augur.api.DisputeBondToken.getDisputedPayoutDistributionHash({ disputeBondToken }[, callback])

This transaction will return the distribution hash for the dispute payout given a specified `disputeBondToken`.

#### augur.api.DisputeBondToken.getMarket({ disputeBondToken }[, callback])

This transaction will return the market that a specified `disputeBondToken` is disputing the results of.

#### augur.api.DisputeBondToken.getReputationToken({ disputeBondToken }[, callback])

This transaction will return the reputation token address that a specified `disputeBondToken`'s market uses.

#### augur.api.DisputeBondToken.getTotalSupply({ disputeBondToken }[, callback])

This transaction will return the total supply of a specified `disputeBondToken`, which will always return `1`.

#### augur.api.DisputeBondToken.getTypeName({ disputeBondToken }[, callback])

Returns the type name of the specified `disputeBondToken`, this should always return "DisputeBondToken".

Market Call API
----------------
```javascript
// Market Contract Call API Examples:
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Market.canBeReportedOn({ market }, function (canBeReportedOn) { /* ... */ })
// example output:
canBeReportedOn = "1"

augur.api.Market.getAutomatedReportDisputeDueTimestamp({ market }, function (automatedReporterDisputeDueTimestamp) { /* ... */ })
// example output:
automatedReporterDisputeDueTimestamp = "1500907130"

augur.api.Market.getAutomatedReportDueTimestamp({ market }, function (automatedReporterDueTimestamp) { /* ... */ })
// example output:
automatedReporterDueTimestamp = "1500647930"

augur.api.Market.getBranch({ market }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.Market.getCompleteSetCostInAttotokens({ market }, function (completeSetCostInAttotokens) { /* ... */ })
// example output:
completeSetCostInAttotokens = "1000000000000000000"

augur.api.Market.getCreator({ market }, function (marketCreator) { /* ... */ })
// example output:
marketCreator = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa"

augur.api.Market.getDenominationToken({ market }, function (denominationToken) { /* ... */ })
// example output:
denominationToken = "0x30e3852448f4ab5d62bbf7544ca3c92daca5c957"

augur.api.Market.getEndTime({ market }, function (endTime) { /* ... */ })
// example output:
endTime = "1500388730";

augur.api.Market.getFinalizationTime({ market }, function (finalizationTime) { /* ... */ })
// example output:
finalizationTime = "1500647930";

augur.api.Market.getFinalPayoutDistributionHash({ market }, function (finalPayoutDistributionHash) { /* ... */ })
// example output:
finalPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Market.getFinalWinningReportingToken({ market }, function (winningReportingToken) { /* ... */ })
// example output:
winningReportingToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.Market.getMarketCreatorSettlementFeeInAttoethPerEth({ market }, function (marketCreatorSettlementFee) { /* ... */ })
// example output:
marketCreatorSettlementFee = "20000000000000000"

augur.api.Market.getMaxDisplayPrice({ market }, function (maxDisplayPrice) { /* ... */ })
// example output:
maxDisplayPrice = "1"

augur.api.Market.getMinDisplayPrice({ market }, function (minDisplayPrice) { /* ... */ })
// example output:
minDisplayPrice = "0"

augur.api.Market.getNumberOfOutcomes({ market }, function (numOutcomes) { /* ... */ })
// example output:
numOutcomes = "2"

augur.api.Market.getPayoutDenominator({ market }, function (payoutDenominator) { /* ... */ })
// example output:
payoutDenominator = "1"

augur.api.Market.getRegistrationToken({ market }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api.Market.getReportingWindow({ market }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.Market.getReputationToken({ market }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.Market.getShareToken({ market, _outcome: 1 }, function (shareToken) { /* ... */ })
// example output:
shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054"

augur.api.Market.getTentativeWinningPayoutDistributionHash({ market }, function (tentativeWinningPayoutDistributionHash) { /* ... */ })
// example output:
tentativeWinningPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.Market.getTopic({ market }, function (topic) { /* ... */ })
// example output:
topic = "Augur"

augur.api.Market.getTypeName({ market }, function (typeName) { /* ... */ })
// example output:
typeName = "Market";

const reportingToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
augur.api.Market.isContainerForReportingToken({ market, _shadyTarget: reportingToken }, function (isContainerForReportingToken) { /* ... */ })
// example output:
isContainerForReportingToken = "1"

const shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
augur.api.Market.isContainerForShareToken({ market, _shadyTarget: shareToken }, function (isContainerForShareToken) { /* ... */ })
// example output:
isContainerForShareToken = "1"

const disputeBondToken = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
augur.api.Market.isContainerForDisputeBondToken({ market, _shadyTarget: disputeBondToken }, function (isContainerForShareToken) { /* ... */ })
// example output:
isContainerForDisputeBondToken = "1"

augur.api.Market.isDoneWithAllReporters({ market }, function (isDoneWithAllReporters) { /* ... */ })
// example output:
isDoneWithAllReporters = "1"

augur.api.Market.isDoneWithAutomatedReporters({ market }, function (isDoneWithAutomatedReporters) { /* ... */ })
// example output:
isDoneWithAutomatedReporters = "1"

augur.api.Market.isDoneWithLimitedReporters({ market }, function (isDoneWithLimitedReporters) { /* ... */ })
// example output:
isDoneWithLimitedReporters = "1"

augur.api.Market.isFinalized({ market }, function (isFinalized) { /* ... */ })
// example output:
isFinalized = "1"

augur.api.Market.isInAllDisputePhase({ market }, function (isInAllDisputePhase) { /* ... */ })
// example output:
isInAllDisputePhase = "1";

augur.api.Market.isInAllReportingPhase({ market }, function (isInAllReportingPhase) { /* ... */ })
// example output:
isInAllReportingPhase = "1";

augur.api.Market.isInAutomatedDisputePhase({ market }, function (isInAutomatedDisputePhase) { /* ... */ })
// example output:
isInAutomatedDisputePhase = "1";

augur.api.Market.isInAutomatedReportingPhase({ market }, function (isInAutomatedReportingPhase) { /* ... */ })
// example output:
isInAutomatedReportingPhase = "1";

augur.api.Market.isInLimitedDisputePhase({ market }, function (isInLimitedDisputePhase) { /* ... */ })
// example output:
isInLimitedDisputePhase = "1";

augur.api.Market.isInLimitedReportingPhase({ market }, function (isInLimitedReportingPhase) { /* ... */ })
// example output:
isInLimitedReportingPhase = "1";

augur.api.Market.needsMigration({ market }, function (needsMigration) { /* ... */ })
// example output:
needsMigration = "1";

augur.api.Market.shouldCollectReportingFees({ market }, function (shouldCollectReportingFees) { /* ... */ })
// example output:
shouldCollectReportingFees = "1";
```
#### [Market Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/market.sol)

#### augur.api.Market.canBeReportedOn({ market }[, callback])

Returns wether the specific `market` can be reported on. Returns `1` if true, `0` if false.

#### augur.api.Market.getAutomatedReportDisputeDueTimestamp({ market }[, callback])

Returns the timestamp for when a specific `market`'s  automated report process and dispute period should be completed.

#### augur.api.Market.getAutomatedReportDueTimestamp({ market }[, callback])

Returns the timestamp for when a specific `market`'s  automated report process should be completed but doesn't count the dispute period.

#### augur.api.Market.getBranch({ market }[, callback])

Returns the branch ID of the branch that the specified `market` is contained within.

#### augur.api.Market.getCompleteSetCostInAttotokens({ market }[, callback])

Returns the cost of a complete set for a specific `market` denominated in attotokens.

#### augur.api.Market.getCreator({ market }[, callback])

Returns the address of the creator of the specified `market`.

#### augur.api.Market.getDenominationToken({ market }[, callback])

Returns the address of the token used to denominate the specified `market`.

#### augur.api.Market.getEndTime({ market }[, callback])

Returns the timestamp for when the specified `market`'s event has come to pass and the `market` is ready to be reported on.

#### augur.api.Market.getFinalizationTime({ market }[, callback])

Returns the timestamp for when the specified `market` was finalized.

#### augur.api.Market.getFinalPayoutDistributionHash({ market }[, callback])

Returns the final payout distribution hash for a specified `market`.

#### augur.api.Market.getFinalWinningReportingToken({ market }[, callback])

Returns the winning reporting token address for a specified `market`.

#### augur.api.Market.getMarketCreatorSettlementFeeInAttoethPerEth({ market }[, callback])

Returns the fee paid to the `market` creator denominated in attotokens for 1 token settled on the market.

#### augur.api.Market.getMaxDisplayPrice({ market }[, callback])

Returns the maximum displayed price for the specified `market`.

#### augur.api.Market.getMinDisplayPrice({ market }[, callback])

Returns the minimum displayed price for the specified `market`.

#### augur.api.Market.getNumberOfOutcomes({ market }[, callback])

Returns the number of outcomes for a specified `market`.

#### augur.api.Market.getPayoutDenominator({ market }[, callback])

Returns the payout denominator for a specified `market`.

#### augur.api.Market.getRegistrationToken({ market }[, callback])

Returns the registration token's address for the specified `market`.

#### augur.api.Market.getReportingWindow({ market }[, callback])

Returns the reporting window address for the specified `market`.

#### augur.api.Market.getReputationToken({ market }[, callback])

Returns the reputation token's address for the specified `market`.

#### augur.api.Market.getShareToken({ market, \_outcome }[, callback])

Returns the share token's address for the specified `market` and `_outcome`.

#### augur.api.Market.getTentativeWinningPayoutDistributionHash({ market }[, callback])

Returns the tentatively winning Payout Distribution Hash given a specified `market`.

#### augur.api.Market.getTopic({ market }[, callback])

Returns the topic of the specified `market`.

#### augur.api.Market.getTypeName({ market }[, callback])

Returns the type name for the specified `market`, should always return "Market".

#### augur.api.Market.isContainerForReportingToken({ market, \_shadyTarget }[, callback])

Returns wether the specific `market` is a container for the `_shadyTarget` address provided, which is the reporting token address we intend to check. Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForShareToken({ market, \_shadyTarget }[, callback])

Returns wether the specific `market` is a container for the `_shadyTarget` address provided, which is the share token address we intend to check. Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForDisputeBondToken({ market, \_shadyTarget }[, callback])

Returns wether the specific `market` is a container for the `_shadyTarget` address provided. A `_shadyTarget` will return true if it is a automated report dispute bond token, a limited reporter dispute bond token, or an all reporters dispute bond token for this `market`. Returns `1` if true, `0` if false.

#### augur.api.Market.isDoneWithAllReporters({ market }[, callback])

Returns wether the specific `market` is done with the all reporting phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isDoneWithAutomatedReporters({ market }[, callback])

Returns wether the specific `market` is done with the automated reporting phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isDoneWithLimitedReporters({ market }[, callback])

Returns wether the specific `market` is done with the limited reporting phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isFinalized({ market }[, callback])

Returns wether the specific `market` has been finalized or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInAllDisputePhase({ market }[, callback])

Returns wether the specific `market` is current in the All Dispute Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInAllReportingPhase({ market }[, callback])

Returns wether the specific `market` is current in the All Reporting Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInAutomatedDisputePhase({ market }[, callback])

Returns wether the specific `market` is current in the Automated Dispute Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInAutomatedReportingPhase({ market }[, callback])

Returns wether the specific `market` is current in the Automated Reporting Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInLimitedDisputePhase({ market }[, callback])

Returns wether the specific `market` is current in the Limited Dispute Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInLimitedReportingPhase({ market }[, callback])

Returns wether the specific `market` is current in the the Limited Reporting Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.needsMigration({ market }[, callback])

Returns wether the specific `market` needs to be migrated to a new branch or not. Returns `1` if true, `0` if false.

#### augur.api.Market.shouldCollectReportingFees({ market }[, callback])

Returns wether the specific `market` should collect reporting fees or not. Returns `1` if true, `0` if false.

Registration Token Call API
---------------------------
```javascript
// Registration Token Contract Call API Examples:
const registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
const _owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const _spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.RegistrationToken.allowance({ registrationToken, _owner, _spender }, function (allowance) { /* ... */ })
// example output:
allowance = "1";

augur.api.RegistrationToken.balanceOf({ registrationToken, _owner }, function (balance) { /* ... */ })
// example output:
balance = "1";

augur.api.RegistrationToken.getBranch({ registrationToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.RegistrationToken.getPeakSupply({ registrationToken }, function (peakSuppy) { /* ... */ })
// example output:
peakSuppy = "234"

augur.api.RegistrationToken.getReportingWindow({ registrationToken }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.RegistrationToken.getReputationToken({ registrationToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.RegistrationToken.getTotalSupply({ registrationToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "215";

augur.api.RegistrationToken.getTypeName({ registrationToken }, function (typeName) { /* ... */ })
// example output:
typeName = "RegistrationToken";
```
#### [Registration Token Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/registrationToken.sol)

#### augur.api.RegistrationToken.allowance({ registrationToken, \_owner, \_spender }[, callback])

Returns the allowance that a specified `_spender` can spend of the `_owner`'s `registrationToken`s.

#### augur.api.RegistrationToken.balanceOf({ registrationToken, \_owner }[, callback])

Returns the token balance for the specified `registrationToken` owned by the `_owner` provided.

#### augur.api.RegistrationToken.getBranch({ registrationToken }[, callback])

Returns the Branch address for the specified `registrationToken`'s Reporting Window.

#### augur.api.RegistrationToken.getPeakSupply({ registrationToken }[, callback])

Returns the peak supply of tokens for the specified `registrationToken`.

#### augur.api.RegistrationToken.getReportingWindow({ registrationToken }[, callback])

Returns the Reporting Window address for the specified `registrationToken`.

#### augur.api.RegistrationToken.getReputationToken({ registrationToken }[, callback])

Returns the Reputation Tokens address for the specific `registrationToken`'s Reporting Window.

#### augur.api.RegistrationToken.getTotalSupply({ registrationToken }[, callback])

Returns the current total supply of the specified `registrationToken`.

#### augur.api.RegistrationToken.getTypeName({ registrationToken }[, callback])

Returns the type name for the specified `registrationToken`, should always return "RegistrationToken".

Reporting Token Call API
------------------------
```javascript
// Reporting Token Contract Call API Examples:
const reportingToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
const _owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const _spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.ReportingToken.allowance({ reportingToken, _owner, _spender }, function (allowance) { /* ... */ })
// example output:
allowance = "1"

augur.api.ReportingToken.balanceOf({ reportingToken, _owner }, function (balance) { /* ... */ })
// example output:
balance = "1"

augur.api.ReportingToken.getBranch({ reportingToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReportingToken.getMarket({ reportingToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.ReportingToken.getPayoutDistributionHash({ reportingToken }, function (payoutDistributionHash) { /* ... */ })
// example output:
payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.ReportingToken.getPayoutNumerator({ reportingToken, index: 0 }, function (payoutNumerator) { /* ... */ })
// example output:
payoutNumerator = "1"

augur.api.ReportingToken.getRegistrationToken({ reportingToken }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api.ReportingToken.getReportingWindow({ reportingToken }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.ReportingToken.getReputationToken({ reportingToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.ReportingToken.getTotalSupply({ reportingToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "210"

augur.api.ReportingToken.getTypeName({ reportingToken }, function (typeName) { /* ... */ })
// example output:
typeName = "ReportingToken";
```
#### [Reporting Token Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reportingToken.sol)

#### augur.api.ReportingToken.allowance({ reportingToken, \_owner, \_spender }[, callback])

Returns the allowance that a specified `_spender` can spend of the `_owner`'s `reportingToken`s.

#### augur.api.ReportingToken.balanceOf({ reportingToken, \_owner }[, callback])

Returns the token balance for the specified `reportingToken` owned by the provided `_owner`.

#### augur.api.ReportingToken.getBranch({ reportingToken }[, callback])

Returns the Branch address for the specified `reportingToken`'s Reporting Window.

#### augur.api.ReportingToken.getMarket({ reportingToken }[, callback])

Returns the market address for the specified `reportingToken`.

#### augur.api.ReportingToken.getPayoutDistributionHash({ reportingToken }[, callback])

Returns the payoutDistributionHash for a specific `reportingToken`.

#### augur.api.ReportingToken.getPayoutNumerator({ reportingToken, index }[, callback])

Returns the payout Numerator for a specific `reportingToken` given an outcome `index`.

#### augur.api.ReportingToken.getRegistrationToken({ reportingToken }[, callback])

Returns the Registration Token address for this specific `reportingToken`.

#### augur.api.ReportingToken.getReportingWindow({ reportingToken }[, callback])

Returns the Reporting Window address for the specified `reportingToken`.

#### augur.api.ReportingToken.getReputationToken({ reportingToken }[, callback])

Returns the Reputation Tokens address for the specific `reportingToken`'s Reporting Window.

#### augur.api.ReportingToken.getTotalSupply({ reportingToken }[, callback])

Returns the current total supply of the specified `reportingToken`.

#### augur.api.ReportingToken.getTypeName({ reportingToken }[, callback])

Returns the type name for the specified `reportingToken`, should always return "ReportingToken".

Reporting Window Call API
-------------------------
```javascript
// Reporting Window Contract Call API Examples:
const reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.ReportingWindow.getBranch({ reportingWindow }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReportingWindow.getDisputeEndTime({ reportingWindow }, function (disputeEndTime) { /* ... */ })
// example output:
disputeEndTime = "1500907130"

augur.api.ReportingWindow.getDisputeStartTime({ reportingWindow }, function (disputeStartTime) { /* ... */ })
// example output:
disputeStartTime = "1500647930"

augur.api.ReportingWindow.getEndTime({ reportingWindow }, function (endTime) { /* ... */ })
// example output:
endTime = "1500388730"

augur.api.ReportingWindow.getMaxReportsPerLimitedReporterMarket({ reportingWindow }, function (maxReportsPerLimitedReporterMarket) { /* ... */ })
// example output:
maxReportsPerLimitedReporterMarket = "9"

const _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.ReportingWindow.getNumberOfReportsByMarket({ reportingWindow, _market }, function (numOfReportsByMarket) { /* ... */ })
// example output:
numOfReportsByMarket = "10";

augur.api.ReportingWindow.getRegistrationToken({ reportingWindow }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api.ReportingWindow.getReportingEndTime({ reportingWindow }, function (reportingEndTime) { /* ... */ })
// example output:
reportingEndTime = "1500647900"

augur.api.ReportingWindow.getReportingStartTime({ reportingWindow }, function (reportingStartTime) { /* ... */ })
// example output:
reportingStartTime = "14998315100"

const _reporter = "0x2cd999e2f90dfc237ccbc52e2a469e1e11221f75";
augur.api.ReportingWindow.getReportsByReporter({ reportingWindow, _reporter }, function (reportsByReporter) { /* ... */ })
// example output:
reportsByReporter = "0x03198001d1c223b2fdb1866703a7c2e4d5313f80"

augur.api.ReportingWindow.getReputationToken({ reportingWindow }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.ReportingWindow.getRequiredReportsPerReporterForlimitedReporterMarkets({ reportingWindow }, function (requiredReportsPerReporterForLimitedReporterMarkets) { /* ... */ })
// example output:
requiredReportsPerReporterForLimitedReporterMarkets = "2"

augur.api.ReportingWindow.getStartTime({ reportingWindow }, function (startTime) { /* ... */ })
// example output:
startTime = "14995895900"

augur.api.ReportingWindow.getTargetReportsPerLimitedReporterMarket({ reportingWindow }, function (targetReportsPerLimitedReporterMarket) { /* ... */ })
// example output:
targetReportsPerLimitedReporterMarket = "3"

augur.api.ReportingWindow.getTargetReportsPerReporter({ reportingWindow }, function (targetReportsPerReporter) { /* ... */ })
// example output:
targetReportsPerReporter = "5"

augur.api.ReportingWindow.isActive({ reportingWindow }, function (isActive) { /* ... */ })
// example output:
isActive = "1"

augur.api.ReportingWindow.isContainerForMarket({ reportingWindow, _shadyMarket: _market }, function (isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1"

const _registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
augur.api.ReportingWindow.isContainerForRegistrationToken({ reportingWindow, _shadyRegistrationToken: _registrationToken }, function (isContainerForRegistrationToken) { /* ... */ })
// example output:
isContainerForRegistrationToken = "1"

augur.api.ReportingWindow.isDisputeActive({ reportingWindow }, function (isDisputeActive) { /* ... */ })
// example output:
isDisputeActive = "1"

augur.api.ReportingWindow.isDoneReporting({ reportingWindow, _reporter }, function (isDoneReporting) { /* ... */ })
// example output:
isDoneReporting = "1"

augur.api.ReportingWindow.isReportingActive({ reportingWindow }, function (isReportingActive) { /* ... */ })
// example output:
isReportingActive = "1"
```
#### [Reporting Window Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reportingWindow.sol)

#### augur.api.ReportingWindow.getBranch({ reportingWindow }[, callback])

Returns the branch address that contains the specified `reportingWindow`.

#### augur.api.ReportingWindow.getDisputeEndTime({ reportingWindow }[, callback])

Returns the Dispute End Time for a specified `reportingWindow`.

#### augur.api.ReportingWindow.getDisputeStartTime({ reportingWindow }[, callback])

Returns the Dispute Start Time for the specified `reportingWindow`.

#### augur.api.ReportingWindow.getEndTime({ reportingWindow }[, callback])

Returns the End Time for the specified `reportingWindow`.

#### augur.api.ReportingWindow.getMaxReportsPerLimitedReporterMarket({ reportingWindow }[, callback])

Returns the maximum number of reports a limited reporter market can have in the specified `reportingWindow`.

#### augur.api.ReportingWindow.getNumberOfReportsByMarket({ reportingWindow, \_market }[, callback])

Returns the number of reports submitted so far to a specified `_market` in a given `reportingWindow`.

#### augur.api.ReportingWindow.getRegistrationToken({ reportingWindow }[, callback])

Returns the Registration Token address for the specified `reportingWindow`.

#### augur.api.ReportingWindow.getReportingEndTime({ reportingWindow }[, callback])

Returns the reporting phase end time for the specified `reportingWindow`.

#### augur.api.ReportingWindow.getReportingStartTime({ reportingWindow }[, callback])

Returns the reporting phase start time for the specified `reportingWindow`.

#### augur.api.ReportingWindow.getReportsByReporter({ reportingWindow, reporter }[, callback])

Returns the reports set address for the specified `reportingWindow` and `reporter`.

#### augur.api.ReportingWindow.getReputationToken({ reportingWindow }[, callback])

Returns the Reputation Token address for the specified `reportingWindow`.

#### augur.api.ReportingWindow.getRequiredReportsPerReporterForlimitedReporterMarkets({ reportingWindow }[, callback])

Returns the required number of reports per reporter for the limited reporter markets contained within the specified `reportingWindow`.

#### augur.api.ReportingWindow.getStartTime({ reportingWindow }[, callback])

Returns the specified `reportingWindow`'s start time.

#### augur.api.ReportingWindow.getTargetReportsPerLimitedReporterMarket({ reportingWindow }[, callback])

Returns the target number of reports per reporter for the limited reporter markets contained within the specified `reportingWindow`.

#### augur.api.ReportingWindow.getTargetReportsPerReporter({ reportingWindow }[, callback])

Returns the target number of reports per reporter for all markets contained within the specified `reportingWindow`.

#### augur.api.ReportingWindow.isActive({ reportingWindow }[, callback])

Returns wether the specified `reportingWindow` is currently active or not. Returns `1` if true, `0` if false.

#### augur.api.ReportingWindow.isContainerForMarket({ reportingWindow, shadyMarket }[, callback])

Returns wether the specified `reportingWindow` contains the `shadyMarket` or not. Returns `1` if true, `0` if false.

#### augur.api.ReportingWindow.isContainerForRegistrationToken({ reportingWindow, shadyRegistrationToken }[, callback])

Returns wether the specified `reportingWindow` contains the `shadyRegistrationToken` or not. Returns `1` if true, `0` if false.

#### augur.api.ReportingWindow.isDisputeActive({ reportingWindow }[, callback])

Returns wether the specified `reportingWindow`'s dispute phase is active or not. Returns `1` if true, `0` if false.

#### augur.api.ReportingWindow.isDoneReporting({ reportingWindow, reporter }[, callback])

Returns wether the specified `reporter` is finished reporting for the `reportingWindow` or not. Returns `1` if true, `0` if false.

#### augur.api.ReportingWindow.isReportingActive({ reportingWindow }[, callback])

Returns wether the specified `reportingWindow`'s reporting phase is active or not. Returns `1` if true, `0` if false.

Reputation Token Call API
-------------------------
```javascript
// Reputation Token Contract Call API Examples:
const reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
const _owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const _spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.ReputationToken.allowance({ reputationToken, _owner, _spender }, function (allowance) { /* ... */ })
// example output:
allowance = "200"

augur.api.ReputationToken.assertReputationTokenIsLegit({ _shadyReputationToken: reputationToken }, function (isLegitREPToken) { /* ... */ })
// example output:
isLegitREPToken = true;

augur.api.ReputationToken.balanceOf({ reputationToken, _owner }, function (balance) { /* ... */ })
// example output:
balance = "1000"

augur.api.ReputationToken.getBranch({ reputationToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReputationToken.getTopMigrationDestination({ reputationToken }, function (topMigrationDestination) { /* ... */ })
// example output:
topMigrationDestination = "0x1aa30942000ac72dee6580e1ac32d1d01ac1af00"

augur.api.ReputationToken.getTotalSupply({ reputationToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "11000000"

augur.api.ReputationToken.getTypeName({ reputationToken }, function (typeName) { /* ... */ })
// example output:
reputationToken = "ReputationToken";
```
#### [Reputation Token Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reputationToken.sol)

#### augur.api.ReputationToken.allowance({ reputationToken, \_owner, \_spender }[, callback])

Returns the allowance that a specified `_spender` can spend of the `_owner`'s `reputationToken`s.

#### augur.api.ReputationToken.assertReputationTokenIsLegit({ reputationToken, \_shadyReputationToken }[, callback])

Returns true or false depending on if the `_shadyReputationToken` provided is actually a legitimate token belonging to the correct branch for a specified `reputationToken`.

#### augur.api.ReputationToken.balanceOf({ reputationToken, \_owner }[, callback])

Returns the token balance of the specified `reputationToken` owned by the `_owner` provided.

#### augur.api.ReputationToken.getBranch({ reputationToken }[, callback])

Returns the Branch address for the specified `reputationToken`.

#### augur.api.ReputationToken.getTopMigrationDestination({ reputationToken }[, callback])

Returns the top migration destination address for the specified `reputationToken`.

#### augur.api.ReputationToken.getTotalSupply({ reputationToken }[, callback])

Returns the current total supply of the specified `reputationToken`.

#### augur.api.ReputationToken.getTypeName({ reputationToken }[, callback])

Returns the type name for a specified `reputationToken`, this should always return "ReputationToken".

Cash Call API
-------------
```javascript
// Cash Contract Call API Examples:
const _owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const _spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.Cash.allowance({ _owner, _spender }, function (allowance) { /* ... */ })
// example output:
allowance = "100"

augur.api.Cash.balanceOf({ _owner }, function (balance) { /* ... */ })
// example output:
balance = "10000"

augur.api.Cash.getDecimals({}, function (decimals) { /* ... */ })
// example output:
decimals = "18"

augur.api.Cash.getInitiated({}, function (initiated) { /* ... */ })
// example output:
initiated = "1"

augur.api.Cash.getName({}, function (name) { /* ... */ })
// example output:
name = "Cash"

augur.api.Cash.getSymbol({}, function (symbol) { /* ... */ })
// example output:
symbol = "CASH"

augur.api.Cash.getTotalSupply({}, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "11000000"
```
#### [Cash Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/trading/cash.sol)

#### augur.api.Cash.allowance({ \_owner, \_spender }[, callback])

Returns the allowance that a specified `_spender` address can spend of the `_owner` address' Cash tokens.

#### augur.api.Cash.balanceOf({ \_owner }[, callback])

Returns the balance of Cash tokens owned by the specified `_owner`.

#### augur.api.Cash.getDecimals({}[, callback])

Returns the amount of decimal places that the Cash contract is accurate to: `18`, like Ether.

#### augur.api.Cash.getInitiated({}[, callback])

Returns wether the sender of this call has initiated a withdraw of Ether from the Cash contract or not. Returns `1` if true, `0` if false.

#### augur.api.Cash.getName({}[, callback])

Returns the name string for Cash: `Cash`.

#### augur.api.Cash.getSymbol({}[, callback])

Returns the symbol string for Cash: `CASH`.

#### augur.api.Cash.getTotalSupply({}[, callback])

Returns the current total supply of Cash.

Orders Call API
---------------
```javascript
// Orders Contract Call API Examples:
const _orderId = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
const _type = "1";
const _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
const _outcome = "1";
const _fxpPrice = "450000000000000000"; // 0.45

augur.api.Orders.assertIsNotBetterPrice({ _type, _fxpPrice, _betterOrderId: _orderId }, function (isNotBetterPrice) { /* ... */ })
// example output:
isNotBetterPrice = "1"

augur.api.Orders.assertIsNotWorsePrice({ _type, _fxpPrice, _worstOrderId: _orderId }, function (isNotWorstPrice) { /* ... */ })
// example output:
isNotWorstPrice = "0"

augur.api.Orders.getAmount({ _orderId }, function (amount) { /* ... */ })
// example output:
amount = "15"

augur.api.Orders.getBestOrderId({ _type, _market, _outcome }, function (bestOrderID) { /* ... */ })
// example output:
bestOrderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

augur.api.Orders.getBestOrderWorstOrderHash({ _market, _outcome, _type }, function (bestOrderWorstOrderHash) { /* ... */ })
// example output:
bestOrderWorstOrderHash = "0x502caca9a38d2456d8ac2d743b49982a857222b53039f7eee8de94e5d91aec99"

const secondBestOrderID = "0x49cb49f610b5f6e31ee163a8ad65f964af1088e38c8a1b07f1218177b5e006b5";
augur.api.Orders.getBetterOrderId({ _orderId: secondBestOrderID }, function (betterOrderID) { /* ... */ })
// example output:
betterOrderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

augur.api.Orders.getLastOutcomePrice({ _market, _outcome }, function (lastOutcomePrice) { /* ... */ })
// example output:
lastOutcomePrice = "490000000000000000"

augur.api.Orders.getMarket({ _orderId }, function (orderMarket) { /* ... */ })
// example output:
orderMarket = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Orders.getOrderMoneyEscrowed({ _orderId }, function (orderMoneyEscrowed) { /* ... */ })
// example output:
orderMoneyEscrowed = "5000000000000000000"

augur.api.Orders.getOrderMaker({ _orderId }, function (owner) { /* ... */ })
// example output:
owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";

augur.api.Orders.getOrderSharesEscrowed({ _orderId }, function (orderSharesEscrowed) { /* ... */ })
// example output:
orderSharesEscrowed = "0";

augur.api.Orders.getOutcome({ _orderId }, function (orderOutcome) { /* ... */ })
// example output:
orderOutcome = "1";

augur.api.Orders.getPrice({ _orderId }, function (price) { /* ... */ })
// example output:
price = "500000000000000000";

augur.api.Orders.getTradeType({ _orderId }, function (type) { /* ... */ })
// example output:
type = "1";

augur.api.Orders.getVolume({ _market }, function (volume) { /* ... */ })
// example output:
volume = "100000000000000000000000";

const secondWorstOrderID = "0x4b538f4de2517f7d7bbb227161981c51c40bf725da9941b3dc02e6c14cafd1f1";
augur.api.Orders.getWorseOrderId({ _orderId: secondWorstOrderID }, function (worseOrderID) { /* ... */ })
// example output:
worseOrderID = "0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api.Orders.getWorstOrderId({ _type, _market, _outcome }, function (worstOrderID) { /* ... */ })
// example output:
worstOrderID = "0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api.Orders.isBetterPrice({ _type, _fxpPrice, _orderId }, function (isBetterPrice) { /* ... */ })
// example output:
isBetterPrice = "0"

augur.api.Orders.isWorsePrice({ _type, _fxpPrice, _orderId }, function (isWorsePrice) { /* ... */ })
// example output:
isWorsePrice = "1"
```
#### [Orders Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/trading/orders.se)

#### augur.api.Orders.assertIsNotBetterPrice({ \_type, \_fxpPrice, \_betterOrderId }[, callback])

Returns wether the specified `_fxpPrice` is not a better price than the `_betterOrderId` for a given order `_type`. Returns `1` if true, `0` if false.

#### augur.api.Orders.assertIsNotWorsePrice({ \_type, \_fxpPrice, \_worstOrderId }[, callback])

Returns wether the specified `_fxpPrice` is not a worst price than the `_worstOrderId` for a given order `_type`. Returns `1` if true, `0` if false.

#### augur.api.Orders.getAmount({ \_orderId }[, callback])

Returns the amount of [Shares](#shares) requested on a specified `_orderId`.

#### augur.api.Orders.getBestOrderId({ \_type, \_market, \_outcome }[, callback])

Returns the order ID of the best order on the `_market` of `_type` trading on the provided `_outcome`.

#### augur.api.Orders.getBestOrderWorstOrderHash({ \_market, \_outcome, \_type }[, callback])

Returns a sha256 order hash containing the inputted params `_market`, `_outcome`, and `_type`.

#### augur.api.Orders.getBetterOrderId({ \_orderId }[, callback])

Returns a better order ID than the `_orderId`.

#### augur.api.Orders.getLastOutcomePrice({ \_market, \_outcome }[, callback])

Returns the fixed point value of the last price traded for a specified `_market` and `_outcome`.

#### augur.api.Orders.getMarket({ \_orderId }[, callback])

Returns the [Market](#market) address for the specified `_orderId`.

#### augur.api.Orders.getOrderMoneyEscrowed({ \_orderId }[, callback])

Returns the fixed point value of the amount of money escrowed by the [Maker](#maker) for a specified `_orderId`.

#### augur.api.Orders.getOrderMaker({ \_orderId }[, callback])

Returns the [Maker](#maker) address of the specified `_orderId`.

#### augur.api.Orders.getOrderSharesEscrowed({ \_orderId }[, callback])

Returns the fixed point value of the amount of shares escrowed by the [Maker](#maker) for a specified `_orderId`.

#### augur.api.Orders.getOutcome({ \_orderId }[, callback])

Returns the [Outcome](#outcome) being traded on for the specified `_orderId`.

#### augur.api.Orders.getPrice({ \_orderId }[, callback])

Returns the fixed point value of the price of a specified `_orderId`.

#### augur.api.Orders.getTradeType({ \_orderId }[, callback])

Returns the trade type, `1` for buy and `2` for sell, for a specified `_orderId`.

#### augur.api.Orders.getVolume({ \_market }[, callback])

Returns the fixed point value of the volume of a specified `_market`.

#### augur.api.Orders.getWorseOrderId({ \_orderId }[, callback])

Returns a worse order ID than the `_orderId`.

#### augur.api.Orders.getWorstOrderId({ \_type, \_market, \_outcome }[, callback])

Returns the order ID of the worst order on the `_market` of `_type` trading on the provided `_outcome`.

#### augur.api.Orders.isBetterPrice({ \_type, \_fxpPrice, \_orderId }[, callback])

Returns wether the specified `_fxpPrice` is a better price than the `_orderId` for a given order `_type`. Returns `1` if true, `0` if false.

#### augur.api.Orders.isWorsePrice({ \_type, \_fxpPrice, \_orderId }[, callback])

Returns wether the specified `_fxpPrice` is a worst price than the `_orderId` for a given order `_type`. Returns `1` if true, `0` if false.

Orders Fetcher Call API
-----------------------
```javascript
// Orders Fetcher Contract Call API Examples:
const orderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
const type = "1";
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
const outcome = "1";
const fxpPrice = "450000000000000000"; // 0.45

augur.api.OrdersFetcher.ascendOrderList({ type, market, outcome, fxpPrice, lowestOrderID: orderID }, function (ascendingOrderList) { /* ... */ })
// example output:
ascendingOrderList = [
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870",
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0"]

augur.api.OrdersFetcher.descendOrderList({ type, market, outcome, fxpPrice, highestOrderID: orderID }, function (decendingOrderList) { /* ... */ })
// example output:
decendingOrderList = [
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e",
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"]

augur.api.OrdersFetcher.findBoundingOrders({ type, market, outcome, fxpPrice, bestOrderID: orderID, worstOrderID: 0, betterOrderID: 0, worseOrderID: 0 }, function (boundingOrders) { /* ... */ })
// example output:
boundingOrders = [
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0",
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e"]

augur.api.OrdersFetcher.getOrder({ orderID, type, market, outcome }, function (order) { /* ... */ })
// example output:
order = [ "10000000000000000000",
          "500000000000000000",
          "0x438f2aeb8a16745b1cd711e168581ebce744ffaa",
          "5000000000000000000",
          "0",
          "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0",
          "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e",
          "42000000000000"  ]

augur.api.OrdersFetcher.getOrderIDs({ type, market, outcome, startingOrderID: orderID, numOrdersToLoad: 2 }, function (orderIDs) { /* ... */ })
// example output:
orderIDs = [
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870",
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0"]
```
#### [Orders Fetcher Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/trading/ordersFetcher.se)

#### augur.api.OrdersFetcher.ascendOrderList({ type, market, outcome, fxpPrice, lowestOrderID }[, callback])

Returns an array containing the order IDs that should be set to `betterOrderID` and `worseOrderID` respectively for an order inserted at `fxpPrice`. `lowestOrderID` is an order ID expected to be a worse price than the `fxpPrice` specified for an order of `type` trading on `market` around `outcome`.

#### augur.api.OrdersFetcher.descendOrderList({ type, market, outcome, fxpPrice, highestOrderID }[, callback])

Returns an array containing the order IDs that should be set to `betterOrderID` and `worseOrderID` respectively for an order inserted at `fxpPrice`. `highestOrderID` is an order ID expected to be a better price than the `fxpPrice` specified for an order of `type` trading on `market` around `outcome`.

#### augur.api.OrdersFetcher.findBoundingOrders({ type, market, outcome, fxpPrice, bestOrderID, worstOrderID, betterOrderID, worseOrderID }[, callback])

Returns an array containing the order IDs that should be set to `betterOrderID` and `worseOrderID` respectively for an order inserted at `fxpPrice`. `betterOrderID` and `worseOrderID` should be orders that are better or worse than the `fxpPrice` for an order of `type` trading on `market` around `outcome`. `bestOrderID` and `worstOrderID` should be the best and worst order IDs on the order book for the specified `market`, `type`, and `outcome`.

#### augur.api.OrdersFetcher.getOrder({ orderID, type, market, outcome }[, callback])

Returns a length 8 array containing information about a specified `orderID` of `type` trading on `market` around `outcome`. Information returned includes: amount of attoshares, fixed point display price, owner address, tokens escrowed, shares escrowed, better order id, worse order id, and gas price.

#### augur.api.OrdersFetcher.getOrderIDs({ type, market, outcome, startingOrderID, numOrdersToLoad }[, callback])

Returns an array of order IDs of `type` trading on `market` around `outcome` starting from the `startingOrderID` order ID specified. The array will be of length `numOrdersToLoad`.

Share Token Call API
--------------------
```javascript
// Share Token Contract Call API Examples:
const shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
const _owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const _spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.ShareToken.allowance({ shareToken, _owner, _spender }, function (allowance) { /* ... */ })
// example output:
allowance = "100"

augur.api.ShareToken.balanceOf({ shareToken, _owner }, function (balance) { /* ... */ })
// example output:
balance = "1000"

augur.api.ShareToken.getDecimals({ shareToken }, function (decimals) { /* ... */ })
// example output:
decimals = "18"

augur.api.ShareToken.getMarket({ shareToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.ShareToken.getName({ shareToken }, function (name) { /* ... */ })
// example output:
name = "Shares"

augur.api.ShareToken.getOutcome({ shareToken }, function (outcome) { /* ... */ })
// example output:
outcome = "1"

augur.api.ShareToken.getSymbol({ shareToken }, function (symbol) { /* ... */ })
// example output:
symbol = "SHARES"

augur.api.ShareToken.isShareToken({ shareToken }, function (isShareToken) { /* ... */ })
// example output:
isShareToken = "1"

augur.api.ShareToken.getTotalSupply({ shareToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "50000"

augur.api.ShareToken.getTypeName({ shareToken }, function (typeName) { /* ... */ })
// example output:
typeName = "ShareToken";
```
#### [Share Token Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/trading/shareToken.se)

#### augur.api.ShareToken.allowance({ shareToken, \_owner, \_spender }[, callback])

Returns the token allowance a `_spender` can use of an `_owner`'s specified `shareToken`s.

#### augur.api.ShareToken.balanceOf({ shareToken, \_owner }[, callback])

Returns the token balance of a specified `shareToken` owned by the `_owner`.

#### augur.api.ShareToken.getDecimals({ shareToken }[, callback])

Returns the amount of decimal places the `shareToken` is accurate to: `18`.

#### augur.api.ShareToken.getMarket({ shareToken }[, callback])

Returns the market address for the specified `shareToken`.

#### augur.api.ShareToken.getName({ shareToken }[, callback])

Returns the name string of the `shareToken`: `Shares`.

#### augur.api.ShareToken.getOutcome({ shareToken }[, callback])

Returns the Outcome of the market that the specified `shareToken` is for.

#### augur.api.ShareToken.getSymbol({ shareToken }[, callback])

Returns the symbol string of the `shareToken`: `SHARES`.

#### augur.api.ShareToken.isShareToken({ shareToken }[, callback])

Returns wether the `shareToken` is a share token or not. Returns `1` if true, `0` if false. (should always be true.)

#### augur.api.ShareToken.getTotalSupply({ shareToken }[, callback])

Returns the total supply of `shareToken`s specified.

#### augur.api.ShareToken.getTypeName({ shareToken }[, callback])

Returns the type name for a specific `shareToken`, which should always return "ShareToken".

Topics Call API
---------------
```javascript
// Topics Contract Call API Examples:
const topics = "0x14f094c79a676c681e7cc490e775f73072e535ae";
const topic = "Augur";

augur.api.Topics.count({ topics }, function (count) { /* ... */ })
// example output:
count = "152"

augur.api.Topics.getPopularity({ topics, topic }, function (popularity) { /* ... */ })
// example output:
popularity = "1000"

augur.api.Topics.getPopularityByOffset({ topics, offset: 0 }, function () { /* ... */ })
// example output:
popularity = "1000"

augur.api.Topics.getTopicByOffset({ topics, offset: 0 }, function () { /* ... */ })
// example output:
topic = "Augur"
```
#### [Topics Contract Code](https://github.com/AugurProject/augur-core/blob/develop/src/trading/topics.se)

#### augur.api.Topics.count({ topics }[, callback])

Returns a count of all the Topics at the specified `topics` address.

#### augur.api.Topics.getPopularity({ topics, topic }[, callback])

Returns the popularity value of a specified `topic` at the `topics` address provided.

#### augur.api.Topics.getPopularityByOffset({ topics, offset }[, callback])

Returns the popularity of the Topic at the index `offset` at the `topics` address provided.

#### augur.api.Topics.getTopicByOffset({ topics, offset }[, callback])

Returns the Topic at the index `offset` at the `topics` address provided.
