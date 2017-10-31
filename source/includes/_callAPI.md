Call API
========
```javascript
// Call API Example
/**
* Get the Number of Outcomes for the Market:
* 0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42
*/

var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var params = { market: market };

// augur.api.<Contract Name>.<Contract Method>(<Params Object>, <Callback Function>);

// asynchronously -- STRONGLY RECOMMENDED --
augur.api.Market.getNumberOfOutcomes(params, function (numOutcomes) {
  console.log(numOutcomes);
});
// console prints 2

// synchronous -- possible but not recommended.
var outcomes = augur.api.Market.getNumberOfOutcomes(params);
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
var _address = "0x3d62bafc1791752393384b902f813da861ddedd9";

augur.api.DisputeBond.balanceOf({
  disputeBond: disputeBond,
  _address: _address
}, function (balance) { /* ... */ })
// example output:
balance = "1"

augur.api.DisputeBond.getBondRemainingToBePaidOut({ disputeBond: disputeBond }, function (bondRemainingToBePaidOut) { /* ... */ })
// example output:
bondRemainingToBePaidOut = "1100000000000000000000"

augur.api.DisputeBond.getDisputedPayoutDistributionHash({ disputeBond: disputeBond }, function (disputedPayoutDistributionHash) { /* ... */ })
// example output:
disputedPayoutDistributionHash = "0xff89be2020af3cb2ca244eb862df2d350300904a96039eb53cbacf380f13f21b"

augur.api.DisputeBond.getMarket({ disputeBond: disputeBond }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.DisputeBond.getOwner({ disputeBond: disputeBond }, function (owner) { /* ... */ })
// example output:
owner = "0x3d62bafc1791752393384b902f813da861ddedd9"

augur.api.DisputeBond.getUniverse({ disputeBond: disputeBond }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"
```
#### [Dispute Bond Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/DisputeBond.sol)

The [Dispute Bond](#dispute-bond) Token is used by [REP](#rep) holders to [Challenge](#challenge) the [Outcome](#outcome) of [Markets Awaiting Finalization](#market-awaiting-finalization). The Dispute Bond is only purchasable with REP. Only one Dispute Bond needs to be purchased for a Market per round of [Reporting](#reporting) in order for the Market to move to the next state of [Reporting](#report) in the next upcoming [Reporting Window](#reporting-window). If a Challenge is successful, which means the [Final Outcome](#final-outcome) of the [Market](#market) is something other than the [Proposed Outcome](#proposed-outcome) that was disputed, then the Bond Holder is entitled to up to 2x the Dispute Bond cost in REP. After 2x the cost of the Dispute Bond is paid to the Bond Holder any remaining REP is redistributed as normal to other Reporters who correctly staked on the Final Outcome.

#### augur.api.DisputeBond.getBondRemainingToBePaidOut({ disputeBond }[, callback])

This transaction will return the amount of REP, denoted in attotokens, of bond remaining to be paid out for a specified `disputeBond`. Bonds are paid back to the Bond Holder if they correctly [Challenged](#challenge) a [Proposed Outcome](#proposed-outcome) and the [Final Outcome](#final-outcome) for the [Market](#market) is something different than the Challenged Proposed Outcome. Otherwise, the Bond is paid out to [Reporters](#reporter) who confirmed the Proposed Outcome to be the Final Outcome of the Market.

#### augur.api.DisputeBond.getDisputedPayoutDistributionHash({ disputeBond }[, callback])

This transaction will return the [Payout Distribution Hash](#payout-distribution-hash) for the dispute payout given a specified `disputeBond`. The Payout Distribution Hash in this case refers the Hash that was the [Proposed Outcome](#proposed-outcome) but was [Challenged](#challenged) by the [Dispute Bond](#dispute-bond).

#### augur.api.DisputeBond.getMarket({ disputeBond }[, callback])

This transaction will return the [Market](#market) that a specified `disputeBond` belongs to. All Dispute Bonds belong to a Market, and there can only be one Dispute Bond per [Dispute Phase](#dispute-phase) for that Market.

#### augur.api.DisputeBond.getOwner({ disputeBond }[, callback])

This transaction will return the account address of the bond holder for a specified `disputeBond`. This is the person who owns/purchased the bond and would be the person to refund if they correctly [Challenged](#challenge) the [Proposed Outcome](#proposed-outcome) of a [Market Awaiting Finalization](#market-awaiting-finalization).

#### augur.api.DisputeBond.getUniverse({ disputeBond }[, callback])

This transaction will return the [Universe](#universe) that contained the [Market](#market) that a specified `disputeBond` belongs to.

Market Call API
----------------
```javascript
// Market Contract Call API Examples:
var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Market.getBestGuessSecondPlaceTentativeWinningPayoutDistributionHash({ market: market }, function (secondPlaceTentativeWinningPayoutDistributionHash) { /* ... */ })
// example output:
secondPlaceTentativeWinningPayoutDistributionHash = "0x20acbb1199c43cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Market.getDenominationToken({ market: market }, function (denominationToken) { /* ... */ })
// example output:
denominationToken = "0x30e3852448f4ab5d62bbf7544ca3c92daca5c957"

augur.api.Market.getDesignatedReportDisputeDueTimestamp({ market: market }, function (designatedReporterDisputeDueTimestamp) { /* ... */ })
// example output:
designatedReporterDisputeDueTimestamp = "1500907130"

augur.api.Market.getDesignatedReportDueTimestamp({ market: market }, function (designatedReporterDueTimestamp) { /* ... */ })
// example output:
designatedReporterDueTimestamp = "1500647930"

augur.api.Market.getDesignatedReporter({ market: market }, function (designatedReporter) { /* ... */ })
// example output:
designatedReporter = "0xca3edca4ed326bbcb77e914b379913b12d49654d"

augur.api.Market.getDesignatedReporterDisputeBond({ market: market }, function (designatedReporterDisputeBond) { /* ... */ })
// example output:
designatedReporterDisputeBond = "0xe783e32cfeea2d2be8df1ae6e978e6d4de63fe26"

augur.api.Market.getDesignatedReportPayoutHash({ market: market }, function (designatedReportPayoutHash) { /* ... */ })
// example output:
designatedReportPayoutHash = "0x58aff2018af3cb2ca3a3a1862df2d350300904a96039eb50cb0fd1ac3710aad"

augur.api.Market.getDesignatedReportReceivedTime({ market: market }, function (designatedReportReceivedTime) { /* ... */ })
// example output:
designatedReportReceivedTime = "1500388800";

augur.api.Market.getEndTime({ market: market }, function (endTime) { /* ... */ })
// example output:
endTime = "1500388730";

augur.api.Market.getExtraDisputeBondRemainingToBePaidOut({ market: market }, function (extraDisputeBondRemainingToBePaidOut) { /* ... */ })
// example output:
extraDisputeBondRemainingToBePaidOut = "1000";

augur.api.Market.getFinalizationTime({ market: market }, function (finalizationTime) { /* ... */ })
// example output:
finalizationTime = "1500647930";

augur.api.Market.getFinalPayoutDistributionHash({ market: market }, function (finalPayoutDistributionHash) { /* ... */ })
// example output:
finalPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Market.getFinalWinningStakeToken({ market: market }, function (winningStakeToken) { /* ... */ })
// example output:
winningStakeToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.Market.getFirstReportersDisputeBond({ market: market }, function (round1ReportersDisputeBond) { /* ... */ })
// example output:
round1ReportersDisputeBond = "0x0151f9153f236359ea3605cef0fffff90aba0327";

augur.api.Market.getForkingMarket({ market: market }, function (forkedMarket) { /* ... */ })
// example output:
forkedMarket = "0x0"

augur.api.Market.getLastReportersDisputeBond({ market: market }, function (round2ReportersDisputeBond) { /* ... */ })
// example output:
round2ReportersDisputeBond = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";

augur.api.Market.getMarketCreatorSettlementFeeDivisor({ market: market }, function (marketCreatorSettlementFee) { /* ... */ })
// example output:
marketCreatorSettlementFee = "20000000000000000"

augur.api.Market.getOwner({ market: market }, function (owner) { /* ... */ })
// example output:
owner = "0x06cbcd92af2571f1419b622a794d65db524f682b"

augur.api.Market.getNumberOfOutcomes({ market: market }, function (numOutcomes) { /* ... */ })
// example output:
numOutcomes = "2"

augur.api.Market.getNumTicks({ market: market }, function (numTicks) { /* ... */ })
// example output:
numTicks = "1000"

augur.api.Market.getReportingState({ market: market }, function (reportingState) { /* ... */ })
// example output:
reportingState = "0";

augur.api.Market.getReportingWindow({ market: market }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.Market.getShareToken({
  market: market,
  _outcome: 1
}, function (shareToken) { /* ... */ })
// example output:
shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054"

augur.api.Market.getStakeToken({ market: market, _payoutNumerators: _payoutNumerators, _invalid: _invalid }, function (stakeToken) { /* ... */ })
// example output:
stakeToken = "0x5caa66408617f77601d0dc19c163621e7f4b8b38"

var _payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";
augur.api.Market.getStakeTokenOrZeroByPayoutDistributionHash({ market: market, _payoutDistributionHash: _payoutDistributionHash }, function (stakeTokenOrZero) { /* ... */ })
// example output:
stakeTokenOrZero = "0x5caa66408617f77601d0dc19c163621e7f4b8b38"

augur.api.Market.getTentativeWinningPayoutDistributionHash({ market: market }, function (tentativeWinningPayoutDistributionHash) { /* ... */ })
// example output:
tentativeWinningPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.Market.getTotalStake({ market: market }, function (totalStake) { /* ... */ })
// example output:
totalStake = "1000"

augur.api.Market.getTotalWinningDisputeBondStake({ market: market }, function (_totalDisputeBondStake) { /* ... */ })
// example output:
_totalDisputeBondStake = "1000"

augur.api.Market.getUniverse({ market: market }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

var disputeBond = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
augur.api.Market.isContainerForDisputeBond({
  market: market,
  _shadyDisputeBond: disputeBond
}, function (isContainerForShareToken) { /* ... */ })
// example output:
isContainerForDisputeBond = "1"

var stakeToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
augur.api.Market.isContainerForStakeToken({
  market: market,
  _shadyStakeToken: stakeToken
}, function (isContainerForStakeToken) { /* ... */ })
// example output:
isContainerForStakeToken = "1"

var shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
augur.api.Market.isContainerForShareToken({
  market: market,
  _shadyShareToken: shareToken
}, function (isContainerForShareToken) { /* ... */ })
// example output:
isContainerForShareToken = "1"

augur.api.Market.isValid({
  market: market
}, function (isValid) { /* ... */ })
// example output:
isValid = "1"
```
#### [Market Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Market.sol)

#### augur.api.Market.getBestGuessSecondPlaceTentativeWinningPayoutDistributionHash({ market }[, callback])

Returns the second best, or second place, tentatively winning [Payout Distribution Hash](#payout-distribution-hash). The [Payout Set](#payout-set) [Reported](#report) by a [REP](#rep) holder is converted into a sha3 hash known as the Payout Distribution Hash. This method returns the Payout Set Hash with the 2nd most REP staked on it while the [Market](#market) is still [Awaiting Finalization](#market-awaiting-finalization).

#### augur.api.Market.getDenominationToken({ market }[, callback])

Returns the address of the token used to denominate the specified `market`. A Denomination Token is the ERC20 Token used as the currency to trade on the [Outcome](#outcome) of a [Market](#market). Currently, this will always return "Cash"; however, Augur will eventually support other types of Denomination Tokens.

#### augur.api.Market.getDesignatedReportDisputeDueTimestamp({ market }[, callback])

Returns the timestamp for when a specific `market`'s [Designated Report Phase](#designated-report-phase) and [Designated Dispute Phase](#designated-dispute-phase) should end. Can be up to 6 days after the [Market](#market)'s [End Time](#end-time), or as little as 3 days after the End Time, depends on how quickly the [Designated Reporter](#designated-reporter) [Reports](#report).

#### augur.api.Market.getDesignatedReportDueTimestamp({ market }[, callback])

Returns the timestamp for when a specific `market`'s  [Designated Report Phase](#designated-report-phase) should be completed but doesn't count the [Designated Dispute Phase](#designated-dispute-phase). If the [Designated Reporter](#designated-reporter) has already submit their [Report](#report) for the Designated Report Phase, then this will return the timestamp for when that Report was submit to the [Market](#market).

#### augur.api.Market.getDesignatedReporter({ market }[, callback])

Returns the address for the [Designated Reporter](#designated-reporter) set for the specified `market`. Every [Market](#market) is required to have an assigned Designated Reporter, which is set by the [Market Creator](#market-creator) during Market Creation.

#### augur.api.Market.getDesignatedReporterDisputeBond({ market }[, callback])

Returns the [Dispute Bond](#dispute-bond) Token address for the [Designated Reporting](#designated-reporting) round for a specified `market`. When a Designated [Report](#report) is [Challenged](#challenge) a Dispute Bond is created and this function is designed to return that bond address. This returns 0 if the [Market](#market) doesn't have a Designated Report or if the Designated Report hasn't been Challenged.

#### augur.api.Market.getDesignatedReportPayoutHash({ market }[, callback])

Returns the [Payout Distribution Hash](#payout-distribution-hash) of the [Payout Set](#payout-set) submit by the [Designated Reporter](#designated-reporter) for their [Report](#report). The Payout Set is used to determine how the [Market](#market) should [Settle](#settlement) for particular [Shares](#shares) after being [Finalized](#finalized-market).

#### augur.api.Market.getDesignatedReportReceivedTime({ market }[, callback])

Returns the timestamp of when the [Designated Reporter](#designated-reporter)'s [Report](#report) was submit for the specified `market`. Each [Market](#market) is required to have a Designated Reporter who is expected to Report during the [Designated Report Phase](#designated-report-phase). This timestamp is used to determine if they actually submit their Report on time, which would be anytime within 3 days of the Market's [End Time](#end-time).

#### augur.api.Market.getEndTime({ market }[, callback])

Returns the timestamp for when the specified `market`'s event has come to pass. When the [Market](#market)'s [End Time](#end-time) passes the Market either enters the [Designated Report Phase](#designated-report-phase) if a [Designated Reporter](#designated-reporter) is assigned or the Market will await the next [Reporting Window](#reporting-window) to begin [Reporting Rounds](#reporting-round).

#### augur.api.Market.getExtraDisputeBondRemainingToBePaidOut({ market }[, callback])

Returns the amount of [REP](#rep) that is available in a [Market](#market) to be migrated to other [Universes](#universe) to reward [Dispute Bond](#dispute-bond) holders.

#### augur.api.Market.getFinalizationTime({ market }[, callback])

Returns the timestamp for when the specified `market` was [Finalized](#finalized-market). A Finalized Market has a [Final Outcome](#final-outcome) set from successful [Market Resolution](#market-resolution) which cannot be [Challenged](#challenge) and the Market is considered [Settled](#settlement).

#### augur.api.Market.getFinalPayoutDistributionHash({ market }[, callback])

Returns the final [Payout Distribution Hash](#payout-distribution-hash) for a specified `market`. The Final Payout Distribution Hash determines how a [Finalized Market](#finalized-market) payouts out for a specific [Share](#shares) of an [Outcome](#outcome).

#### augur.api.Market.getFinalWinningStakeToken({ market }[, callback])

Returns the Stake Token address for the [Final Outcome](#final-outcome) of a specific `market`. If the [Market](#market) isn't [Settled](#market-settlement) then this will return a null address, `0x0`.

#### augur.api.Market.getFirstReportersDisputeBond({ market }[, callback])

Returns the [Dispute Bond](#dispute-bond) Token address for the [First Report Round](#first-report-round) for a specified `market`. When a First Report Round [Report](#report) is [Challenged](#challenge) a Dispute Bond is created and this function is designed to return that bond address. This returns 0 if the [Market](#market) doesn't have a First Round Report or if the First Round Report hasn't been Challenged.

#### augur.api.Market.getForkingMarket({ market }[, callback])

Returns the [Market](#market) address of the [Forked Market](#forked-market) for the [Universe](#universe) that contains the specified `market` address. If the `market` address specified belongs in a Universe that hasn't had a [Fork](#fork) take place, this will return `0x0`.

#### augur.api.Market.getLastReportersDisputeBond({ market }[, callback])

Returns the [Last Report Round](#last-report-round)'s [Dispute Bond](#dispute-bond) address for the `market`. If this [Market](#market) hasn't gone through a Last Report Round or there was no [Challenge](#challenge) to the [Proposed Outcome](#proposed-outcome) of the Last Report Round then this will return an empty address `0x0`.

#### augur.api.Market.getMarketCreatorSettlementFeeDivisor({ market }[, callback])

Returns the [Creator Fee](#creator-fee) set by the [Market Creator](#market-creator), denominated in attotokens per [settlement](#settlement) of a [Complete Set](#complete-set), for the `market`.

#### augur.api.Market.getNumberOfOutcomes({ market }[, callback])

Returns the number of [Outcomes](#outcome) for a specified `market`. The number of outcomes is the number of potential results of the [Market](#market).

#### augur.api.Market.getNumTicks({ market }[, callback])

Returns the [Number of Ticks](#number-of-ticks) set for a specific `market`. The Number of [Ticks](#ticks) represents the number of valid price points between the [Market](#market)'s [Minimum Price](#minimum-display-price) and [Maximum Price](#maximum-display-price).

#### augur.api.Market.getOwner({ market }[, callback])

Returns the address of the specified [Market's](#market) owner.

#### augur.api.Market.getPayoutDistributionHashStake({ market, \_payoutDistributionHash }[, callback])

Returns the staked amount of [REP](#rep), in attorep, for a specified `_payoutDistributionHash` of a `market`. [Reporters](#reporter) who stake REP on the same [Outcome](#outcome) during the [Reporting Phase](#reporting-phase) will have identical [Payout Distribution Hashs](#payout-distribution-hash). This method returns the total amount of staked REP for a specific Payout Distribution Hash. The Hash with the most REP staked on [it is considered the Proposed Outcome](#proposed-outcome) while the [Market](#market) is [Awaiting Finalization](#awaiting-finalization). If the [Dispute Phase](#dispute-phase) passes without a [Challenge](#challenge) to the Proposed Outcome then the Market is [Finalized](#finalized-market) and the Proposed Outcome becomes the [Final Outcome](#final-outcome).

#### augur.api.Market.getReportingState({ market }[, callback])

Returns the current [Reporting](#report) State that the `market` is in. This method returns a number between 0 and 12, which corresponds to the various Reporting States outlined in the [Reporting Section](#reporting) of the documentation.

#### augur.api.Market.getReportingWindow({ market }[, callback])

Returns the [Reporting Window](#reporting-window) address for the specified `market`. This returns the Reporting Window when this [Market](#market) is scheduled to be [Reported](#report) on for [Reporting Rounds](#reporting-round) if a [Designated Reporter](#designated-reporter) fails to Report or their Report is [Challenged](#challenge).

#### augur.api.Market.getShareToken({ market, \_outcome }[, callback])

Returns the [Share](#shares) Token's address for the specified `market` and `_outcome`. Every [Outcome](#outcome) of a [Market](#market) has a separate Share Token used to handle trading around that Outcome and this method returns the contract address of the Share Tokens for the specified Outcome.

#### augur.api.Market.getStakeToken({ market, \_payoutNumerators, \_invalid }[, callback])

Returns the Stake Token address for `_payoutNumerators` and `_invalid` on the `market` specified. When a [Reporter](#reporter) submits a [Report](#report), they submit a [Payout Set](#payout-set) (`_payoutNumerators`) array indicating how the [Market](#market) should payout. Each Payout Set has a Stake Token associated with it, so if two different Reporters submit the same Payout Set as their Report then they will receive the same Stake Token currency. This method will return the Stake Token associated with a specific Payout Set.

#### augur.api.Market.getStakeTokenOrZeroByPayoutDistributionHash({ market, \_payoutDistributionHash }[, callback])

Returns a Stake Token address, or 0 if there is no Stake Token for a specific `_payoutDistributionHash` for a `market`. [Payout Sets](#payout-set) that are valid, that is that they sum of the values in the array equal the [Number of Ticks](#number-of-ticks) for the [Market](#market) and no individual value in the array is greater than the Number of [Ticks](#ticks), become [Payout Distribution Hashes](#payout-distribution-hash). This method returns the Stake Token given a Payout Distribution Hash, much like `getStakeToken` returns the Stake Token given a Payout Set.

#### augur.api.Market.getTentativeWinningPayoutDistributionHash({ market }[, callback])

Returns the tentatively winning [Payout Distribution Hash](#payout-distribution-hash) given a specified `market`. The tentatively winning Payout Distribution Hash is the hash with the most [REP](#rep) staked on it prior to [Market Finalization](#finalized-market). Once the [Market](#market) Finalized the tentatively winning Payout Distribution Hash becomes the Winning Payout Distribution Hash.

#### augur.api.Market.getTotalStake({ market }[, callback])

Returns the total amount staked on the specified [Market](#market).

#### augur.api.Market.getTotalWinningDisputeBondStake({ market }[, callback])

Returns the sum of all [Dispute Bonds](#dispute-bond) staked on challenging a [Payout Distribution Hash](#payout-distribution-hash) other than the final Payout Distribution Hash for the specfied [Market](#market).

#### augur.api.Market.getUniverse({ market }[, callback])

Returns the [Universe](#universe) address of the Universe that the specified `market` is contained within. All [Markets](#market) are created on a specific Universe and new Universes can be created in the event of a [Fork](#fork).

#### augur.api.Market.isContainerForDisputeBond({ market, \_shadyDisputeBond }[, callback])

Returns whether the specific `market` is a container for the `_shadyDisputeBond` address provided. A `_shadyDisputeBond` address will return true if it is a [Designated Report](#designated-reporter) [Dispute Bond](#dispute-bond) token, or a [Reporting Round](#reporting-round) Dispute Bond for this [Market](#market). Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForStakeToken({ market, \_shadyStakeToken }[, callback])

Returns whether the specific `market` is a container for the `_shadyStakeToken` address provided. The `_shadyStakeToken` address will return true if the address is a Stake Token that belongs to the [Market](#market), otherwise false will be returned. Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForShareToken({ market, \_shadyShareToken }[, callback])

Returns whether the specific `market` is a container for the `_shadyShareToken` address provided. The `_shadyShareToken` address will return true if it's a [Share](#shares) Token belonging to the [Market](#market), otherwise it will return false if the address is not a Share Token belonging to this Market. Returns `1` if true, `0` if false.

#### augur.api.Market.isValid({ market }[, callback])

Returns True or False depending on if the [Market](#market) passed as `market` has been [Finalized](#finalized-market) as Invalid. This means the Market is unable to be finalized to a single [Final Outcome](#final-outcome) due to an unclear Market or an indeterminate [Outcome](#outcome). Returns `1` for True in the event that the Market is valid, `0` is False, the Market is invalid.

Orders Call API
---------------
```javascript
// Orders Contract Call API Examples:
var _orderId = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
var _type = "1";
var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var _outcome = "1";
var _fxpPrice = "450000000000000000"; // 0.45

augur.api.Orders.assertIsNotBetterPrice({
  _type: _type,
  _fxpPrice: _fxpPrice,
  _betterOrderId: _orderId
}, function (isNotBetterPrice) { /* ... */ })
// example output:
isNotBetterPrice = "1"

augur.api.Orders.assertIsNotWorsePrice({
  _type: _type,
  _fxpPrice: _fxpPrice,
  _worstOrderId: _orderId
}, function (isNotWorstPrice) { /* ... */ })
// example output:
isNotWorstPrice = "0"

augur.api.Orders.getAmount({ _orderId: _orderId }, function (amount) { /* ... */ })
// example output:
amount = "15"

augur.api.Orders.getBestOrderId({
  _type: _type,
  _market: _market,
  _outcome: _outcome
}, function (bestOrderID) { /* ... */ })
// example output:
bestOrderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

var secondBestOrderID = "0x49cb49f610b5f6e31ee163a8ad65f964af1088e38c8a1b07f1218177b5e006b5";
augur.api.Orders.getBetterOrderId({ _orderId: secondBestOrderID }, function (betterOrderID) { /* ... */ })
// example output:
betterOrderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

augur.api.Orders.getLastOutcomePrice({
  _market: _market,
  _outcome: _outcome
}, function (lastOutcomePrice) { /* ... */ })
// example output:
lastOutcomePrice = "490000000000000000"

augur.api.Orders.getMarket({ _orderId: _orderId }, function (orderMarket) { /* ... */ })
// example output:
orderMarket = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Orders.getOrderCreator({ _orderId: _orderId }, function (owner) { /* ... */ })
// example output:
owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";

augur.api.Orders.getOrderMoneyEscrowed({ _orderId: _orderId }, function (orderMoneyEscrowed) { /* ... */ })
// example output:
orderMoneyEscrowed = "5000000000000000000"

augur.api.Orders.getOrderSharesEscrowed({ _orderId: _orderId }, function (orderSharesEscrowed) { /* ... */ })
// example output:
orderSharesEscrowed = "0";

augur.api.Orders.getOrderType({ _orderId: _orderId }, function (type) { /* ... */ })
// example output:
type = "1";

augur.api.Orders.getOutcome({ _orderId: _orderId }, function (orderOutcome) { /* ... */ })
// example output:
orderOutcome = "1";

augur.api.Orders.getPrice({ _orderId: _orderId }, function (price) { /* ... */ })
// example output:
price = "500000000000000000";

augur.api.Orders.getVolume({ _market: _market }, function (volume) { /* ... */ })
// example output:
volume = "100000000000000000000000";

var secondWorstOrderID = "0x4b538f4de2517f7d7bbb227161981c51c40bf725da9941b3dc02e6c14cafd1f1";
augur.api.Orders.getWorseOrderId({ _orderId: secondWorstOrderID }, function (worseOrderID) { /* ... */ })
// example output:
worseOrderID = "0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api.Orders.getWorstOrderId({
  _type: _type,
  _market: _market,
  _outcome: _outcome
}, function (worstOrderID) { /* ... */ })
// example output:
worstOrderID = "0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api.Orders.isBetterPrice({
  _type: _type,
  _fxpPrice: _fxpPrice,
  _orderId: _orderId
}, function (isBetterPrice) { /* ... */ })
// example output:
isBetterPrice = "0"

augur.api.Orders.isWorsePrice({
  _type: _type,
  _fxpPrice: _fxpPrice,
  _orderId: _orderId
}, function (isWorsePrice) { /* ... */ })
// example output:
isWorsePrice = "1"
```
#### [Orders Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/Orders.sol)

#### augur.api.Orders.assertIsNotBetterPrice({ \_type, \_fxpPrice, \_betterOrderId }[, callback])

Returns whether the specified `_fxpPrice` is not a better price than the `_betterOrderId` for a given order `_type`. Returns `1` if true, `0` if false.

#### augur.api.Orders.assertIsNotWorsePrice({ \_type, \_fxpPrice, \_worstOrderId }[, callback])

Returns whether the specified `_fxpPrice` is not a worst price than the `_worstOrderId` for a given order `_type`. Returns `1` if true, `0` if false.

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

#### augur.api.Orders.getOrderCreator({ \_orderId }[, callback])

Returns the [Maker](#maker) address of the specified `_orderId`.

#### augur.api.Orders.getOrderMoneyEscrowed({ \_orderId }[, callback])

Returns the fixed point value of the amount of money escrowed by the [Maker](#maker) for a specified `_orderId`.

#### augur.api.Orders.getOrderSharesEscrowed({ \_orderId }[, callback])

Returns the fixed point value of the amount of shares escrowed by the [Maker](#maker) for a specified `_orderId`.

#### augur.api.Orders.getOrderType({ \_orderId }[, callback])

Returns the trade type, `1` for bid and `2` for ask, for a specified `_orderId`.

#### augur.api.Orders.getOutcome({ \_orderId }[, callback])

Returns the [Outcome](#outcome) being traded on for the specified `_orderId`.

#### augur.api.Orders.getPrice({ \_orderId }[, callback])

Returns the fixed point value of the price of a specified `_orderId`.

#### augur.api.Orders.getVolume({ \_market }[, callback])

Returns the fixed point value of the volume of a specified `_market`.

#### augur.api.Orders.getWorseOrderId({ \_orderId }[, callback])

Returns a worse order ID than the `_orderId`.

#### augur.api.Orders.getWorstOrderId({ \_type, \_market, \_outcome }[, callback])

Returns the order ID of the worst order on the `_market` of `_type` trading on the provided `_outcome`.

#### augur.api.Orders.isBetterPrice({ \_type, \_fxpPrice, \_orderId }[, callback])

Returns whether the specified `_fxpPrice` is a better price than the `_orderId` for a given order `_type`. Returns `1` if true, `0` if false.

#### augur.api.Orders.isWorsePrice({ \_type, \_fxpPrice, \_orderId }[, callback])

Returns whether the specified `_fxpPrice` is a worst price than the `_orderId` for a given order `_type`. Returns `1` if true, `0` if false.

Orders Fetcher Call API
-----------------------
```javascript
// Orders Fetcher Contract Call API Examples:
var _orderId = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
var _type = "1";
var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var _outcome = "1";
var _fxpPrice = "450000000000000000"; // 0.45

augur.api.OrdersFetcher.ascendOrderList({
  _type: _type,
  _fxpPrice: _fxpPrice,
  _lowestOrderId: _orderId
}, function (ascendingOrderList) { /* ... */ })
// example output:
ascendingOrderList = [
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870",
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0"]

augur.api.OrdersFetcher.descendOrderList({
  _type: _type,
  _fxpPrice: _fxpPrice,
  _highestOrderId: _orderId
}, function (decendingOrderList) { /* ... */ })
// example output:
decendingOrderList = [
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e",
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"]

augur.api.OrdersFetcher.findBoundingOrders({
  _type: _type,
  _fxpPrice: _fxpPrice,
  _bestOrderId: _orderId,
  _worstOrderId: 0,
  _betterOrderId: 0,
  _worseOrderId: 0
}, function (boundingOrders) { /* ... */ })
// example output:
boundingOrders = [
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0",
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e"]
```
#### [Orders Fetcher Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/OrdersFetcher.sol)

#### augur.api.OrdersFetcher.ascendOrderList({ \_type, \_fxpPrice, \_lowestOrderId }[, callback])

Returns an array containing the order IDs that should be the better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_lowestOrderId` is an order ID expected to be a worse price than the `_fxpPrice` specified for an order of `_type`.

#### augur.api.OrdersFetcher.descendOrderList({ \_type, \_fxpPrice, \_highestOrderId }[, callback])

Returns an array containing the order IDs that should be set to better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_highestOrderId` is an order ID expected to be a better price than the `_fxpPrice` specified for an order of `_type`.

#### augur.api.OrdersFetcher.findBoundingOrders({ \_type, \_fxpPrice, \_bestOrderId, \_worstOrderId, \_betterOrderId, \_worseOrderId }[, callback])

Returns an array containing the order IDs that should be set to better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_betterOrderId` and `_worseOrderId` should be orders that are better or worse than the `_fxpPrice` for an order of `_type`. `_bestOrderId` and `_worstOrderId` should be the best and worst order IDs on the order book for the specified `_type`.

Participation Token Call API
--------------------
```javascript
// Participation Token Contract Call API Examples:
var participationToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";

augur.api.ParticipationToken.getReportingWindow({ participationToken: participationToken }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow =  "0x1f90cc6b4e89303e451c9b852827b5791667f570";
```
#### [Participation Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/ParticipationToken.sol)

The Participation Token is an ERC-20 token that implements all of the required functions listed in the [ERC-20 Token Standard] (https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md). It does not, however, implement the optional functions.

#### augur.api.ParticipationToken.getReportingWindow({ participationToken }[, callback])

Returns the [Reporting Window](#reporting-window) address for the specified `participationToken`. All Stake Tokens belong to a specific [Market](#market) and all Market's belong to a specific Reporting Window. This method will return the Reporting Window that contains the Market that these Participation Tokens were purchased for.

Reporting Window Call API
-------------------------
```javascript
// Reporting Window Contract Call API Examples:
var reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a";
var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var _reporter = "0x2cd999e2f90dfc237ccbc52e2a469e1e11221f75";

augur.api.ReportingWindow.allMarketsFinalized({ reportingWindow: reportingWindow }, function (allMarketsFinalized) { /* ... */ })
// example output:
allMarketsFinalized = "1"

augur.api.ReportingWindow.getAvgReportingGasPrice({ reportingWindow: reportingWindow }, function (avgReportingGasPrice) { /* ... */ })
// example output:
avgReportingGasPrice = "340000"

augur.api.ReportingWindow.getDisputeEndTime({ reportingWindow: reportingWindow }, function (disputeEndTime) { /* ... */ })
// example output:
disputeEndTime = "1500907130"

augur.api.ReportingWindow.getDisputeStartTime({ reportingWindow: reportingWindow }, function (disputeStartTime) { /* ... */ })
// example output:
disputeStartTime = "1500647930"

augur.api.ReportingWindow.getEndTime({ reportingWindow: reportingWindow }, function (endTime) { /* ... */ })
// example output:
endTime = "1500388730"

augur.api.ReportingWindow.getFirstReporterMarketsCount({ reportingWindow: reportingWindow }, function (firstReporterMarketsCount) { /* ... */ })
// example output:
firstReporterMarketsCount = "54321"

augur.api.ReportingWindow.getLastReporterMarketsCount({ reportingWindow: reportingWindow }, function (lastReporterMarketsCount) { /* ... */ })
// example output:
lastReporterMarketsCount = "12345"

augur.api.ReportingWindow.getMarketsCount({ reportingWindow: reportingWindow }, function (marketsCount) { /* ... */ })
// example output:
marketsCount = "65"

augur.api.ReportingWindow.getNextReportingWindow({ reportingWindow: reportingWindow }, function (nextReportingWindow) { /* ... */ })
// example output:
nextReportingWindow = "0x0d6b5a54f940bf3d52e438cab785981aaefdf40c"

augur.api.ReportingWindow.getNumDesignatedReportNoShows({ reportingWindow: reportingWindow }, function (numDesignatedReportNoShows) { /* ... */ })
// example output:
numDesignatedReportNoShows = "2"

augur.api.ReportingWindow.getNumIncorrectDesignatedReportMarkets({ reportingWindow: reportingWindow }, function (numIncorrectDesignatedReportMarkets) { /* ... */ })
// example output:
numIncorrectDesignatedReportMarkets = "1"

augur.api.ReportingWindow.getNumInvalidMarkets({ reportingWindow: reportingWindow }, function (numInvalidMarkets) { /* ... */ })
// example output:
numInvalidMarkets = "3"

augur.api.ReportingWindow.getNumMarkets({ reportingWindow: reportingWindow }, function (numMarkets) { /* ... */ })
// example output:
numMarkets = "65"

augur.api.ReportingWindow.getParticipationToken({ reportingWindow: reportingWindow }, function () { /* ... */ })
// example output:
= "0x3d1db1cac3153879b1c190aeb9bb7292f09ad83e"

augur.api.ReportingWindow.getPreviousReportingWindow({ reportingWindow: reportingWindow }, function () { /* ... */ })
// example output:
= "0x3d1db1cac3153879b1c190aeb9bb7292f09ad83e"

augur.api.ReportingWindow.getReportingEndTime({ reportingWindow: reportingWindow }, function (reportingEndTime) { /* ... */ })
// example output:
reportingEndTime = "1500647900"

augur.api.ReportingWindow.getReportingStartTime({ reportingWindow: reportingWindow }, function (reportingStartTime) { /* ... */ })
// example output:
reportingStartTime = "14998315100"

augur.api.ReportingWindow.getReputationToken({ reportingWindow: reportingWindow }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.ReportingWindow.getStartTime({ reportingWindow: reportingWindow }, function (startTime) { /* ... */ })
// example output:
startTime = "14995895900"

augur.api.ReportingWindow.getTotalStake({ reportingWindow: reportingWindow }, function (totalStake) { /* ... */ })
// example output:
totalStake = "18900512367"

augur.api.ReportingWindow.getTotalWinningStake({ reportingWindow: reportingWindow }, function (totalStake) { /* ... */ })
// example output:
totalStake = "29105"

augur.api.ReportingWindow.getUniverse({ reportingWindow: reportingWindow }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReportingWindow.isActive({ reportingWindow: reportingWindow }, function (isActive) { /* ... */ })
// example output:
isActive = "1"

augur.api.ReportingWindow.isContainerForMarket({
  reportingWindow: reportingWindow,
  _shadyMarket: _market
}, function (isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1"

augur.api.ReportingWindow.isDisputeActive({ reportingWindow: reportingWindow }, function (isDisputeActive) { /* ... */ })
// example output:
isDisputeActive = "1"

augur.api.ReportingWindow.isForkingMarketFinalized({ reportingWindow: reportingWindow }, function (isForkingMarketFinalized) { /* ... */ })
// example output:
isForkingMarketFinalized = "0";

augur.api.ReportingWindow.isOver({ reportingWindow: reportingWindow }, function (isOver) { /* ... */ })
// example output:
isOver = "0";

augur.api.ReportingWindow.isReportingActive({ reportingWindow: reportingWindow }, function (isReportingActive) { /* ... */ })
// example output:
isReportingActive = "1"
```
#### [Reporting Window Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/ReportingWindow.sol)

#### augur.api.ReportingWindow.allMarketsFinalized({ reportingWindow }[, callback])

Returns true if all [Markets](#market) in the specified [Reporting Window](#reporting-window) are [Finalized Markets](#finalized-market). Otherwise, returns false.

#### augur.api.ReportingWindow.getAvgReportingGasPrice({ reportingWindow }[, callback ])

Returns the average amount of Gas spent per [Report](#report) submit during the [Reporting Window](#reporting-window).

#### augur.api.ReportingWindow.getDisputeEndTime({ reportingWindow }[, callback])

Every [Reporting Window](#reporting-window) consists of two phases, the [Reporting Phase](#reporting-phase) and the [Dispute Phase](#dispute-phase). This method is used to return the timestamp for the end of the Reporting Window's Dispute Phase.

#### augur.api.ReportingWindow.getDisputeStartTime({ reportingWindow }[, callback])

As stated above, [Reporting Windows](#reporting-window) have two phases, the [Reporting Phase](#reporting-phase) and [Dispute Phase](#dispute-phase) and this method returns the timestamp of the start of the Dispute Phase.

#### augur.api.ReportingWindow.getEndTime({ reportingWindow }[, callback])

Returns a timestamp for when this [Reporting Window](#reporting-window) will end. A Reporting Window is considered active for a total of 30 days, then ends, and is no longer considered to be active.

#### augur.api.ReportingWindow.getFirstReporterMarketsCount({ reportingWindow }[, callback])

Returns the number of [Markets](#market) in the specified [Reporting Window](#reporting-window) that are in the [First Report Round](#first-report-round). NOTE: If the Reporting Window has not begun yet, this number will include Markets that may end up being [Finalized](#finalized-market) in the [Designated Report Round](#designated-report-phase). Since it is unknown whether [Designated Reporters](#designated-reporter) ultimately will submit a report, Augur assumes by default that all new Markets will go to the First Report Round. Once the Reporting Window has begun, this function will return the number of Markets that are actually in the First Report Round.

#### augur.api.ReportingWindow.getLastReporterMarketsCount({ reportingWindow }[, callback])

Returns the number of [Markets](#market) in the specified [Reporting Window](#reporting-window) that are in the [Last Report Round](#last-report-round).

#### augur.api.ReportingWindow.getMarketsCount({ reportingWindow }[, callback])

Returns the total number of [Markets](#market) that belong to the [Reporting Window](#reporting-window). This includes [Reporting Round](#reporting-round) Markets.

#### augur.api.ReportingWindow.getNextReportingWindow({ reportingWindow }[, callback])

Returns the next [Reporting Window](#reporting-window)'s Contract Address. Reporting Windows last 30 days and continually occur, one after the other. Only one Reporting Window can be active at a time in a [Universe](#universe), and every Reporting Window belongs to a Universe.

#### augur.api.ReportingWindow.getNumDesignatedReportNoShows({ reportingWindow }[, callback])

Returns the number of [Markets](#market) belonging to this [Reporting Window](#reporting-window) in which the [Designated Reporter](#designated-reporter) failed to [Report](#report) during the [Designated Report Phase](#designated-report-phase). These Markets will have [First Report Bonds](#first-report-bonds) up for grabs for the [First Reporter](#first-reporter) because these Markets have yet to receive a Report. This only includes Markets where Designated Reporters failed to Report, and does not include Markets where the Designated Reporter's [Proposed Outcome](#proposed-outcome) was [Challenged](#challenge).

#### augur.api.ReportingWindow.getNumIncorrectDesignatedReportMarkets({ reportingWindow }[, callback])

Returns the number of [Markets](#market) belonging to this [Reporting Window](#reporting-window) in which [Designated Reporter's](#designated-reporter) [Proposed Outcome](#proposed-outcome) was [Challenged](#challenge) during the [Designated Dispute Phase](#designated-dispute-phase). This is only the count of Markets where Designated Reporters did [Report](#report) during the [Designated Report Phase](#designated-report-phase) but the Market didn't get [Finalized](#finalized-market). This doesn't include Markets where the Designated Reporter failed to submit a Report.

#### augur.api.ReportingWindow.getNumInvalidMarkets({ reportingWindow }[, callback])

Returns the number of [Markets](#market) that were [Reported](#report) to be Invalid during a [Reporting Window](#reporting-window). Invalid Markets are Markets that aren't clearly defined or doesn't fit one of the [Outcomes](#outcome) set for this Market. [Reporters](#reporter) are encouraged to Report the Market as Invalid if they can't confidently stake their [REP](#rep) into a single Outcome for the Market.

#### augur.api.ReportingWindow.getNumMarkets({ reportingWindow }[, callback])

Returns the total number of [Markets](#market) that belong to the [Reporting Window](#reporting-window). This includes [Reporting Round](#reporting-round) Markets.

#### augur.api.ReportingWindow.getParticipationToken({ reportingWindow }[, callback])

Returns the Contract Address of the [Participation Token](#participation-token) associated with the specified [Reporting Window](#reporting-window).

#### augur.api.ReportingWindow.getPreviousReportingWindow({ reportingWindow }[, callback])

Returns the previous [Reporting Window](#reporting-window)'s Contract Address. Reporting Windows last 30 days and continually occur, one after the other. Only one Reporting Window can be active at a time in a [Universe](#universe), and every Reporting Window belongs to a Universe.

#### augur.api.ReportingWindow.getReportingEndTime({ reportingWindow }[, callback])

Returns a timestamp of when the [Reporting Phase](#reporting-phase) of a specific [Reporting Window](#reporting-window) will be ending. Reporting Windows are 30 days long and split into two Phases, the Reporting Phase and the [Dispute Phase](#dispute-phase). The Reporting Phase lasts 27 days, the Dispute Phase lasts 3 days.

#### augur.api.ReportingWindow.getReportingStartTime({ reportingWindow }[, callback])

Returns a timestamp of when the [Reporting Phase](#reporting-phase) of a specific [Reporting Window](#reporting-window) will be starting. Reporting Windows are 30 days long and split into two Phases, the Reporting Phase and the [Dispute Phase](#dispute-phase). The Reporting Phase lasts 27 days, the Dispute Phase lasts 3 days.

#### augur.api.ReportingWindow.getReputationToken({ reportingWindow }[, callback])

Returns the [Reputation Token (REP)](#rep) address for the specified [Reporting Window](#reporting-window). Every Reporting Window has a [Reporting Phase](#reporting-phase) where [Reporters](#reporter) submit [Reports](#report) on the [Outcomes](#outcome) of [Markets](#market). In order to Report, Reporters need to stake REP. A Reporting Window only accepts one REP contract as the source of staked REP and this method returns that contract's address.

#### augur.api.ReportingWindow.getStartTime({ reportingWindow }[, callback])

Returns a timestamp of when a [Reporting Window](#reporting-window) becomes active and starts. A Reporting Window is considered active for a total of 30 days, then ends, and is no longer considered to be active. Only active Reporting Windows allow [Reporters](#reporter) to [Report](#report) on the [Outcomes](#outcome) of the [Markets](#market) contained in the Reporting Window.

#### augur.api.ReportingWindow.getTotalStake({ reportingWindow }[, callback])

Returns the total amount staked across all [Markets](#market) in the specified [Reporting Window](#reporting-window).

#### augur.api.ReportingWindow.getTotalWinningStake({ reportingWindow }[, callback])

Returns the total amount [Participation Tokens](#participation-token) purchased in the specified [Reporting Window](#reporting-window). NOTE: Participation Tokens are only purchasable in the event that no [Market](#market) in a Reporting Window can be reported on.

#### augur.api.ReportingWindow.getUniverse({ reportingWindow }[, callback])

Returns the [Universe](#universe) address that the [Reporting Window](#reporting-window) belongs to. Every Reporting Window belongs to a specific Universe in which they were created and operate within.

#### augur.api.ReportingWindow.isActive({ reportingWindow }[, callback])

This method returns whether the [Reporting Window](#reporting-window) is currently active or not. Reporting Windows are considered active during the Window's [Reporting Phase](#reporting-phase) and [Dispute Phase](#dispute-phase), which last a total of 30 days. Returns `1` if the `reportingWindow` is active, `0` if not.

#### augur.api.ReportingWindow.isContainerForMarket({ reportingWindow, \_shadyMarket }[, callback])

Returns whether the `_shadyMarket` address provided is a [Market](#market) that is set to be [Reported](#report) on during the [Reporting Window](#reporting-window). Markets are assigned a Reporting Window that is the first Reporting Window following the Market's [End Time](#end-time). Returns `1` if the Market belongs to the `reportingWindow`, `0` if not.

#### augur.api.ReportingWindow.isDisputeActive({ reportingWindow }[, callback])

Returns whether the [Reporting Window](#reporting-window) is currently in it's [Dispute Phase](#dispute-phase) or not. The Dispute Phase is a 3 day long period that follows the Reporting Window's [Reporting Phase](#reporting-phase), which lasts 27 days. Returns `1` if the Reporting Window's Dispute Phase is active, `0` if not.

#### augur.api.ReportingWindow.isForkingMarketFinalized({ reportingwindow }[, callback])

Returns whether the [Forked Market](#forked-market) that caused this [Reporting Window](#reporting-window)'s [Universe](#universe) to be created has been [Finalized](#finalized-market) or not. Every Reporting Window belongs to a Universe and all Universes, except for the first Universe, are created because of a [Fork](#fork). Returns `1` if the Forked Market is Finalized, `0` it not.

#### augur.api.ReportingWindow.isOver({ reportingwindow }[, callback])

Returns whether the 30-day reporting period for the specified [Reporting Window](#reporting-window) has ended.

#### augur.api.ReportingWindow.isReportingActive({ reportingWindow }[, callback])

Returns whether the [Reporting Window](#reporting-window) is currently in it's [Reporting Phase](#reporting-phase) or not. The Reporting Phase lasts 27 days at the start of a Reporting Window and is followed by a 3 day [Dispute Phase](#dispute-phase). Returns `1` if the Reporting Window's Reporting Phase is active, `0` if not.

Reputation Token Call API
-------------------------
```javascript
// Reputation Token Contract Call API Examples:
var reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";

augur.api.ReputationToken.getUniverse({ reputationToken: reputationToken }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReputationToken.getTopMigrationDestination({ reputationToken: reputationToken }, function (topMigrationDestination) { /* ... */ })
// example output:
topMigrationDestination = "0x1aa30942000ac72dee6580e1ac32d1d01ac1af00"
```
#### [Reputation Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/ReputationToken.sol)

The Reputation Token, known as [REP](#rep), is the key that allows Augur's Decentralized Oracle System to function, and by extension the entirety of Augur. REP has three major functions, it's used to [Report](#report) on the [Outcome](#outcome) of a [Market](#market), [Challenge](#challenge) the [Proposed Outcome](#proposed-outcome) of a Market, and as a bond when creating a Market. [Reporters](#reporter) stake REP on the Outcome of a Market as a show of confidence in their Report. If the Reporter correctly staked on the [Final Outcome](#final-outcome) of the Market they can claim their REP back, earn [Reporting Fees](#reporting-fee) proportional to their staked REP, and a portion of the REP incorrectly staked on other Outcomes.

REP is also used to Challenge the Proposed Outcome of Reports during the [Designated Dispute Phase](#designated-dispute-phase) or the [Dispute Phase](#dispute-phase). If the Challenge successfully changes the Proposed Outcome of a Market and that outcome becomes the Final Outcome, the [Dispute Bond](#dispute-bond) holder can redeem the bond for up to double the REP it cost to place. When creating a Market, the Market Creator is required to specify a Designated Reporter and pay a [First Report REP Bond](#first-report-rep-bond) to ensure the Designated Reporter shows up. If the Reporter doesn't show up, the Market Creators First Report REP Bond will go to the First Reporter to Report on the Market. Their GAS cost for the Report transaction will be covered by the Market Creator's [First Report GAS Bond](#first-report-gas-bond) and the First Report REP Bond is added to whatever the First Reporter staked, there by improving her stake and potential rewards if correctly staked.

The Reputation Token is an ERC-20 token that implements all of the required functions listed in the [ERC-20 Token Standard] (https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md). It does not, however, implement the optional functions.

#### augur.api.ReputationToken.getUniverse({ reputationToken }[, callback])

Returns the [Universe](#universe) address for the [REP](#rep) address provided. All of Augur's information belongs to a Universe and each Universe has it's own REP contract. New Universes are created in the event of a [Fork](#fork).

#### augur.api.ReputationToken.getTopMigrationDestination({ reputationToken }[, callback])

As mentioned in the previous method description, all of Augur's [Universes](#universe) have their own [REP](#rep) contract, specific to that Universe. In the event of a [Fork](#fork) occurring, REP holders are expected to migrate their REP to one of the newly created [Child Universes](#child-universe). The Child Universe with the most REP migrated to it at the end of the 60 day [Fork Period](#fork-period) will be the only Universe that allows for [Settlement](#settlement) on the [Forked Market](#forked-market) and will be the Universe that all pending [Markets](#market) will migrate to for [Reporting](#report). What this method does is returns the Universe address of the currently "winning" migration destination. In other words, this returns the Universe with the most REP migrated to it so far.

Share Token Call API
--------------------
```javascript
// Share Token Contract Call API Examples:
var shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";

augur.api.ShareToken.getMarket({ shareToken: shareToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.ShareToken.getOutcome({ shareToken: shareToken }, function (outcome) { /* ... */ })
// example output:
outcome = "1"

```
#### [Share Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/ShareToken.sol)

The Share Token is an ERC-20 token that implements all of the required functions listed in the [ERC-20 Token Standard] (https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md). It does not, however, implement the optional functions.

#### augur.api.ShareToken.getMarket({ shareToken }[, callback])

Returns the market address for the specified `shareToken`.

#### augur.api.ShareToken.getOutcome({ shareToken }[, callback])

Returns the Outcome of the market that the specified `shareToken` is for.

Stake Token Call API
------------------------
```javascript
// Stake Token Contract Call API Examples:
var stakeToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";

augur.api.StakeToken.getMarket({ stakeToken: stakeToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.StakeToken.getPayoutDistributionHash({ stakeToken: stakeToken }, function (payoutDistributionHash) { /* ... */ })
// example output:
payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.StakeToken.getPayoutNumerator({
  stakeToken: stakeToken,
  index: 0
}, function (payoutSetValue) { /* ... */ })
// example output:
payoutSetValue = "1000"

augur.api.StakeToken.getStakeWindow({ stakeToken: stakeToken }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.StakeToken.getReputationToken({ stakeToken: stakeToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.StakeToken.getUniverse({ stakeToken: stakeToken }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.StakeToken.isValid({ stakeToken: stakeToken }, function (isValid) { /* ... */ })
// example output:
isValid = "1"
```

#### [Stake Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/StakeToken.sol)

The Stake Token is used to represent Staked [REP](#rep) by a [Reporter](#reporter) for a specific [Report](#report) and [Market](#market). When a Reporter submits a Report during a [Reporting Round](#reporting-round) for this Market they need to stake REP based on how confident they are in the Report. The REP staked is converted into Stake Tokens. A new type of Stake Token is created for each [Payout Set](#payout-set) submit by Reporters. Stake Tokens are then redeemed after [Finalizing the Market](#finalized-market). If your Stake Tokens are the tokens that represent the Winning Payout Set then your Stake Tokens can be redeemed for the amount of REP that was staked as well as a portion of [Reporting Fee](#reporting-fee) and REP staked on any other [Outcome](#outcome) but the [Final Outcome](#final-outcome). If your tokens are Staked on any other Outcome they are worthless and the Staked REP is redistributed to people with winning Stake Tokens.

The Stake Token is an ERC-20 token that implements all of the required functions listed in the [ERC-20 Token Standard] (https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md). It does not, however, implement the optional functions.

#### augur.api.StakeToken.getMarket({ stakeToken }[, callback])

This method will return the [Market](#market) address that the Stake Token belongs too. Every Stake Token belongs to a specific Market in which the Token acts as a 1 to 1 representation of Staked [REP](#rep) for a particular [Payout Set](#payout-set) representing a [Report](#report) for the Market.

#### augur.api.StakeToken.getPayoutDistributionHash({ stakeToken }[, callback])

Returns the [Payout Distribution Hash](#payout-distribution-hash) for a specific `stakeToken`. A Payout Distribution Hash is a sha3 hash of the [Payout Set](#payout-set) the Stake Token represents. The Payout Distribution Hash is used in other contract methods to identify a particular Payout Set.

#### augur.api.StakeToken.getPayoutNumerator({ stakeToken, index }[, callback])

Returns the value at the `index` of the [Payout Set](#payout-set) represented by this `stakeToken`. The `index` is a value between `0` and number of [Outcomes](#outcome) for the [Market](#market) and corresponds to a particular Outcome of the Market. In a [Binary Market](#binary-market) for example, there are only two Outcomes, so the allowable `index` values are 0 and 1.

#### augur.api.StakeToken.getReportingWindow({ stakeToken }[, callback])

Returns the [Reporting Window](#reporting-window) address for the specified `stakeToken`. All Stake Tokens belong to a specific [Market](#market) and all Market's belong to a specific Reporting Window. This method will return the Reporting Window that contains the Market that these Stake Tokens were purchased for.

#### augur.api.StakeToken.getReputationToken({ stakeToken }[, callback])

As mentioned above, a Stake Token belongs to a [Market](#market) which in turn belongs to a [Reporting Window](#reporting-window). Reporting Windows only interact with a single [REP](#rep) contract, and this method will return the contract address of valid REP for this Reporting Window and by extension these Stake Tokens. Stake Tokens can only be purchased with REP that belongs to the Reporting Window the tokens are ultimately contained within.

#### augur.api.StakeToken.getUniverse({ stakeToken }[, callback])

Returns the [Universe](#universe) address for the specified `stakeToken`'s [Reporting Window](#reporting-window). Everything in Augur belongs to a Universe, and Stake Tokens are no different. New Universes are created in the event of a [Fork](#fork), so this method provides an API user the ability to find out which Universe the Stake Token is currently in.

#### augur.api.StakeToken.isValid({ stakeToken }[, callback])

This method is used as a way to check if the `stakeToken` represents a [Payout Set](#payout-set) that indicates the [Market](#market) is Invalid. This isn't to say that the Stake Token, the [Report](#report), or the Payout Set is invalid, but rather the [Outcome](#outcome) of the Market is unclear based on how the Market is worded or the result of Market doesn't correspond to any of the Outcomes set for the Market.

Universe Call API
---------------
```javascript
// Universe Contract Call API Examples:
var universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5";
var _parentPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";

augur.api.Universe.getChildUniverse({
  universe: universe,
  _parentPayoutDistributionHash: _parentPayoutDistributionHash
}, function (childUniverse) { /* ... */ })
// example output:
childUniverse = "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c"

augur.api.Universe.getCurrentReportingWindow({ universe: universe }, function (currReportingWindow) { /* ... */ })
// example output:
currReportingWindow = "578"

augur.api.Universe.getForkEndTime({ universe: universe }, function (forkEndTime) { /* ... */ })
// example output:
forkEndTime = "1489855429";

augur.api.Universe.getForkingMarket({ universe: universe }, function (forkingMarket) { /* ... */ })
// example output:
forkingMarket = "0x78f7b43150d27c464359e735781c16ac585f52a8";

augur.api.Universe.getNextReportingWindow({ universe: universe }, function (nextReportingWindow) { /* ... */ })
// example output:
nextReportingWindow = "579"

augur.api.Universe.getParentUniverse({ universe: universe }, function (parentUniverse) { /* ... */ })
// example output:
parentUniverse = "0x63c59544b89cce1dd53b1b566862189b25adec41"

augur.api.Universe.getParentPayoutDistributionHash({ universe: universe }, function (universeParentPayoutDistributionHash) { /* ... */ })
// example output:
universeParentPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Universe.getPreviousReportingWindow({ universe: universe }, function (previousReportingWindow) { /* ... */ })
// example output:
previousReportingWindow = "577"

augur.api.Universe.getReportingPeriodDurationInSeconds({ universe: universe }, function (reportingPeriodDuration) { /* ... */ })
// example output:
reportingPeriodDuration = "2592000";

augur.api.Universe.getReportingWindow({ universe: universe, _reportingWindowId: 579 }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x1f90cc6b4e89303e451c9b852827b5791667f570";

var _endTime = 2524608000;
augur.api.Universe.getReportingWindowByMarketEndTime({
  universe: universe,
  _endTime: _endTime
}, function (reportingWindowByEndTime) { /* ... */ })
// example output:
reportingWindowByEndTime = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.Universe.getReportingWindowByTimestamp({
  universe: universe,
  _timestamp: _endTime
}, function (reportingWindowByTimestamp) { /* ... */ })
// example output:
reportingWindowByTimestamp = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.Universe.getReportingWindowId({
  universe: universe,
  _timestamp: new Date().getTime()
}, function (reportingWindowId) { /* ... */ })
// example output:
reportingWindowId = "578";

augur.api.Universe.getReputationToken({ universe: universe }, function (reputationTokenAddress) { /* ... */ })
// example output:
reputationTokenAddress = "0x2fb561b2bdbcd1ae1995bdd6aff6776d6f4292f2";

var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.Universe.isContainerForMarket({
  universe: universe,
  _shadyMarket: market
}, function (isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1";

var stakeToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
augur.api.Universe.isContainerForStakeToken({
  universe: universe,
  _shadyStakeToken: stakeToken
}, function (isContainerForStakeToken) { /* ... */ })
// example output:
isContainerForStakeToken = "1";

var reportingWindow = "578";
augur.api.Universe.isContainerForReportingWindow({
  universe: universe,
  _shadyReportingWindow: reportingWindow
}, function (isContainerForReportingWindow) { /* ... */ })
// example output:
isContainerForReportingWindow = "1";

var childUniverse = "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c";
augur.api.Universe.isParentOf({
  universe: universe,
  _shadyChild: childUniverse
}, function (isParentOf) { /* ... */ })
// example output:
isParentOf = "1";
```
#### [Universe Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Universe.sol)

The Universe Contract is the contract that defines an Augur [Universe](#universe) and the methods used to interact with them. All of Augur's [Markets](#market), [Order Books](#order-book), [Reporting Windows](#reporting-window), and [REP](#rep) belong to a specific Universe. In the rare event that an [Last Report Round](#last-report-round) Market's [Proposed Outcome](#proposed-outcome) is [Challenged](#challenge) during the [Dispute Phase](#dispute-phase) of a Reporting Window then a [Fork](#fork) will occur and new Universes will be created. The Universe that originally contained the [Forked Market](#forked-market) will become a [Locked Universe](#locked-universe), thereby not allowing any Market creation to take place in the Locked Universe. The newly created Universes are known as [Child Universes](#child-universe), where as the original and now Locked Universe is considered those Child Universes' [Parent Universe](#parent-universe).

#### augur.api.Universe.getChildUniverse({ universe, \_parentPayoutDistributionHash }[, callback])

Returns the Child [Universe](#universe) address for this `universe` address who's [Forked Market](#forked-market)'s [Final Outcome](#final-outcome) is the `_parentPayoutDistributionHash` [Payout Distribution Hash](#payout-distribution-hash) provided.

#### augur.api.Universe.getCurrentReportingWindow({ universe }[, callback])

Returns the current running [Reporting Window](#reporting-window)'s Id belonging to the `universe` specified. Every [Universe](#universe) has Reporting Windows that are continually run for a duration of 30 days before immediately starting the next Window.

#### augur.api.Universe.getForkEndTime({ universe }[, callback])

Returns the timestamp for when the [Fork Period](#fork-period) ends that was started on the `universe` specified.

#### augur.api.Universe.getForkingMarket({ universe }[, callback])

Returns the contract address of the [Market](#market) that the specified `universe` is [Forking](#fork) over. This returns 0 if the Universe has never forked and there is no [Forked Market](#forked-market).

#### augur.api.Universe.getNextReportingWindow({ universe }[, callback])

Returns the next [Reporting Window](#reporting-window) Id coming up after the current Reporting Window ends in a specific `universe`.

#### augur.api.Universe.getParentUniverse({ universe }[, callback])

Returns the [Parent Universe](#parent-universe) address of the specified `universe`. When a [Fork](#fork) occurs, [Child Universes](#child-universe) are created and the original [Universe](#universe) that contained the [Forking Market](#forked-market) would become a Parent Universe to the newly created Universes. If this is called on the very first Augur Universe then it will return 0, as the first Universe had no Parent Universe.

#### augur.api.Universe.getParentPayoutDistributionHash({ universe }[, callback])

Returns the [Parent Universes](#parent-universe)'s [Payout Distribution Hash](#payout-distribution-hash) given the `universe` address of a [Child Universe](#child-universe). The Payout Distrubution Hash is a hash of the [Disputed](#dispute-bond) [Outcome](#outcome) of the [Forked Market](#forked-market).

#### augur.api.Universe.getPreviousReportingWindow({ universe }[, callback])

Returns the previous [Reporting Window](#reporting-window) Id to the current Reporting Window for the specified `universe`.

#### augur.api.Universe.getReportingPeriodDurationInSeconds({ universe }[, callback])

Returns the specified `universe`'s [Reporting Window](#reporting-window) full duration in seconds, also known as the [Reporting Period](#reporting-period). This includes both the [Reporting Phase](#reporting-phase) and [Dispute Phase](#dispute-phase) of the Reporting Window. The Reporting Phase should last 27 days followed by a 3 day Dispute Phase which makes the Reporting Period Duration a total of 30 days, or 2,592,000 seconds.

#### augur.api.Universe.getReportingWindow({ universe, \_reportingWindowId }[, callback])

Returns the [Reporting Window](#reporting-window) Contract Address belonging to this `universe` that matches the `_reportingWindowId` passed to the function.

#### augur.api.Universe.getReportingWindowByMarketEndTime({ universe, \_endTime }[, callback])

Returns the [Reporting Window](#reporting-window) address on the specific `universe` given an `_endTime`.

#### augur.api.Universe.getReportingWindowByTimestamp({ universe, \_timestamp }[, callback])

Returns the [Reporting Window](#reporting-window) Contract Address for a specific `universe` and provided `_timestamp`.

#### augur.api.Universe.getReportingWindowId({ universe, \_timestamp }[, callback])

Returns the [Reporting Window](#reporting-window) Id for a specific `universe` and provided `_timestamp`. This is calculated by dividing the timestamp by the [Universes'](#universe) Reporting Window duration in seconds.

#### augur.api.Universe.getReputationToken({ universe }[, callback])

Returns the [Reputation Token](#rep) Address for a specific `universe`. This is the REP usable within this Universe.

#### augur.api.Universe.isContainerForMarket({ universe, \_shadyMarket }[, callback])

Returns whether the specific `universe` is a container for the [Market](#market) `_shadyMarket` address provided. Returns `1` if true, `0` if false. All Markets are created within a [Universe](#universe), and this function is used to help confirm if a Market is actually attached to the Universe in question.

#### augur.api.Universe.isContainerForStakeToken({ universe, \_shadyStakeToken }[, callback])

Returns whether the specific `universe` is a container for the [Reporting](#reporting) Token `_shadyStakeToken` address provided. Returns `1` if true, `0` if false. [Stake Tokens](#stake-tokens) are 1:1 exchangeable for [REP](#rep) by [Reporters](#reporter) to represent their staked REP for a [Report](#report). This method is designed determine whether the Universe contains the Stake Token specified.

#### augur.api.Universe.isContainerForReportingWindow({ universe, \_shadyReportingWindow }[, callback])

Returns whether the specific `universe` is a container for the [Reporting Window](#reporting-window) `_shadyReportingWindow` Contract Address provided. Returns `1` if true, `0` if false. Every Reporting Window belongs to a Universe and this method is used to see if a specific Reporting Window Address belongs to the Universe in question.

#### augur.api.Universe.isParentOf({ universe, \_shadyChild }[, callback])

Returns whether the specific `universe` is a container for the `_shadyChild` [Child Universe](#child-universe) Address provided. Returns `1` if true, `0` if false. If you want to see if a specific [Universe](#universe) is the [Parent Universe](#parent-universe) to a Child Universe you would use this function to determine that.
