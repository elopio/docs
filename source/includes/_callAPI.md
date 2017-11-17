Call API
========
```javascript
// Call API Example
/**
* Get the Number of Outcomes for the Market:
* 0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42
*/

var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var params = { tx: { to: market } };

// augur.api.<Contract Name>.<Contract Method>(<Params Object>, <Callback Function>);

// asynchronously -- STRONGLY RECOMMENDED --
augur.api.Market.getNumberOfOutcomes(params, function (error, numOutcomes) {
  console.log(error);
  console.log(numOutcomes);
});
// console prints 2

// synchronous -- possible but not recommended.
var outcomes = augur.api.Market.getNumberOfOutcomes(error, params);
// outcomes = 2
```
<aside class="notice">The Call API section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

Augur's Call API is made up of "getter" methods that retrieve information from the blockchain (using Ethereum's `eth_call` RPC) but does not write information to the blockchain. The Call API is for more granular "gets" than the [Simplified API](#simplified-api) allows for. The Call API is directly mapped to the Augur Contracts and their publicly exposed methods. All Call API functions will accept two arguments, a `params` object with key/value pairs that match inputs for the contract method and a callback function. The Augur API functions are attached to the `augur.api` object and follow a pattern of `augur.api.<Contract Name>.<Contract Method>(<Params Object>, <Callback Function>)`. Although technically optional, the Augur Team **strongly recommends** using a callback. Without the callback the calls will be synchronous which can lock up your browser until they complete.

<aside class="warning">Synchronous HTTP RPC is generally not recommended, especially if augur.js is running in the browser. Synchronous RPC requests block the main JavaScript thread, which essentially freezes the browser!</aside>


Dispute Bond Call API
---------------------------
```javascript
// Dispute Bond Contract Call API Examples:
var disputeBond = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";

augur.api.DisputeBond.getBondRemainingToBePaidOut({ tx: { to: disputeBond } }, function (error, bondRemainingToBePaidOut) { /* ... */ })
// example output:
bondRemainingToBePaidOut = "1100000000000000000000"

augur.api.DisputeBond.getDisputedPayoutDistributionHash({ tx: { to: disputeBond } }, function (error, disputedPayoutDistributionHash) { /* ... */ })
// example output:
disputedPayoutDistributionHash = "0xff89be2020af3cb2ca244eb862df2d350300904a96039eb53cbacf380f13f21b"

augur.api.DisputeBond.getMarket({ tx: { to: disputeBond } }, function (error, market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.DisputeBond.getOwner({ tx: { to: disputeBond } }, function (error, owner) { /* ... */ })
// example output:
owner = "0x3d62bafc1791752393384b902f813da861ddedd9"

augur.api.DisputeBond.getUniverse({ tx: { to: disputeBond } }, function (error, universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"
```
#### [Dispute Bond Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/DisputeBond.sol)

The [Dispute Bond](#dispute-bond) Token is used by [REP](#rep) holders to [Challenge](#challenge) the [Outcome](#outcome) of [Markets Awaiting Finalization](#market-awaiting-finalization). The Dispute Bond is only purchasable with REP. Only one Dispute Bond needs to be purchased for a Market per round of [Reporting](#reporting) in order for the Market to move to the next state of [Reporting](#report) in the next upcoming [Reporting Window](#reporting-window). If a Challenge is successful, which means the [Final Outcome](#final-outcome) of the [Market](#market) is something other than the [Proposed Outcome](#proposed-outcome) that was disputed, then the Bond Holder is entitled to up to 2x the Dispute Bond cost in REP. After 2x the cost of the Dispute Bond is paid to the Bond Holder any remaining REP is redistributed as normal to other Reporters who correctly staked on the Final Outcome.

#### augur.api.DisputeBond.getBondRemainingToBePaidOut({ tx }[, callback])

This transaction will return the amount of REP, denoted in attotokens, of bond remaining to be paid out for a specified `disputeBond`. Bonds are paid back to the Bond Holder if they correctly [Challenged](#challenge) a [Proposed Outcome](#proposed-outcome) and the [Final Outcome](#final-outcome) for the [Market](#market) is something different than the Challenged Proposed Outcome. Otherwise, the Bond is paid out to [Reporters](#reporter) who confirmed the Proposed Outcome to be the Final Outcome of the Market.

#### augur.api.DisputeBond.getDisputedPayoutDistributionHash({ tx }[, callback])

This transaction will return the [Payout Distribution Hash](#payout-distribution-hash) for the dispute payout given a specified `disputeBond`. The Payout Distribution Hash in this case refers the Hash that was the [Proposed Outcome](#proposed-outcome) but was [Challenged](#challenged) by the [Dispute Bond](#dispute-bond).

#### augur.api.DisputeBond.getMarket({ tx }[, callback])

This transaction will return the [Market](#market) that a specified `disputeBond` belongs to. All Dispute Bonds belong to a Market, and there can only be one Dispute Bond per [Dispute Phase](#dispute-phase) for that Market.

#### augur.api.DisputeBond.getOwner({ tx }[, callback])

This transaction will return the account address of the bond holder for a specified `disputeBond`. This is the person who owns/purchased the bond and would be the person to refund if they correctly [Challenged](#challenge) the [Proposed Outcome](#proposed-outcome) of a [Market Awaiting Finalization](#market-awaiting-finalization).

#### augur.api.DisputeBond.getUniverse({ tx }[, callback])

This transaction will return the [Universe](#universe) that contained the [Market](#market) that a specified `disputeBond` belongs to.

Market Call API
----------------
```javascript
// Market Contract Call API Examples:
var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Market.getBestGuessSecondPlaceTentativeWinningPayoutDistributionHash({ tx: { to: market } }, function (error, secondPlaceTentativeWinningPayoutDistributionHash) { /* ... */ })
// example output:
secondPlaceTentativeWinningPayoutDistributionHash = "0x20acbb1199c43cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Market.getDenominationToken({ tx: { to: market } }, function (error, denominationToken) { /* ... */ })
// example output:
denominationToken = "0x30e3852448f4ab5d62bbf7544ca3c92daca5c957"

augur.api.Market.getDesignatedReportDisputeDueTimestamp({ tx: { to: market } }, function (error, designatedReporterDisputeDueTimestamp) { /* ... */ })
// example output:
designatedReporterDisputeDueTimestamp = "1500907130"

augur.api.Market.getDesignatedReportDueTimestamp({ tx: { to: market } }, function (error, designatedReporterDueTimestamp) { /* ... */ })
// example output:
designatedReporterDueTimestamp = "1500647930"

augur.api.Market.getDesignatedReporter({ tx: { to: market } }, function (error, designatedReporter) { /* ... */ })
// example output:
designatedReporter = "0xca3edca4ed326bbcb77e914b379913b12d49654d"

augur.api.Market.getDesignatedReporterDisputeBond({ tx: { to: market } }, function (error, designatedReporterDisputeBond) { /* ... */ })
// example output:
designatedReporterDisputeBond = "0xe783e32cfeea2d2be8df1ae6e978e6d4de63fe26"

augur.api.Market.getDesignatedReportPayoutHash({ tx: { to: market } }, function (error, designatedReportPayoutHash) { /* ... */ })
// example output:
designatedReportPayoutHash = "0x58aff2018af3cb2ca3a3a1862df2d350300904a96039eb50cb0fd1ac3710aad"

augur.api.Market.getDesignatedReportReceivedTime({ tx: { to: market } }, function (error, designatedReportReceivedTime) { /* ... */ })
// example output:
designatedReportReceivedTime = "1500388800";

augur.api.Market.getEndTime({ tx: { to: market } }, function (error, endTime) { /* ... */ })
// example output:
endTime = "1500388730";

augur.api.Market.getExtraDisputeBondRemainingToBePaidOut({ tx: { to: market } }, function (error, extraDisputeBondRemainingToBePaidOut) { /* ... */ })
// example output:
extraDisputeBondRemainingToBePaidOut = "1000";

augur.api.Market.getFinalizationTime({ tx: { to: market } }, function (error, finalizationTime) { /* ... */ })
// example output:
finalizationTime = "1500647930";

augur.api.Market.getFinalPayoutDistributionHash({ tx: { to: market } }, function (error, finalPayoutDistributionHash) { /* ... */ })
// example output:
finalPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Market.getFinalWinningStakeToken({ tx: { to: market } }, function (error, winningStakeToken) { /* ... */ })
// example output:
winningStakeToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.Market.getFirstReportersDisputeBond({ tx: { to: market } }, function (error, round1ReportersDisputeBond) { /* ... */ })
// example output:
round1ReportersDisputeBond = "0x0151f9153f236359ea3605cef0fffff90aba0327";

augur.api.Market.getForkingMarket({ tx: { to: market } }, function (error, forkedMarket) { /* ... */ })
// example output:
forkedMarket = "0x0"

augur.api.Market.getLastReportersDisputeBond({ tx: { to: market } }, function (error, round2ReportersDisputeBond) { /* ... */ })
// example output:
round2ReportersDisputeBond = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";

augur.api.Market.getMarketCreatorMailbox({ tx: { to: market } }, function (error, marketCreatorMailbox) { /* ... */ })
// example output:
marketCreatorMailbox = "0xeabdeaefcfaf7ea1e17c4768a554d5780069eabd"

augur.api.Market.getMarketCreatorSettlementFeeDivisor({ tx: { to: market } }, function (error, marketCreatorSettlementFee) { /* ... */ })
// example output:
marketCreatorSettlementFee = "20000000000000000"

augur.api.Market.getOwner({ tx: { to: market } }, function (error, owner) { /* ... */ })
// example output:
owner = "0x06cbcd92af2571f1419b622a794d65db524f682b"

augur.api.Market.getNumberOfOutcomes({ tx: { to: market } }, function (error, numOutcomes) { /* ... */ })
// example output:
numOutcomes = "2"

augur.api.Market.getNumTicks({ tx: { to: market } }, function (error, numTicks) { /* ... */ })
// example output:
numTicks = "1000"

augur.api.Market.getReportingState({ tx: { to: market } }, function (error, reportingState) { /* ... */ })
// example output:
reportingState = "0";

augur.api.Market.getReportingWindow({ tx: { to: market } }, function (error, reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.Market.getShareToken({
  tx: { to: market },
  _outcome: 1
}, function (error, shareToken) { /* ... */ })
// example output:
shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054"

augur.api.Market.getStakeToken({ tx: { to: market }, _payoutNumerators: _payoutNumerators, _invalid: _invalid }, function (error, stakeToken) { /* ... */ })
// example output:
stakeToken = "0x5caa66408617f77601d0dc19c163621e7f4b8b38"

var _payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";
augur.api.Market.getStakeTokenOrZeroByPayoutDistributionHash({ tx: { to: market }, _payoutDistributionHash: _payoutDistributionHash }, function (error, stakeTokenOrZero) { /* ... */ })
// example output:
stakeTokenOrZero = "0x5caa66408617f77601d0dc19c163621e7f4b8b38"

augur.api.Market.getTentativeWinningPayoutDistributionHash({ tx: { to: market } }, function (error, tentativeWinningPayoutDistributionHash) { /* ... */ })
// example output:
tentativeWinningPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.Market.getTotalStake({ tx: { to: market } }, function (error, totalStake) { /* ... */ })
// example output:
totalStake = "1000"

augur.api.Market.getTotalWinningDisputeBondStake({ tx: { to: market } }, function (error, _totalDisputeBondStake) { /* ... */ })
// example output:
_totalDisputeBondStake = "1000"

augur.api.Market.getUniverse({ tx: { to: market } }, function (error, universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

var disputeBond = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
augur.api.Market.isContainerForDisputeBond({
  tx: { to: market },
  _shadyDisputeBond: disputeBond
}, function (error, isContainerForShareToken) { /* ... */ })
// example output:
isContainerForDisputeBond = "1"

var stakeToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
augur.api.Market.isContainerForStakeToken({
  tx: { to: market },
  _shadyStakeToken: stakeToken
}, function (error, isContainerForStakeToken) { /* ... */ })
// example output:
isContainerForStakeToken = "1"

var shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
augur.api.Market.isContainerForShareToken({
  tx: { to: market },
  _shadyShareToken: shareToken
}, function (error, isContainerForShareToken) { /* ... */ })
// example output:
isContainerForShareToken = "1"

augur.api.Market.isValid({
  tx: { to: market }
}, function (error, isValid) { /* ... */ })
// example output:
isValid = "1"
```
#### [Market Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Market.sol)

#### augur.api.Market.getBestGuessSecondPlaceTentativeWinningPayoutDistributionHash({ tx }[, callback])

Returns the second best, or second place, tentatively winning [Payout Distribution Hash](#payout-distribution-hash). The [Payout Set](#payout-set) [Reported](#report) by a [REP](#rep) holder is converted into a sha3 hash known as the Payout Distribution Hash. This method returns the Payout Set Hash with the 2nd most REP staked on it while the [Market](#market) is still [Awaiting Finalization](#market-awaiting-finalization).

#### augur.api.Market.getDenominationToken({ tx }[, callback])

Returns the address of the token used to denominate the specified `market`. A Denomination Token is the ERC20 Token used as the currency to trade on the [Outcome](#outcome) of a [Market](#market). Currently, this will always return "Cash"; however, Augur will eventually support other types of Denomination Tokens.

#### augur.api.Market.getDesignatedReportDisputeDueTimestamp({ tx }[, callback])

Returns the timestamp for when a specific `market`'s [Designated Report Phase](#designated-report-phase) and [Designated Dispute Phase](#designated-dispute-phase) should end. Can be up to 6 days after the [Market](#market)'s [End Time](#end-time), or as little as 3 days after the End Time, depends on how quickly the [Designated Reporter](#designated-reporter) [Reports](#report).

#### augur.api.Market.getDesignatedReportDueTimestamp({ tx }[, callback])

Returns the timestamp for when a specific `market`'s  [Designated Report Phase](#designated-report-phase) should be completed but doesn't count the [Designated Dispute Phase](#designated-dispute-phase). If the [Designated Reporter](#designated-reporter) has already submit their [Report](#report) for the Designated Report Phase, then this will return the timestamp for when that Report was submit to the [Market](#market).

#### augur.api.Market.getDesignatedReporter({ tx }[, callback])

Returns the address for the [Designated Reporter](#designated-reporter) set for the specified `market`. Every [Market](#market) is required to have an assigned Designated Reporter, which is set by the [Market Creator](#market-creator) during Market Creation.

#### augur.api.Market.getDesignatedReporterDisputeBond({ tx }[, callback])

Returns the [Dispute Bond](#dispute-bond) Token address for the [Designated Reporting](#designated-reporting) round for a specified `market`. When a Designated [Report](#report) is [Challenged](#challenge) a Dispute Bond is created and this function is designed to return that bond address. This returns 0 if the [Market](#market) doesn't have a Designated Report or if the Designated Report hasn't been Challenged.

#### augur.api.Market.getDesignatedReportPayoutHash({ tx }[, callback])

Returns the [Payout Distribution Hash](#payout-distribution-hash) of the [Payout Set](#payout-set) submit by the [Designated Reporter](#designated-reporter) for their [Report](#report). The Payout Set is used to determine how the [Market](#market) should [Settle](#settlement) for particular [Shares](#shares) after being [Finalized](#finalized-market).

#### augur.api.Market.getDesignatedReportReceivedTime({ tx }[, callback])

Returns the timestamp of when the [Designated Reporter](#designated-reporter)'s [Report](#report) was submit for the specified `market`. Each [Market](#market) is required to have a Designated Reporter who is expected to Report during the [Designated Report Phase](#designated-report-phase). This timestamp is used to determine if they actually submit their Report on time, which would be anytime within 3 days of the Market's [End Time](#end-time).

#### augur.api.Market.getEndTime({ tx }[, callback])

Returns the timestamp for when the specified `market`'s event has come to pass. When the [Market](#market)'s [End Time](#end-time) passes the Market either enters the [Designated Report Phase](#designated-report-phase) if a [Designated Reporter](#designated-reporter) is assigned or the Market will await the next [Reporting Window](#reporting-window) to begin [Reporting Rounds](#reporting-round).

#### augur.api.Market.getExtraDisputeBondRemainingToBePaidOut({ tx }[, callback])

Returns the amount of [REP](#rep) that is available in a [Market](#market) to be migrated to other [Universes](#universe) to reward [Dispute Bond](#dispute-bond) holders.

#### augur.api.Market.getFinalizationTime({ tx }[, callback])

Returns the timestamp for when the specified `market` was [Finalized](#finalized-market). A Finalized Market has a [Final Outcome](#final-outcome) set from successful [Market Resolution](#market-resolution) which cannot be [Challenged](#challenge) and the Market is considered [Settled](#settlement).

#### augur.api.Market.getFinalPayoutDistributionHash({ tx }[, callback])

Returns the final [Payout Distribution Hash](#payout-distribution-hash) for a specified `market`. The Final Payout Distribution Hash determines how a [Finalized Market](#finalized-market) payouts out for a specific [Share](#shares) of an [Outcome](#outcome).

#### augur.api.Market.getFinalWinningStakeToken({ tx }[, callback])

Returns the Stake Token address for the [Final Outcome](#final-outcome) of a specific `market`. If the [Market](#market) isn't [Settled](#market-settlement) then this will return a null address, `0x0`.

#### augur.api.Market.getFirstReportersDisputeBond({ tx }[, callback])

Returns the [Dispute Bond](#dispute-bond) Token address for the [First Report Round](#first-report-round) for a specified `market`. When a First Report Round [Report](#report) is [Challenged](#challenge) a Dispute Bond is created and this function is designed to return that bond address. This returns 0 if the [Market](#market) doesn't have a First Round Report or if the First Round Report hasn't been Challenged.

#### augur.api.Market.getForkingMarket({ tx }[, callback])

Returns the [Market](#market) address of the [Forked Market](#forked-market) for the [Universe](#universe) that contains the specified `market` address. If the `market` address specified belongs in a Universe that hasn't had a [Fork](#fork) take place, this will return `0x0`.

#### augur.api.Market.getLastReportersDisputeBond({ tx }[, callback])

Returns the [Last Report Round](#last-report-round)'s [Dispute Bond](#dispute-bond) address for the `market`. If this [Market](#market) hasn't gone through a Last Report Round or there was no [Challenge](#challenge) to the [Proposed Outcome](#proposed-outcome) of the Last Report Round then this will return an empty address `0x0`.

#### augur.api.Market.getMarketCreatorMailbox({ tx }[, callback])

Returns the address of the [Market Creator Mailbox](#market-creator-mailbox) for `market`.

#### augur.api.Market.getMarketCreatorSettlementFeeDivisor({ tx }[, callback])

Returns the [Creator Fee](#creator-fee) set by the [Market Creator](#market-creator), denominated in attotokens per [settlement](#settlement) of a [Complete Set](#complete-set), for the `market`.

#### augur.api.Market.getNumberOfOutcomes({ tx }[, callback])

Returns the number of [Outcomes](#outcome) for a specified `market`. The number of outcomes is the number of potential results of the [Market](#market).

#### augur.api.Market.getNumTicks({ tx }[, callback])

Returns the [Number of Ticks](#number-of-ticks) set for a specific `market`. The Number of [Ticks](#ticks) represents the number of valid price points between the [Market](#market)'s [Minimum Price](#minimum-display-price) and [Maximum Price](#maximum-display-price).

#### augur.api.Market.getOwner({ tx }[, callback])

Returns the address of the specified [Market's](#market) owner.

#### augur.api.Market.getPayoutDistributionHashStake({ tx, \_payoutDistributionHash }[, callback])

Returns the staked amount of [REP](#rep), in attorep, for a specified `_payoutDistributionHash` of a `market`. [Reporters](#reporter) who stake REP on the same [Outcome](#outcome) during the [Reporting Phase](#reporting-phase) will have identical [Payout Distribution Hashs](#payout-distribution-hash). This method returns the total amount of staked REP for a specific Payout Distribution Hash. The Hash with the most REP staked on [it is considered the Proposed Outcome](#proposed-outcome) while the [Market](#market) is [Awaiting Finalization](#awaiting-finalization). If the [Dispute Phase](#dispute-phase) passes without a [Challenge](#challenge) to the Proposed Outcome then the Market is [Finalized](#finalized-market) and the Proposed Outcome becomes the [Final Outcome](#final-outcome).

#### augur.api.Market.getReportingState({ tx }[, callback])

Returns the current [Reporting](#report) State that the `market` is in. This method returns a number between 0 and 12, which corresponds to the various Reporting States outlined in the [Reporting Section](#reporting) of the documentation.

#### augur.api.Market.getReportingWindow({ tx }[, callback])

Returns the [Reporting Window](#reporting-window) address for the specified `market`. This returns the Reporting Window when this [Market](#market) is scheduled to be [Reported](#report) on for [Reporting Rounds](#reporting-round) if a [Designated Reporter](#designated-reporter) fails to Report or their Report is [Challenged](#challenge).

#### augur.api.Market.getShareToken({ tx, \_outcome }[, callback])

Returns the [Share](#shares) Token's address for the specified `market` and `_outcome`. Every [Outcome](#outcome) of a [Market](#market) has a separate Share Token used to handle trading around that Outcome and this method returns the contract address of the Share Tokens for the specified Outcome.

#### augur.api.Market.getStakeToken({ tx, \_payoutNumerators, \_invalid }[, callback])

Returns the Stake Token address for `_payoutNumerators` and `_invalid` on the `market` specified. When a [Reporter](#reporter) submits a [Report](#report), they submit a [Payout Set](#payout-set) (`_payoutNumerators`) array indicating how the [Market](#market) should payout. Each Payout Set has a Stake Token associated with it, so if two different Reporters submit the same Payout Set as their Report then they will receive the same Stake Token currency. This method will return the Stake Token associated with a specific Payout Set.

#### augur.api.Market.getStakeTokenOrZeroByPayoutDistributionHash({ tx, \_payoutDistributionHash }[, callback])

Returns a Stake Token address, or 0 if there is no Stake Token for a specific `_payoutDistributionHash` for a `market`. [Payout Sets](#payout-set) that are valid, that is that they sum of the values in the array equal the [Number of Ticks](#number-of-ticks) for the [Market](#market) and no individual value in the array is greater than the Number of [Ticks](#ticks), become [Payout Distribution Hashes](#payout-distribution-hash). This method returns the Stake Token given a Payout Distribution Hash, much like `getStakeToken` returns the Stake Token given a Payout Set.

#### augur.api.Market.getTentativeWinningPayoutDistributionHash({ tx }[, callback])

Returns the tentatively winning [Payout Distribution Hash](#payout-distribution-hash) given a specified `market`. The tentatively winning Payout Distribution Hash is the hash with the most [REP](#rep) staked on it prior to [Market Finalization](#finalized-market). Once the [Market](#market) Finalized the tentatively winning Payout Distribution Hash becomes the Winning Payout Distribution Hash.

#### augur.api.Market.getTotalStake({ tx }[, callback])

Returns the total amount staked on the specified [Market](#market).

#### augur.api.Market.getTotalWinningDisputeBondStake({ tx }[, callback])

Returns the sum of all [Dispute Bonds](#dispute-bond) staked on challenging a [Payout Distribution Hash](#payout-distribution-hash) other than the final Payout Distribution Hash for the specfied [Market](#market).

#### augur.api.Market.getUniverse({ tx }[, callback])

Returns the [Universe](#universe) address of the Universe that the specified `market` is contained within. All [Markets](#market) are created on a specific Universe and new Universes can be created in the event of a [Fork](#fork).

#### augur.api.Market.isContainerForDisputeBond({ tx, \_shadyDisputeBond }[, callback])

Returns whether the specific `market` is a container for the `_shadyDisputeBond` address provided. A `_shadyDisputeBond` address will return true if it is a [Designated Report](#designated-reporter) [Dispute Bond](#dispute-bond) token, or a [Reporting Round](#reporting-round) Dispute Bond for this [Market](#market). Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForStakeToken({ tx, \_shadyStakeToken }[, callback])

Returns whether the specific `market` is a container for the `_shadyStakeToken` address provided. The `_shadyStakeToken` address will return true if the address is a Stake Token that belongs to the [Market](#market), otherwise false will be returned. Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForShareToken({ tx, \_shadyShareToken }[, callback])

Returns whether the specific `market` is a container for the `_shadyShareToken` address provided. The `_shadyShareToken` address will return true if it's a [Share](#shares) Token belonging to the [Market](#market), otherwise it will return false if the address is not a Share Token belonging to this Market. Returns `1` if true, `0` if false.

#### augur.api.Market.isValid({ tx }[, callback])

Returns True or False depending on if the [Market](#market) passed as `market` has been [Finalized](#finalized-market) as Invalid. This means the Market is unable to be finalized to a single [Final Outcome](#final-outcome) due to an unclear Market or an indeterminate [Outcome](#outcome). Returns `1` for True in the event that the Market is valid, `0` is False, the Market is invalid.

Orders Call API
---------------
```javascript
// Orders Contract Call API Examples:
var orders = "0x8868ff3e9ce1c0459b309fac6dd4e69229b91a56";
var _orderId = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
var _type = "1";
var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var _outcome = "1";
var _fxpPrice = "450000000000000000"; // 0.45

augur.api.Orders.assertIsNotBetterPrice({
  tx: { to: orders },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _betterOrderId: _orderId
}, function (error, isNotBetterPrice) { /* ... */ })
// example output:
isNotBetterPrice = "1"

augur.api.Orders.assertIsNotWorsePrice({
  tx: { to: orders },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _worstOrderId: _orderId
}, function (error, isNotWorstPrice) { /* ... */ })
// example output:
isNotWorstPrice = "0"

augur.api.Orders.getAmount({ 
  tx: { to: orders }, 
  _orderId: _orderId 
}, function (error, amount) { /* ... */ })
// example output:
amount = "15"

augur.api.Orders.getBestOrderId({
  tx: { to: orders },
  _type: _type,
  _market: _market,
  _outcome: _outcome
}, function (error, bestOrderID) { /* ... */ })
// example output:
bestOrderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

var secondBestOrderID = "0x49cb49f610b5f6e31ee163a8ad65f964af1088e38c8a1b07f1218177b5e006b5";
augur.api.Orders.getBetterOrderId({ 
  tx: { to: orders },
  _orderId: secondBestOrderID
}, function (error, betterOrderID) { /* ... */ })
// example output:
betterOrderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

augur.api.Orders.getLastOutcomePrice({
  tx: { to: orders },
  _market: _market,
  _outcome: _outcome
}, function (error, lastOutcomePrice) { /* ... */ })
// example output:
lastOutcomePrice = "490000000000000000"

augur.api.Orders.getMarket({ 
  tx: { to: orders },
  _orderId: _orderId
}, function (error, orderMarket) { /* ... */ })
// example output:
orderMarket = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Orders.getOrderCreator({ 
  tx: { to: orders },
  _orderId: _orderId 
}, function (error, owner) { /* ... */ })
// example output:
owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";

augur.api.Orders.getOrderMoneyEscrowed({ 
  tx: { to: orders },
  _orderId: _orderId 
}, function (error, orderMoneyEscrowed) { /* ... */ })
// example output:
orderMoneyEscrowed = "5000000000000000000"

augur.api.Orders.getOrderSharesEscrowed({ 
  tx: { to: orders },
  _orderId: _orderId
}, function (error, orderSharesEscrowed) { /* ... */ })
// example output:
orderSharesEscrowed = "0";

augur.api.Orders.getOrderType({ 
  tx: { to: orders },
  _orderId: _orderId 
}, function (error, type) { /* ... */ })
// example output:
type = "1";

augur.api.Orders.getOutcome({ 
  tx: { to: orders },
  _orderId: _orderId 
}, function (error, orderOutcome) { /* ... */ })
// example output:
orderOutcome = "1";

augur.api.Orders.getPrice({ 
  tx: { to: orders },
  _orderId: _orderId 
}, function (error, price) { /* ... */ })
// example output:
price = "500000000000000000";

augur.api.Orders.getVolume({ 
  tx: { to: orders },
  _market: _market 
}, function (error, volume) { /* ... */ })
// example output:
volume = "100000000000000000000000";

var secondWorstOrderID = "0x4b538f4de2517f7d7bbb227161981c51c40bf725da9941b3dc02e6c14cafd1f1";
augur.api.Orders.getWorseOrderId({ 
  tx: { to: orders },
  _orderId: secondWorstOrderID 
}, function (error, worseOrderID) { /* ... */ })
// example output:
worseOrderID = "0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api.Orders.getWorstOrderId({
  tx: { to: orders },
  _type: _type,
  _market: _market,
  _outcome: _outcome
}, function (error, worstOrderID) { /* ... */ })
// example output:
worstOrderID = "0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api.Orders.isBetterPrice({
  tx: { to: orders },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _orderId: _orderId
}, function (error, isBetterPrice) { /* ... */ })
// example output:
isBetterPrice = "0"

augur.api.Orders.isWorsePrice({
  tx: { to: orders },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _orderId: _orderId
}, function (error, isWorsePrice) { /* ... */ })
// example output:
isWorsePrice = "1"
```
#### [Orders Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/Orders.sol)

#### augur.api.Orders.assertIsNotBetterPrice({ tx, \_type, \_fxpPrice, \_betterOrderId }[, callback])

Returns whether the specified `_fxpPrice` is not a better price than the `_betterOrderId` for a given order `_type`. Returns `1` if true, `0` if false.

#### augur.api.Orders.assertIsNotWorsePrice({ tx, \_type, \_fxpPrice, \_worstOrderId }[, callback])

Returns whether the specified `_fxpPrice` is not a worst price than the `_worstOrderId` for a given order `_type`. Returns `1` if true, `0` if false.

#### augur.api.Orders.getAmount({ tx, \_orderId }[, callback])

Returns the amount of [Shares](#shares) requested on a specified `_orderId`.

#### augur.api.Orders.getBestOrderId({ tx, \_type, \_market, \_outcome }[, callback])

Returns the order ID of the best order on the `_market` of `_type` trading on the provided `_outcome`.

#### augur.api.Orders.getBestOrderWorstOrderHash({ tx, \_market, \_outcome, \_type }[, callback])

Returns a sha256 order hash containing the inputted params `_market`, `_outcome`, and `_type`.

#### augur.api.Orders.getBetterOrderId({ tx, \_orderId }[, callback])

Returns a better order ID than the `_orderId`.

#### augur.api.Orders.getLastOutcomePrice({ tx, \_market, \_outcome }[, callback])

Returns the fixed point value of the last price traded for a specified `_market` and `_outcome`.

#### augur.api.Orders.getMarket({ tx, \_orderId }[, callback])

Returns the [Market](#market) address for the specified `_orderId`.

#### augur.api.Orders.getOrderCreator({ tx, \_orderId }[, callback])

Returns the [Creator](#order-creator) address of the specified `_orderId`.

#### augur.api.Orders.getOrderMoneyEscrowed({ tx, \_orderId }[, callback])

Returns the fixed point value of the amount of money escrowed by the [Creator](#order-creator) for a specified `_orderId`.

#### augur.api.Orders.getOrderSharesEscrowed({ tx, \_orderId }[, callback])

Returns the fixed point value of the amount of shares escrowed by the [Creator](#order-creator) for a specified `_orderId`.

#### augur.api.Orders.getOrderType({ tx, \_orderId }[, callback])

Returns the trade type, `1` for bid and `2` for ask, for a specified `_orderId`.

#### augur.api.Orders.getOutcome({ tx, \_orderId }[, callback])

Returns the [Outcome](#outcome) being traded on for the specified `_orderId`.

#### augur.api.Orders.getPrice({ tx, \_orderId }[, callback])

Returns the fixed point value of the price of a specified `_orderId`.

#### augur.api.Orders.getVolume({ tx, \_market }[, callback])

Returns the fixed point value of the volume of a specified `_market`.

#### augur.api.Orders.getWorseOrderId({ tx, \_orderId }[, callback])

Returns a worse order ID than the `_orderId`.

#### augur.api.Orders.getWorstOrderId({ tx, \_type, \_market, \_outcome }[, callback])

Returns the order ID of the worst order on the `_market` of `_type` trading on the provided `_outcome`.

#### augur.api.Orders.isBetterPrice({ tx, \_type, \_fxpPrice, \_orderId }[, callback])

Returns whether the specified `_fxpPrice` is a better price than the `_orderId` for a given order `_type`. Returns `1` if true, `0` if false.

#### augur.api.Orders.isWorsePrice({ tx, \_type, \_fxpPrice, \_orderId }[, callback])

Returns whether the specified `_fxpPrice` is a worst price than the `_orderId` for a given order `_type`. Returns `1` if true, `0` if false.

Orders Fetcher Call API
-----------------------
```javascript
// Orders Fetcher Contract Call API Examples:
var ordersFetcher = "0x7768ff3e9ce1c0459b309fac6dd4e69229b91a77";
var _orderId = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
var _type = "1";
var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var _outcome = "1";
var _fxpPrice = "450000000000000000"; // 0.45

augur.api.OrdersFetcher.ascendOrderList({
  tx: { to: ordersFetcher },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _lowestOrderId: _orderId
}, function (error, ascendingOrderList) { /* ... */ })
// example output:
ascendingOrderList = [
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870",
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0"]

augur.api.OrdersFetcher.descendOrderList({
  tx: { to: ordersFetcher },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _highestOrderId: _orderId
}, function (error, decendingOrderList) { /* ... */ })
// example output:
decendingOrderList = [
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e",
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"]

augur.api.OrdersFetcher.findBoundingOrders({
  tx: { to: ordersFetcher },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _bestOrderId: _orderId,
  _worstOrderId: 0,
  _betterOrderId: 0,
  _worseOrderId: 0
}, function (error, boundingOrders) { /* ... */ })
// example output:
boundingOrders = [
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0",
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e"]
```
#### [Orders Fetcher Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/OrdersFetcher.sol)

#### augur.api.OrdersFetcher.ascendOrderList({ tx, \_type, \_fxpPrice, \_lowestOrderId }[, callback])

Returns an array containing the order IDs that should be the better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_lowestOrderId` is an order ID expected to be a worse price than the `_fxpPrice` specified for an order of `_type`.

#### augur.api.OrdersFetcher.descendOrderList({ tx, \_type, \_fxpPrice, \_highestOrderId }[, callback])

Returns an array containing the order IDs that should be set to better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_highestOrderId` is an order ID expected to be a better price than the `_fxpPrice` specified for an order of `_type`.

#### augur.api.OrdersFetcher.findBoundingOrders({ tx, \_type, \_fxpPrice, \_bestOrderId, \_worstOrderId, \_betterOrderId, \_worseOrderId }[, callback])

Returns an array containing the order IDs that should be set to better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_betterOrderId` and `_worseOrderId` should be orders that are better or worse than the `_fxpPrice` for an order of `_type`. `_bestOrderId` and `_worstOrderId` should be the best and worst order IDs on the order book for the specified `_type`.

Participation Token Call API
--------------------
```javascript
// Participation Token Contract Call API Examples:
var participationToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";

augur.api.ParticipationToken.getReportingWindow({ tx: { to: participationToken } }, function (error, reportingWindow) { /* ... */ })
// example output:
reportingWindow =  "0x1f90cc6b4e89303e451c9b852827b5791667f570";
```
#### [Participation Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/ParticipationToken.sol)

The Participation Token is an ERC-20 token that implements all of the required functions listed in the [ERC-20 Token Standard] (https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md). It does not, however, implement the optional functions.

#### augur.api.ParticipationToken.getReportingWindow({ tx }[, callback])

Returns the [Reporting Window](#reporting-window) address for the specified `participationToken`. All Stake Tokens belong to a specific [Market](#market) and all Market's belong to a specific Reporting Window. This method will return the Reporting Window that contains the Market that these Participation Tokens were purchased for.

Reporting Window Call API
-------------------------
```javascript
// Reporting Window Contract Call API Examples:
var reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.ReportingWindow.allMarketsFinalized({ tx: { to: reportingWindow } }, function (error, allMarketsFinalized) { /* ... */ })
// example output:
allMarketsFinalized = "1"

augur.api.ReportingWindow.getAvgReportingGasPrice({ tx: { to: reportingWindow } }, function (error, avgReportingGasPrice) { /* ... */ })
// example output:
avgReportingGasPrice = "340000"

augur.api.ReportingWindow.getDisputeEndTime({ tx: { to: reportingWindow } }, function (error, disputeEndTime) { /* ... */ })
// example output:
disputeEndTime = "1500907130"

augur.api.ReportingWindow.getDisputeStartTime({ tx: { to: reportingWindow } }, function (error, disputeStartTime) { /* ... */ })
// example output:
disputeStartTime = "1500647930"

augur.api.ReportingWindow.getEndTime({ tx: { to: reportingWindow } }, function (error, endTime) { /* ... */ })
// example output:
endTime = "1500388730"

augur.api.ReportingWindow.getFirstReporterMarketsCount({ tx: { to: reportingWindow } }, function (error, firstReporterMarketsCount) { /* ... */ })
// example output:
firstReporterMarketsCount = "54321"

augur.api.ReportingWindow.getLastReporterMarketsCount({ tx: { to: reportingWindow } }, function (error, lastReporterMarketsCount) { /* ... */ })
// example output:
lastReporterMarketsCount = "12345"

augur.api.ReportingWindow.getMarketsCount({ tx: { to: reportingWindow } }, function (error, marketsCount) { /* ... */ })
// example output:
marketsCount = "65"

augur.api.ReportingWindow.getNextReportingWindow({ tx: { to: reportingWindow } }, function (error, nextReportingWindow) { /* ... */ })
// example output:
nextReportingWindow = "0x0d6b5a54f940bf3d52e438cab785981aaefdf40c"

augur.api.ReportingWindow.getNumDesignatedReportNoShows({ tx: { to: reportingWindow } }, function (error, numDesignatedReportNoShows) { /* ... */ })
// example output:
numDesignatedReportNoShows = "2"

augur.api.ReportingWindow.getNumIncorrectDesignatedReportMarkets({ tx: { to: reportingWindow } }, function (error, numIncorrectDesignatedReportMarkets) { /* ... */ })
// example output:
numIncorrectDesignatedReportMarkets = "1"

augur.api.ReportingWindow.getNumInvalidMarkets({ tx: { to: reportingWindow } }, function (error, numInvalidMarkets) { /* ... */ })
// example output:
numInvalidMarkets = "3"

augur.api.ReportingWindow.getNumMarkets({ tx: { to: reportingWindow } }, function (error, numMarkets) { /* ... */ })
// example output:
numMarkets = "65"

augur.api.ReportingWindow.getParticipationToken({ tx: { to: reportingWindow } }, function (error, ) { /* ... */ })
// example output:
= "0x3d1db1cac3153879b1c190aeb9bb7292f09ad83e"

augur.api.ReportingWindow.getPreviousReportingWindow({ tx: { to: reportingWindow } }, function (error, ) { /* ... */ })
// example output:
= "0x3d1db1cac3153879b1c190aeb9bb7292f09ad83e"

augur.api.ReportingWindow.getReportingEndTime({ tx: { to: reportingWindow } }, function (error, reportingEndTime) { /* ... */ })
// example output:
reportingEndTime = "1500647900"

augur.api.ReportingWindow.getReportingStartTime({ tx: { to: reportingWindow } }, function (error, reportingStartTime) { /* ... */ })
// example output:
reportingStartTime = "14998315100"

augur.api.ReportingWindow.getReputationToken({ tx: { to: reportingWindow } }, function (error, reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.ReportingWindow.getStartTime({ tx: { to: reportingWindow } }, function (error, startTime) { /* ... */ })
// example output:
startTime = "14995895900"

augur.api.ReportingWindow.getTotalStake({ tx: { to: reportingWindow } }, function (error, totalStake) { /* ... */ })
// example output:
totalStake = "18900512367"

augur.api.ReportingWindow.getTotalWinningStake({ tx: { to: reportingWindow } }, function (error, totalStake) { /* ... */ })
// example output:
totalStake = "29105"

augur.api.ReportingWindow.getUniverse({ tx: { to: reportingWindow } }, function (error, universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReportingWindow.isActive({ tx: { to: reportingWindow } }, function (error, isActive) { /* ... */ })
// example output:
isActive = "1"

var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.ReportingWindow.isContainerForMarket({
  tx: { to: reportingWindow },
  _shadyMarket: _market
}, function (error, isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1"

augur.api.ReportingWindow.isDisputeActive({ tx: { to: reportingWindow } }, function (error, isDisputeActive) { /* ... */ })
// example output:
isDisputeActive = "1"

augur.api.ReportingWindow.isForkingMarketFinalized({ tx: { to: reportingWindow } }, function (error, isForkingMarketFinalized) { /* ... */ })
// example output:
isForkingMarketFinalized = "0";

augur.api.ReportingWindow.isOver({ tx: { to: reportingWindow } }, function (error, isOver) { /* ... */ })
// example output:
isOver = "0";

augur.api.ReportingWindow.isReportingActive({ tx: { to: reportingWindow } }, function (error, isReportingActive) { /* ... */ })
// example output:
isReportingActive = "1"
```
#### [Reporting Window Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/ReportingWindow.sol)

#### augur.api.ReportingWindow.allMarketsFinalized({ tx }[, callback])

Returns true if all [Markets](#market) in the specified [Reporting Window](#reporting-window) are [Finalized Markets](#finalized-market). Otherwise, returns false.

#### augur.api.ReportingWindow.getAvgReportingGasPrice({ tx }[, callback ])

Returns the average amount of Gas spent per [Report](#report) submit during the [Reporting Window](#reporting-window).

#### augur.api.ReportingWindow.getDisputeEndTime({ tx }[, callback])

Every [Reporting Window](#reporting-window) consists of two phases, the [Reporting Phase](#reporting-phase) and the [Dispute Phase](#dispute-phase). This method is used to return the timestamp for the end of the Reporting Window's Dispute Phase.

#### augur.api.ReportingWindow.getDisputeStartTime({ tx }[, callback])

As stated above, [Reporting Windows](#reporting-window) have two phases, the [Reporting Phase](#reporting-phase) and [Dispute Phase](#dispute-phase) and this method returns the timestamp of the start of the Dispute Phase.

#### augur.api.ReportingWindow.getEndTime({ tx }[, callback])

Returns a timestamp for when this [Reporting Window](#reporting-window) will end. A Reporting Window is considered active for a total of 30 days, then ends, and is no longer considered to be active.

#### augur.api.ReportingWindow.getFirstReporterMarketsCount({ tx }[, callback])

Returns the number of [Markets](#market) in the specified [Reporting Window](#reporting-window) that are in the [First Report Round](#first-report-round). NOTE: If the Reporting Window has not begun yet, this number will include Markets that may end up being [Finalized](#finalized-market) in the [Designated Report Round](#designated-report-phase). Since it is unknown whether [Designated Reporters](#designated-reporter) ultimately will submit a report, Augur assumes by default that all new Markets will go to the First Report Round. Once the Reporting Window has begun, this function will return the number of Markets that are actually in the First Report Round.

#### augur.api.ReportingWindow.getLastReporterMarketsCount({ tx }[, callback])

Returns the number of [Markets](#market) in the specified [Reporting Window](#reporting-window) that are in the [Last Report Round](#last-report-round).

#### augur.api.ReportingWindow.getMarketsCount({ tx }[, callback])

Returns the total number of [Markets](#market) that belong to the [Reporting Window](#reporting-window). This includes [Reporting Round](#reporting-round) Markets.

#### augur.api.ReportingWindow.getNextReportingWindow({ tx }[, callback])

Returns the next [Reporting Window](#reporting-window)'s Contract Address. Reporting Windows last 30 days and continually occur, one after the other. Only one Reporting Window can be active at a time in a [Universe](#universe), and every Reporting Window belongs to a Universe.

#### augur.api.ReportingWindow.getNumDesignatedReportNoShows({ tx }[, callback])

Returns the number of [Markets](#market) belonging to this [Reporting Window](#reporting-window) in which the [Designated Reporter](#designated-reporter) failed to [Report](#report) during the [Designated Report Phase](#designated-report-phase). These Markets will have a [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) and [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) up for grabs for the [First Reporter](#first-reporter) because these Markets have yet to receive a Report. This only includes Markets where Designated Reporters failed to Report, and does not include Markets where the Designated Reporter's [Proposed Outcome](#proposed-outcome) was [Challenged](#challenge).

#### augur.api.ReportingWindow.getNumIncorrectDesignatedReportMarkets({ tx }[, callback])

Returns the number of [Markets](#market) belonging to this [Reporting Window](#reporting-window) in which [Designated Reporter's](#designated-reporter) [Proposed Outcome](#proposed-outcome) was [Challenged](#challenge) during the [Designated Dispute Phase](#designated-dispute-phase). This is only the count of Markets where Designated Reporters did [Report](#report) during the [Designated Report Phase](#designated-report-phase) but the Market didn't get [Finalized](#finalized-market). This doesn't include Markets where the Designated Reporter failed to submit a Report.

#### augur.api.ReportingWindow.getNumInvalidMarkets({ tx }[, callback])

Returns the number of [Markets](#market) that were [Reported](#report) to be Invalid during a [Reporting Window](#reporting-window). Invalid Markets are Markets that aren't clearly defined or doesn't fit one of the [Outcomes](#outcome) set for this Market. [Reporters](#reporter) are encouraged to Report the Market as Invalid if they can't confidently stake their [REP](#rep) into a single Outcome for the Market.

#### augur.api.ReportingWindow.getNumMarkets({ tx }[, callback])

Returns the total number of [Markets](#market) that belong to the [Reporting Window](#reporting-window). This includes [Reporting Round](#reporting-round) Markets.

#### augur.api.ReportingWindow.getParticipationToken({ tx }[, callback])

Returns the Contract Address of the [Participation Token](#participation-token) associated with the specified [Reporting Window](#reporting-window).

#### augur.api.ReportingWindow.getPreviousReportingWindow({ tx }[, callback])

Returns the previous [Reporting Window](#reporting-window)'s Contract Address. Reporting Windows last 30 days and continually occur, one after the other. Only one Reporting Window can be active at a time in a [Universe](#universe), and every Reporting Window belongs to a Universe.

#### augur.api.ReportingWindow.getReportingEndTime({ tx }[, callback])

Returns a timestamp of when the [Reporting Phase](#reporting-phase) of a specific [Reporting Window](#reporting-window) will be ending. Reporting Windows are 30 days long and split into two Phases, the Reporting Phase and the [Dispute Phase](#dispute-phase). The Reporting Phase lasts 27 days, the Dispute Phase lasts 3 days.

#### augur.api.ReportingWindow.getReportingStartTime({ tx }[, callback])

Returns a timestamp of when the [Reporting Phase](#reporting-phase) of a specific [Reporting Window](#reporting-window) will be starting. Reporting Windows are 30 days long and split into two Phases, the Reporting Phase and the [Dispute Phase](#dispute-phase). The Reporting Phase lasts 27 days, the Dispute Phase lasts 3 days.

#### augur.api.ReportingWindow.getReputationToken({ tx }[, callback])

Returns the [Reputation Token (REP)](#rep) address for the specified [Reporting Window](#reporting-window). Every Reporting Window has a [Reporting Phase](#reporting-phase) where [Reporters](#reporter) submit [Reports](#report) on the [Outcomes](#outcome) of [Markets](#market). In order to Report, Reporters need to stake REP. A Reporting Window only accepts one REP contract as the source of staked REP and this method returns that contract's address.

#### augur.api.ReportingWindow.getStartTime({ tx }[, callback])

Returns a timestamp of when a [Reporting Window](#reporting-window) becomes active and starts. A Reporting Window is considered active for a total of 30 days, then ends, and is no longer considered to be active. Only active Reporting Windows allow [Reporters](#reporter) to [Report](#report) on the [Outcomes](#outcome) of the [Markets](#market) contained in the Reporting Window.

#### augur.api.ReportingWindow.getTotalStake({ tx }[, callback])

Returns the total amount staked across all [Markets](#market) in the specified [Reporting Window](#reporting-window).

#### augur.api.ReportingWindow.getTotalWinningStake({ tx }[, callback])

Returns the total amount [Participation Tokens](#participation-token) purchased in the specified [Reporting Window](#reporting-window). NOTE: Participation Tokens are only purchasable in the event that no [Market](#market) in a Reporting Window can be reported on.

#### augur.api.ReportingWindow.getUniverse({ tx }[, callback])

Returns the [Universe](#universe) address that the [Reporting Window](#reporting-window) belongs to. Every Reporting Window belongs to a specific Universe in which they were created and operate within.

#### augur.api.ReportingWindow.isActive({ tx }[, callback])

This method returns whether the [Reporting Window](#reporting-window) is currently active or not. Reporting Windows are considered active during the Window's [Reporting Phase](#reporting-phase) and [Dispute Phase](#dispute-phase), which last a total of 30 days. Returns `1` if the `reportingWindow` is active, `0` if not.

#### augur.api.ReportingWindow.isContainerForMarket({ tx, \_shadyMarket }[, callback])

Returns whether the `_shadyMarket` address provided is a [Market](#market) that is set to be [Reported](#report) on during the [Reporting Window](#reporting-window). Markets are assigned a Reporting Window that is the first Reporting Window following the Market's [End Time](#end-time). Returns `1` if the Market belongs to the `reportingWindow`, `0` if not.

#### augur.api.ReportingWindow.isDisputeActive({ tx }[, callback])

Returns whether the [Reporting Window](#reporting-window) is currently in it's [Dispute Phase](#dispute-phase) or not. The Dispute Phase is a 3 day long period that follows the Reporting Window's [Reporting Phase](#reporting-phase), which lasts 27 days. Returns `1` if the Reporting Window's Dispute Phase is active, `0` if not.

#### augur.api.ReportingWindow.isForkingMarketFinalized({ tx }[, callback])

Returns whether the [Forked Market](#forked-market) that caused this [Reporting Window](#reporting-window)'s [Universe](#universe) to be created has been [Finalized](#finalized-market) or not. Every Reporting Window belongs to a Universe and all Universes, except for the first Universe, are created because of a [Fork](#fork). Returns `1` if the Forked Market is Finalized, `0` it not.

#### augur.api.ReportingWindow.isOver({ tx }[, callback])

Returns whether the 30-day reporting period for the specified [Reporting Window](#reporting-window) has ended.

#### augur.api.ReportingWindow.isReportingActive({ tx }[, callback])

Returns whether the [Reporting Window](#reporting-window) is currently in it's [Reporting Phase](#reporting-phase) or not. The Reporting Phase lasts 27 days at the start of a Reporting Window and is followed by a 3 day [Dispute Phase](#dispute-phase). Returns `1` if the Reporting Window's Reporting Phase is active, `0` if not.

Reputation Token Call API
-------------------------
```javascript
// Reputation Token Contract Call API Examples:
var reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";

augur.api.ReputationToken.getUniverse({ tx: { to: reputationToken } }, function (error, universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReputationToken.getTopMigrationDestination({ tx: { to: reputationToken } }, function (error, topMigrationDestination) { /* ... */ })
// example output:
topMigrationDestination = "0x1aa30942000ac72dee6580e1ac32d1d01ac1af00"
```
#### [Reputation Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/ReputationToken.sol)

The Reputation Token, known as [REP](#rep), is the key that allows Augur's Decentralized Oracle System to function, and by extension the entirety of Augur. REP has three major functions, it's used to [Report](#report) on the [Outcome](#outcome) of a [Market](#market), [Challenge](#challenge) the [Proposed Outcome](#proposed-outcome) of a Market, and as a bond when creating a Market. [Reporters](#reporter) stake REP on the Outcome of a Market as a show of confidence in their Report. If the Reporter correctly staked on the [Final Outcome](#final-outcome) of the Market they can claim their REP back, earn [Reporting Fees](#reporting-fee) proportional to their staked REP, and a portion of the REP incorrectly staked on other Outcomes.

REP is also used to Challenge the Proposed Outcome of Reports during the [Designated Dispute Phase](#designated-dispute-phase) or the [Dispute Phase](#dispute-phase). If the Challenge successfully changes the Proposed Outcome of a Market and that outcome becomes the Final Outcome, the [Dispute Bond](#dispute-bond) holder can redeem the bond for up to double the REP it cost to place. When creating a Market, the Market Creator is required to specify a Designated Reporter and pay a [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) to ensure the Designated Reporter shows up. If the Reporter doesn't show up, the Market Creator's Designated Report No-Show REP Bond will go to the First Reporter to Report on the Market. Their GAS cost for the Report transaction will be covered by the Market Creator's [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) and the Designated Report No-Show REP Bond is added to whatever the First Reporter staked, there by improving her stake and potential rewards if correctly staked.

The Reputation Token is an ERC-20 token that implements all of the required functions listed in the [ERC-20 Token Standard] (https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md). It does not, however, implement the optional functions.

#### augur.api.ReputationToken.getUniverse({ tx }[, callback])

Returns the [Universe](#universe) address for the [REP](#rep) address provided. All of Augur's information belongs to a Universe and each Universe has it's own REP contract. New Universes are created in the event of a [Fork](#fork).

#### augur.api.ReputationToken.getTopMigrationDestination({ tx }[, callback])

As mentioned in the previous method description, all of Augur's [Universes](#universe) have their own [REP](#rep) contract, specific to that Universe. In the event of a [Fork](#fork) occurring, REP holders are expected to migrate their REP to one of the newly created [Child Universes](#child-universe). The Child Universe with the most REP migrated to it at the end of the 60 day [Fork Period](#fork-period) will be the only Universe that allows for [Settlement](#settlement) on the [Forked Market](#forked-market) and will be the Universe that all pending [Markets](#market) will migrate to for [Reporting](#report). What this method does is returns the Universe address of the currently "winning" migration destination. In other words, this returns the Universe with the most REP migrated to it so far.

Share Token Call API
--------------------
```javascript
// Share Token Contract Call API Examples:
var shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";

augur.api.ShareToken.getMarket({ tx: { to: shareToken } }, function (error, market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.ShareToken.getOutcome({ tx: { to: shareToken } }, function (error, outcome) { /* ... */ })
// example output:
outcome = "1"

```
#### [Share Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/ShareToken.sol)

The Share Token is an ERC-20 token that implements all of the required functions listed in the [ERC-20 Token Standard] (https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md). It does not, however, implement the optional functions.

#### augur.api.ShareToken.getMarket({ tx }[, callback])

Returns the market address for the specified `shareToken`.

#### augur.api.ShareToken.getOutcome({ tx }[, callback])

Returns the Outcome of the market that the specified `shareToken` is for.

Stake Token Call API
------------------------
```javascript
// Stake Token Contract Call API Examples:
var stakeToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";

augur.api.StakeToken.getMarket({ tx: { to: stakeToken } }, function (error, market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.StakeToken.getPayoutDistributionHash({ tx: { to: stakeToken } }, function (error, payoutDistributionHash) { /* ... */ })
// example output:
payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.StakeToken.getPayoutNumerator({
  tx: { to: stakeToken },
  index: 0
}, function (error, payoutSetValue) { /* ... */ })
// example output:
payoutSetValue = "1000"

augur.api.StakeToken.getStakeWindow({ tx: { to: stakeToken } }, function (error, reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.StakeToken.getReputationToken({ tx: { to: stakeToken } }, function (error, reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.StakeToken.getUniverse({ tx: { to: stakeToken } }, function (error, universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.StakeToken.isValid({ tx: { to: stakeToken } }, function (error, isValid) { /* ... */ })
// example output:
isValid = "1"
```

#### [Stake Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/StakeToken.sol)

The Stake Token is used to represent Staked [REP](#rep) by a [Reporter](#reporter) for a specific [Report](#report) and [Market](#market). When a Reporter submits a Report during a [Reporting Round](#reporting-round) for this Market they need to stake REP based on how confident they are in the Report. The REP staked is converted into Stake Tokens. A new type of Stake Token is created for each [Payout Set](#payout-set) submit by Reporters. Stake Tokens are then redeemed after [Finalizing the Market](#finalized-market). If your Stake Tokens are the tokens that represent the Winning Payout Set then your Stake Tokens can be redeemed for the amount of REP that was staked as well as a portion of [Reporting Fee](#reporting-fee) and REP staked on any other [Outcome](#outcome) but the [Final Outcome](#final-outcome). If your tokens are Staked on any other Outcome they are worthless and the Staked REP is redistributed to people with winning Stake Tokens.

The Stake Token is an ERC-20 token that implements all of the required functions listed in the [ERC-20 Token Standard] (https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md). It does not, however, implement the optional functions.

#### augur.api.StakeToken.getMarket({ tx }[, callback])

This method will return the [Market](#market) address that the Stake Token belongs too. Every Stake Token belongs to a specific Market in which the Token acts as a 1 to 1 representation of Staked [REP](#rep) for a particular [Payout Set](#payout-set) representing a [Report](#report) for the Market.

#### augur.api.StakeToken.getPayoutDistributionHash({ tx }[, callback])

Returns the [Payout Distribution Hash](#payout-distribution-hash) for a specific `stakeToken`. A Payout Distribution Hash is a sha3 hash of the [Payout Set](#payout-set) the Stake Token represents. The Payout Distribution Hash is used in other contract methods to identify a particular Payout Set.

#### augur.api.StakeToken.getPayoutNumerator({ tx, index }[, callback])

Returns the value at the `index` of the [Payout Set](#payout-set) represented by this `stakeToken`. The `index` is a value between `0` and number of [Outcomes](#outcome) for the [Market](#market) and corresponds to a particular Outcome of the Market. In a [Binary Market](#binary-market) for example, there are only two Outcomes, so the allowable `index` values are 0 and 1.

#### augur.api.StakeToken.getReportingWindow({ tx }[, callback])

Returns the [Reporting Window](#reporting-window) address for the specified `stakeToken`. All Stake Tokens belong to a specific [Market](#market) and all Market's belong to a specific Reporting Window. This method will return the Reporting Window that contains the Market that these Stake Tokens were purchased for.

#### augur.api.StakeToken.getReputationToken({ tx }[, callback])

As mentioned above, a Stake Token belongs to a [Market](#market) which in turn belongs to a [Reporting Window](#reporting-window). Reporting Windows only interact with a single [REP](#rep) contract, and this method will return the contract address of valid REP for this Reporting Window and by extension these Stake Tokens. Stake Tokens can only be purchased with REP that belongs to the Reporting Window the tokens are ultimately contained within.

#### augur.api.StakeToken.getUniverse({ tx }[, callback])

Returns the [Universe](#universe) address for the specified `stakeToken`'s [Reporting Window](#reporting-window). Everything in Augur belongs to a Universe, and Stake Tokens are no different. New Universes are created in the event of a [Fork](#fork), so this method provides an API user the ability to find out which Universe the Stake Token is currently in.

#### augur.api.StakeToken.isValid({ tx }[, callback])

This method is used as a way to check if the `stakeToken` represents a [Payout Set](#payout-set) that indicates the [Market](#market) is Invalid. This isn't to say that the Stake Token, the [Report](#report), or the Payout Set is invalid, but rather the [Outcome](#outcome) of the Market is unclear based on how the Market is worded or the result of Market doesn't correspond to any of the Outcomes set for the Market.

Universe Call API
---------------
```javascript
// Universe Contract Call API Examples:
var universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5";
var _parentPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";

augur.api.Universe.getChildUniverse({
  tx: { to: universe },
  _parentPayoutDistributionHash: _parentPayoutDistributionHash
}, function (error, childUniverse) { /* ... */ })
// example output:
childUniverse = "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c"

augur.api.Universe.getCurrentReportingWindow({ tx: { to: universe } }, function (error, currReportingWindow) { /* ... */ })
// example output:
currReportingWindow = "578"

augur.api.Universe.getDesignatedReportNoShowBond({ tx: { to: universe } }, function (error, designatedReportNoShowBond) { /* ... */ })
// example output:
designatedReportNoShowBond = "115350000000000000";

augur.api.Universe.getDesignatedReportStake({ tx: { to: universe } }, function (error, designatedReportStake) { /* ... */ })
// example output:
designatedReportStake = "124350000000000000";

augur.api.Universe.getForkEndTime({ tx: { to: universe } }, function (error, forkEndTime) { /* ... */ })
// example output:
forkEndTime = "1489855429";

augur.api.Universe.getForkingMarket({ tx: { to: universe } }, function (error, forkingMarket) { /* ... */ })
// example output:
forkingMarket = "0x78f7b43150d27c464359e735781c16ac585f52a8";

augur.api.Universe.getForkReputationGoal({ universe }, function (error, forkReputationGoal) { /* ... */ })
// example output:
forkReputationGoal = "150657";

augur.api.Universe.getMarketCreationCost({ tx: { to: universe } }, function (error, marketCreationCost) { /* ... */ })
// example output:
marketCreationCost = "100000000000000000";

augur.api.Universe.getNextReportingWindow({ tx: { to: universe } }, function (error, nextReportingWindow) { /* ... */ })
// example output:
nextReportingWindow = "579"

augur.api.Universe.getOpenInterestInAttoEth({ tx: { to: universe } }, function (error, openInterestInAttoEth) { /* ... */ })
// example output:
openInterestInAttoEth = "7123876876123"

augur.api.Universe.getParentUniverse({ tx: { to: universe } }, function (error, parentUniverse) { /* ... */ })
// example output:
parentUniverse = "0x63c59544b89cce1dd53b1b566862189b25adec41"

augur.api.Universe.getParentPayoutDistributionHash({ tx: { to: universe } }, function (error, universeParentPayoutDistributionHash) { /* ... */ })
// example output:
universeParentPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Universe.getPreviousReportingWindow({ tx: { to: universe } }, function (error, previousReportingWindow) { /* ... */ })
// example output:
previousReportingWindow = "577"

augur.api.Universe.getReportingFeeDivisor({ tx: { to: universe } }, function (error, reportingFeeDivisor) { /* ... */ })
// example output:
reportingFeeDivisor = "10000";

augur.api.Universe.getReportingPeriodDurationInSeconds({ tx: { to: universe } }, function (error, reportingPeriodDuration) { /* ... */ })
// example output:
reportingPeriodDuration = "2592000";

augur.api.Universe.getReportingWindow({ 
  tx: { to: universe }, 
  _reportingWindowId: 579 
}, function (error, reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x1f90cc6b4e89303e451c9b852827b5791667f570";

var _endTime = 2524608000;
augur.api.Universe.getReportingWindowByMarketEndTime({
  tx: { to: universe },
  _endTime: _endTime
}, function (error, reportingWindowByEndTime) { /* ... */ })
// example output:
reportingWindowByEndTime = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.Universe.getReportingWindowByTimestamp({
  tx: { to: universe },
  _timestamp: _endTime
}, function (error, reportingWindowByTimestamp) { /* ... */ })
// example output:
reportingWindowByTimestamp = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.Universe.getReportingWindowForForkEndTime({
  tx: { to: universe }
}, function (error, reportingWindowForForkEndTime) { /* ... */ })
// example output:
reportingWindowForForkEndTime = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.Universe.getReportingWindowId({
  tx: { to: universe },
  _timestamp: new Date().getTime()
}, function (error, reportingWindowId) { /* ... */ })
// example output:
reportingWindowId = "578";

augur.api.Universe.getReputationToken({ tx: { to: universe } }, function (error, reputationTokenAddress) { /* ... */ })
// example output:
reputationTokenAddress = "0x2fb561b2bdbcd1ae1995bdd6aff6776d6f4292f2";

augur.api.Universe.getTargetReporterGasCosts({ tx: { to: universe } }, function (error, targetReporterGasCosts) { /* ... */ })
// example output:
targetReporterGasCosts = "6000000";

var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.Universe.isContainerForMarket({
  tx: { to: universe },
  _shadytx: { to: market }
}, function (error, isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1";

var stakeToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
augur.api.Universe.isContainerForStakeToken({
  tx: { to: universe },
  _shadyStakeToken: stakeToken
}, function (error, isContainerForStakeToken) { /* ... */ })
// example output:
isContainerForStakeToken = "1";

var reportingWindow = "578";
augur.api.Universe.isContainerForReportingWindow({
  tx: { to: universe },
  _shadyReportingWindow: reportingWindow
}, function (error, isContainerForReportingWindow) { /* ... */ })
// example output:
isContainerForReportingWindow = "1";

var childUniverse = "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c";
augur.api.Universe.isParentOf({
  tx: { to: universe },
  _shadyChild: childUniverse
}, function (error, isParentOf) { /* ... */ })
// example output:
isParentOf = "1";
```
#### [Universe Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Universe.sol)

The Universe Contract is the contract that defines an Augur [Universe](#universe) and the methods used to interact with them. All of Augur's [Markets](#market), [Order Books](#order-book), [Reporting Windows](#reporting-window), and [REP](#rep) belong to a specific Universe. In the rare event that an [Last Report Round](#last-report-round) Market's [Proposed Outcome](#proposed-outcome) is [Challenged](#challenge) during the [Dispute Phase](#dispute-phase) of a Reporting Window then a [Fork](#fork) will occur and new Universes will be created. The Universe that originally contained the [Forked Market](#forked-market) will become a [Locked Universe](#locked-universe), thereby not allowing any Market creation to take place in the Locked Universe. The newly created Universes are known as [Child Universes](#child-universe), where as the original and now Locked Universe is considered those Child Universes' [Parent Universe](#parent-universe).

#### augur.api.Universe.getChildUniverse({ tx, \_parentPayoutDistributionHash }[, callback])

Returns the Child [Universe](#universe) address for this `universe` address whose [Forked Market](#forked-market)'s [Final Outcome](#final-outcome) is the `_parentPayoutDistributionHash` [Payout Distribution Hash](#payout-distribution-hash) provided.

#### augur.api.Universe.getCurrentReportingWindow({ tx }[, callback])

Returns the address of the current running [Reporting Window](#reporting-window) of the [Universe](#universe) specified in `tx`. Every Universe has Reporting Windows that are continually run for a duration of 30 days before immediately starting the next Window.

#### augur.api.Universe.getDesignatedReportNoShowBond({ tx }[, callback])

Returns the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) for [Markets](#market) in the [Universe](#universe) specified in `tx`, priced in [AttoREP](#atto-prefix).

#### augur.api.Universe.getDesignatedReportStake({ tx }[, callback])

Returns the amount of stake in [AttoREP](#atto-prefix) that the [Designated Reporter](#designated-reporter) must put up when submitting a [Designated Report](#designated-report) in the [Universe](#universe) specified in `tx`. 

#### augur.api.Universe.getForkEndTime({ tx }[, callback])

Returns the timestamp for when the [Fork Period](#fork-period) ends that was started on the [Universe](#universe) specified in `tx`. If there is no [Forked Market](#forked-market) in the Universe, this function will return 0.

#### augur.api.Universe.getForkingMarket({ tx }[, callback])

Returns the contract address of the [Market](#market) that the [Universe](#universe) specified in `tx` is [Forking](#fork) over. This returns the 0 address (0x0000000000000000000000000000000000000000) if the Universe has never Forked and there is no [Forked Market](#forked-market).

#### augur.api.Universe.getForkReputationGoal({ tx }[, callback])

Returns the estimated amount of [REP](#rep) that must be migrated to one [Child Universe](#child-universe) in order to allow a [Fork](#fork) in the [Universe](#universe) specified in `tx` to be finalized before the end of the [Fork Period](#fork-period).

#### augur.api.Universe.getMarketCreationCost({ tx }[, callback])

Returns the estimated amount of [AttoETH](#atto-prefix) required to create a [Market](#market) in the [Universe](#universe) specified in `tx`. The amount it returns will typically be well above the actual cost to create a Market, just to ensure the Market creation will succeed.

#### augur.api.Universe.getNextReportingWindow({ tx }[, callback])

Returns the address of the [Reporting Window](#reporting-window) coming up after the current Reporting Window ends in the [Universe](#universe) specified in `tx`.

#### augur.api.Universe.getOpenInterestInAttoEth({ tx }[, callback])

Returns the total value of all [Complete Sets](#complete-sets) that exist across all [Markets](#market) the [Universe](#universe) specified in `tx`, priced in attoETH.

#### augur.api.Universe.getParentUniverse({ tx }[, callback])

Returns the [Parent Universe](#parent-universe) address of the [Universe](#universe) specified in `tx`. When a [Fork](#fork) occurs, [Child Universes](#child-universe) are created and the original [Universe](#universe) that contained the [Forked Market](#forked-market) would become a Parent Universe to the newly created Universes. If this is called on the very first Augur Universe then it will return 0, as the first Universe had no Parent Universe.

#### augur.api.Universe.getParentPayoutDistributionHash({ tx }[, callback])

Returns the [Parent Universes](#parent-universe)'s [Payout Distribution Hash](#payout-distribution-hash) for [Child Universe](#child-universe) specified in `tx`. The Payout Distrubution Hash is a hash of the [Disputed](#dispute-bond) [Outcome](#outcome) of the [Forked Market](#forked-market).

#### augur.api.Universe.getPreviousReportingWindow({ tx }[, callback])

Returns the address of the previous [Reporting Window](#reporting-window) to the current Reporting Window for the [Universe](#universe) specified in `tx`.

#### augur.api.Universe.getReportingFeeDivisor({ tx }[, callback])

Returns the number by which the total payout amount for a [Market](#market) is divided in order to calculate the [Reporting Fee](#reporting-fee).

#### augur.api.Universe.getReportingPeriodDurationInSeconds({ tx }[, callback])

Returns the [Reporting Window](#reporting-window) full duration in seconds (also known as the [Reporting Period](#reporting-period)) for the Universe](#universe) specified in `tx`. This includes both the [Reporting Phase](#reporting-phase) and [Dispute Phase](#dispute-phase) of the Reporting Window. The Reporting Phase should last 27 days followed by a 3-day Dispute Phase which makes the Reporting Period Duration a total of 30 days, or 2,592,000 seconds.

#### augur.api.Universe.getReportingWindow({ tx, \_reportingWindowId }[, callback])

Returns the [Reporting Window](#reporting-window) Contract Address belonging to this `universe` that matches `_reportingWindowId`.

#### augur.api.Universe.getReportingWindowByMarketEndTime({ tx, \_endTime }[, callback])

Returns the [Reporting Window](#reporting-window) address on the specific `universe` given an `_endTime`.

#### augur.api.Universe.getReportingWindowByTimestamp({ tx, \_timestamp }[, callback])

Returns the [Reporting Window](#reporting-window) Contract Address for a specific `universe` and provided `_timestamp`.

#### augur.api.Universe.getReportingWindowForForkEndTime({ tx }[, callback])

Returns the [Reporting Window](#reporting-window) that the current [Fork Period](#fork-period) ends in the [Universe](#universe) specified in `tx`. If no [Fork](#fork) has occurred, the 0 address (0x0000000000000000000000000000000000000000) is returned.

#### augur.api.Universe.getReportingWindowId({ tx, \_timestamp }[, callback])

Returns the [Reporting Window](#reporting-window) Id for the [Universe](#universe) specified in `tx` and provided `_timestamp`. This is calculated by dividing the timestamp by the [Universe's](#universe) Reporting Window duration in seconds.

#### augur.api.Universe.getReputationToken({ tx }[, callback])

Returns the address of the [Reputation Token](#rep) for the [Universe](#universe) specified in `tx`. This is the REP usable within this Universe.

#### augur.api.Universe.getTargetReporterGasCosts({ tx }[, callback])

Returns the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) in [AttoETH](#atto-prefix) that is paid to the [First Reporter](#first-reporter) in the event of a [Designated Report](#designated-report) no-show, or refunded to the [Market Creator Mailbox](#market-creator-mailbox) if the [Designated Reporter](#designated-reporter) does show up. The [Universe](#universe) must be specified in `tx`.

#### augur.api.Universe.isContainerForMarket({ tx, \_shadyMarket }[, callback])

Returns whether the specific `universe` is a container for the [Market](#market) `_shadyMarket` address provided. Returns `1` if true, `0` if false. All Markets are created within a [Universe](#universe), and this function is used to help confirm if a Market is actually attached to the Universe in question.

#### augur.api.Universe.isContainerForStakeToken({ tx, \_shadyStakeToken }[, callback])

Returns whether the specific `universe` is a container for the [Reporting](#reporting) Token `_shadyStakeToken` address provided. Returns `1` if true, `0` if false. [Stake Tokens](#stake-tokens) are 1:1 exchangeable for [REP](#rep) by [Reporters](#reporter) to represent their staked REP for a [Report](#report). This method is designed determine whether the Universe contains the Stake Token specified.

#### augur.api.Universe.isContainerForReportingWindow({ tx, \_shadyReportingWindow }[, callback])

Returns whether the specific `universe` is a container for the [Reporting Window](#reporting-window) `_shadyReportingWindow` Contract Address provided. Returns `1` if true, `0` if false. Every Reporting Window belongs to a Universe and this method is used to see if a specific Reporting Window Address belongs to the Universe in question.

#### augur.api.Universe.isParentOf({ tx, \_shadyChild }[, callback])

Returns whether the specific `universe` is a container for the `_shadyChild` [Child Universe](#child-universe) Address provided. Returns `1` if true, `0` if false. If you want to see if a specific [Universe](#universe) is the [Parent Universe](#parent-universe) to a Child Universe you would use this function to determine that.
