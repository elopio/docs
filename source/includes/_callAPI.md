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

Augur's Call API is made up of "getter" methods that retrieve information from the blockchain (using Ethereum's `eth_call` RPC) but does not write information to the blockchain. The Call API is for more granular "gets" than the [Simplified API](#simplified-api) allows for. The Call API is directly mapped to the Augur Contracts and their publicly exposed methods. All Call API functions will accept two arguments, a `params` object with key/value pairs that match inputs for the contract method and a callback function. The Augur API functions are attached to the `augur.api` object and follow a pattern of `augur.api.<Contract Name>.<Contract Method>(<Params Object>, <Callback Function>)`. Although technically optional, the Augur team **strongly recommends** using a callback. Without the callback the calls will be synchronous which can lock up your browser until they complete.

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

augur.api.DisputeBond.getUniverse({ tx: { to: disputeBond } }, function (error, universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"
```
#### [Dispute Bond Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/DisputeBond.sol)

The [Dispute Bond](#dispute-bond) Token is used by [REP](#rep) holders to [Challenge](#challenge) the [Outcome](#outcome) of [Markets Awaiting Finalization](#market-awaiting-finalization). The Dispute Bond is only purchasable with REP. Only one Dispute Bond needs to be purchased for a Market per round of [Reporting](#reporting) in order for the Market to move to the next state of [Reporting](#report) in the next upcoming [Reporting Window](#reporting-window). If a Challenge is successful, which means the [Final Outcome](#final-outcome) of the [Market](#market) is something other than the [Tentative Outcome](#tentative-outcome) that was disputed, then the Bond Holder is entitled to up to 2x the Dispute Bond cost in REP. After 2x the cost of the Dispute Bond is paid to the Bond Holder any remaining REP is redistributed as normal to other Reporters who correctly staked on the Final Outcome.

#### augur.api.DisputeBond.getBondRemainingToBePaidOut({ tx }[, callback])

This transaction will return the amount of REP, denoted in attotokens, of bond remaining to be paid out for a specified `disputeBond`. Bonds are paid back to the Bond Holder if they correctly [Challenged](#challenge) a [Tentative Outcome](#tentative-outcome) and the [Final Outcome](#final-outcome) for the [Market](#market) is something different than the Challenged Tentative Outcome. Otherwise, the Bond is paid out to [Reporters](#reporter) who confirmed the Tentative Outcome to be the Final Outcome of the Market.

#### augur.api.DisputeBond.getDisputedPayoutDistributionHash({ tx }[, callback])

This transaction will return the [Payout Distribution Hash](#payout-distribution-hash) for the dispute payout given a specified `disputeBond`. The Payout Distribution Hash in this case refers the Hash that was the [Tentative Outcome](#tentative-outcome) but was [Challenged](#challenged) by the [Dispute Bond](#dispute-bond).

#### augur.api.DisputeBond.getMarket({ tx }[, callback])

This transaction will return the [Market](#market) that a specified `disputeBond` belongs to. All Dispute Bonds belong to a Market, and there can only be one Dispute Bond per [Dispute Round Phase](#dispute-round-phase) for that Market.

#### augur.api.DisputeBond.getUniverse({ tx }[, callback])

This transaction will return the [Universe](#universe) that contained the [Market](#market) that a specified `disputeBond` belongs to.

Market Call API
----------------
```javascript
// Market Contract Call API Examples:
var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Market.designatedReporterShowed({ tx: { to: market } }, function (error, designatedReporterShowed) { console.log(designatedReporterShowed); })
// example output:
"1"

augur.api.Market.getBestGuessSecondPlaceTentativeWinningPayoutDistributionHash({ tx: { to: market } }, function (error, secondPlaceTentativeWinningPayoutDistributionHash) { console.log(secondPlaceTentativeWinningPayoutDistributionHash); })
// example output:
"0x20acbb1199c43cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Market.getDenominationToken({ tx: { to: market } }, function (error, denominationToken) { console.log(denominationToken); })
// example output:
"0x30e3852448f4ab5d62bbf7544ca3c92daca5c957"

augur.api.Market.getDesignatedReporter({ tx: { to: market } }, function (error, designatedReporter) { console.log(designatedReporter); })
// example output:
"0xca3edca4ed326bbcb77e914b379913b12d49654d"

augur.api.Market.getDesignatedReporterDisputeBond({ tx: { to: market } }, function (error, designatedReporterDisputeBond) { console.log(designatedReporterDisputeBond); })
// example output:
"0xe783e32cfeea2d2be8df1ae6e978e6d4de63fe26"

augur.api.Market.getDesignatedReportPayoutHash({ tx: { to: market } }, function (error, designatedReportPayoutHash) { console.log(designatedReportPayoutHash); })
// example output:
"0x58aff2018af3cb2ca3a3a1862df2d350300904a96039eb50cb0fd1ac3710aad"

augur.api.Market.getDesignatedReportReceivedTime({ tx: { to: market } }, function (error, designatedReportReceivedTime) { console.log(designatedReportReceivedTime); })
// example output:
"1500388800";

augur.api.Market.getEndTime({ tx: { to: market } }, function (error, endTime) { console.log(endTime); })
// example output:
"1500388730";

augur.api.Market.getExtraDisputeBondRemainingToBePaidOut({ tx: { to: market } }, function (error, extraDisputeBondRemainingToBePaidOut) { console.log(extraDisputeBondRemainingToBePaidOut); })
// example output:
"1000";

augur.api.Market.getFeeWindow({ 
  tx: { to: market }, 
  _feeWindowId: 578,
}, function (error, feeWindow) { console.log(feeWindow); });
// example output:
"0x1f90cc6b4e89303e451c9b852827b5791667f570"

augur.api.Market.getFinalizationTime({ tx: { to: market } }, function (error, finalizationTime) { console.log(finalizationTime); })
// example output:
"1500647930";

augur.api.Market.getFinalPayoutDistributionHash({ tx: { to: market } }, function (error, finalPayoutDistributionHash) { console.log(finalPayoutDistributionHash); })
// example output:
"0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Market.getFinalWinningStakeToken({ tx: { to: market } }, function (error, winningStakeToken) { console.log(winningStakeToken); })
// example output:
"0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.Market.getForkingMarket({ tx: { to: market } }, function (error, forkedMarket) { console.log(forkedMarket); })
// example output:
"0x0"

augur.api.Market.getMarketCreatorMailbox({ tx: { to: market } }, function (error, marketCreatorMailbox) { console.log(marketCreatorMailbox); })
// example output:
"0xeabdeaefcfaf7ea1e17c4768a554d5780069eabd"

augur.api.Market.getMarketCreatorSettlementFeeDivisor({ tx: { to: market } }, function (error, marketCreatorSettlementFee) { console.log(marketCreatorSettlementFee); })
// example output:
"20000000000000000"

augur.api.Market.getOwner({ tx: { to: market } }, function (error, owner) { console.log(owner); })
// example output:
"0x06cbcd92af2571f1419b622a794d65db524f682b"

augur.api.Market.getNumberOfOutcomes({ tx: { to: market } }, function (error, numOutcomes) { console.log(numOutcomes); })
// example output:
"2"

augur.api.Market.getNumTicks({ tx: { to: market } }, function (error, numTicks) { console.log(numTicks); })
// example output:
"1000"

augur.api.Market.getReportingState({ tx: { to: market } }, function (error, reportingState) { console.log(reportingState); })
// example output:
"0";

augur.api.Market.getReputationToken({ tx: { to: market } }, function (error, reputationToken) { console.log(reputationToken); })
// example output:
"0x23b17188ce3c491f6ab4427258d92452be5c8045"

augur.api.Market.getShareToken({
  tx: { to: market },
  _outcome: 1
}, function (error, shareToken) { console.log(shareToken); })
// example output:
"0x18b17188ce3c491f6ab4427258d92452be5c8054"

var _payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";
augur.api.Market.getStakeTokenOrZeroByPayoutDistributionHash({ tx: { to: market }, _payoutDistributionHash: _payoutDistributionHash }, function (error, stakeTokenOrZero) { console.log(stakeTokenOrZero); })
// example output:
"0x5caa66408617f77601d0dc19c163621e7f4b8b38"

augur.api.Market.getTentativeWinningPayoutDistributionHash({ tx: { to: market } }, function (error, tentativeWinningPayoutDistributionHash) { console.log(tentativeWinningPayoutDistributionHash); })
// example output:
"0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.Market.getTotalStake({ tx: { to: market } }, function (error, totalStake) { console.log(totalStake); })
// example output:
"1000"

augur.api.Market.getTotalWinningDisputeBondStake({ tx: { to: market } }, function (error, totalWinningDisputeBondStake) { console.log(totalWinningDisputeBondStake); })
// example output:
"1000"

augur.api.Market.getUniverse({ tx: { to: market } }, function (error, universe) { console.log(universe); })
// example output:
"0x0920d1513057572be46580b7ef75d1d01a99a3e5"

var stakeToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
augur.api.Market.isContainerForStakeToken({
  tx: { to: market },
  _shadyStakeToken: stakeToken
}, function (error, isContainerForStakeToken) { console.log(isContainerForStakeToken); })
// example output:
"1"

var shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
augur.api.Market.isContainerForShareToken({
  tx: { to: market },
  _shadyShareToken: shareToken
}, function (error, isContainerForShareToken) { console.log(isContainerForShareToken); })
// example output:
"1"

augur.api.Market.isInvalid({
  tx: { to: market }
}, function (error, isInvalid) { console.log(isInvalid); })
// example output:
"1"
```
#### [Market Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Market.sol)

#### augur.api.Market.designatedReporterShowed({ tx }[, callback]) 

Returns `1` if the [Designated Reporting Phase](#designated-reporting-phase) of the [Market](#market) has passed and the [Designated Reporter](#designated-reporter) submitted a Report, `0` otherwise.

#### augur.api.Market.getBestGuessSecondPlaceTentativeWinningPayoutDistributionHash({ tx }[, callback])

Returns the second best, or second place, tentatively winning [Payout Distribution Hash](#payout-distribution-hash). The [Payout Set](#payout-set) [Reported](#report) by a [REP](#rep) holder is converted into a sha3 hash known as the Payout Distribution Hash. This method returns the Payout Set Hash with the 2nd most REP staked on it while the [Market](#market) is still [Awaiting Finalization](#market-awaiting-finalization).

#### augur.api.Market.getDenominationToken({ tx }[, callback])

Returns the address of the token used to denominate the specified `market`. A Denomination Token is the ERC20 Token used as the currency to trade on the [Outcome](#outcome) of a [Market](#market). Currently, this will always return the address of a "Cash" contract; however, Augur will eventually support other types of Denomination Tokens.

#### augur.api.Market.getDesignatedReporter({ tx }[, callback])

Returns the address for the [Designated Reporter](#designated-reporter) set for the specified `market`. Every [Market](#market) is required to have an assigned Designated Reporter, which is set by the [Market Creator](#market-creator) during Market Creation.

#### augur.api.Market.getDesignatedReporterDisputeBond({ tx }[, callback])

Returns the [Dispute Bond](#dispute-bond) Token address for the [Designated Reporting](#designated-reporting) round for a specified `market`. When a Designated [Report](#report) is [Challenged](#challenge) a Dispute Bond is created and this function is designed to return that bond address. This returns 0 if the [Market](#market) doesn't have a Designated Report or if the Designated Report hasn't been Challenged.

#### augur.api.Market.getDesignatedReportPayoutHash({ tx }[, callback])

Returns the [Payout Distribution Hash](#payout-distribution-hash) of the [Payout Set](#payout-set) submit by the [Designated Reporter](#designated-reporter) for their [Report](#report). The Payout Set is used to determine how the [Market](#market) should [Settle](#settlement) for particular [Shares](#share) after being [Finalized](#finalized-market).

#### augur.api.Market.getDesignatedReportReceivedTime({ tx }[, callback])

Returns the timestamp of when the [Designated Reporter](#designated-reporter)'s [Report](#report) was submit for the specified `market`. Each [Market](#market) is required to have a Designated Reporter who is expected to Report during the [Designated Reporting Phase](#designated-reporting-phase). This timestamp is used to determine if they actually submit their Report on time, which would be anytime within 3 days of the Market's [End Time](#end-time).

#### augur.api.Market.getEndTime({ tx }[, callback])

Returns the timestamp for when the specified `market`'s event has come to pass. When the [Market](#market)'s [End Time](#end-time) passes, the Market enters the [Designated Reporting Phase](#designated-reporting-phase).

#### augur.api.Market.getExtraDisputeBondRemainingToBePaidOut({ tx }[, callback])

Returns the amount of [REP](#rep) that is available in a [Market](#market) to be migrated to other [Universes](#universe) to reward [Dispute Bond](#dispute-bond) holders.

#### augur.api.Market.getFeeWindow({ tx, \_feeWindowId  }[, callback])

Returns the Ethereum contract address of the Fee Window `_feeWindowId` in a [Market](#market), as a hexidecimal string.

#### augur.api.Market.getFinalizationTime({ tx }[, callback])

Returns the timestamp for when the specified `market` was [Finalized](#finalized-market). A Finalized Market has a [Final Outcome](#final-outcome) set from successful [Market Resolution](#market-resolution) which cannot be [Challenged](#challenge) and the Market is considered [Settled](#settlement).

#### augur.api.Market.getFinalPayoutDistributionHash({ tx }[, callback])

Returns the final [Payout Distribution Hash](#payout-distribution-hash) for a specified `market`. The Final Payout Distribution Hash determines how a [Finalized Market](#finalized-market) payouts out for a specific [Share](#share) of an [Outcome](#outcome).

#### augur.api.Market.getFinalWinningStakeToken({ tx }[, callback])

Returns the Stake Token address for the [Final Outcome](#final-outcome) of a specific `market`. If the [Market](#market) isn't [Settled](#market-settlement) then this will return a null address, `0x0`.

#### augur.api.Market.getForkingMarket({ tx }[, callback])

Returns the [Market](#market) address of the [Forked Market](#forked-market) for the [Universe](#universe) that contains the specified `market` address. If the `market` address specified belongs in a Universe that hasn't had a [Fork](#fork) take place, this will return `0x0`.

#### augur.api.Market.getMarketCreatorMailbox({ tx }[, callback])

Returns the address of the [Market Creator Mailbox](#market-creator-mailbox) for `market`. [Market Creators](#market-creator) can use this address to collect their fees.

#### augur.api.Market.getMarketCreatorSettlementFeeDivisor({ tx }[, callback])

Returns the [Creator Fee](#creator-fee) set by the [Market Creator](#market-creator), denominated in attotokens per [Settlement](#settlement) of a [Complete Set](#complete-set), for the `market`.

#### augur.api.Market.getNumberOfOutcomes({ tx }[, callback])

Returns the number of [Outcomes](#outcome) for a specified `market`. The number of outcomes is the number of potential results of the [Market](#market).

#### augur.api.Market.getNumTicks({ tx }[, callback])

Returns the [Number of Ticks](#number-of-ticks) set for a specific `market`. The Number of [Ticks](#ticks) represents the number of valid price points between the [Market](#market)'s [Minimum Price](#minimum-display-price) and [Maximum Price](#maximum-display-price).

#### augur.api.Market.getOwner({ tx }[, callback])

Returns the address of the specified [Market's](#market) owner.

#### augur.api.Market.getPayoutDistributionHashStake({ tx, \_payoutDistributionHash }[, callback])

Returns the staked amount of [REP](#rep), in attorep, for a specified `_payoutDistributionHash` of a `market`. [Reporters](#reporter) who stake REP on the same [Outcome](#outcome) during the [Reporting Phase](#reporting-phase) will have identical [Payout Distribution Hashs](#payout-distribution-hash). This method returns the total amount of staked REP for a specific Payout Distribution Hash. The Hash with the most REP staked on it is considered the [Tentative Outcome](#tentative-outcome) while the [Market](#market) is [Awaiting Finalization](#awaiting-finalization). If the [Dispute Round Phase](#dispute-round-phase) passes without a [Challenge](#challenge) to the Tentative Outcome then the Market is [Finalized](#finalized-market) and the Tentative Outcome becomes the [Final Outcome](#final-outcome).

#### augur.api.Market.getReportingState({ tx }[, callback])

Returns the current [Reporting](#report) State that the `market` is in. This method returns a number between 0 and 12, which corresponds to the various Reporting States outlined in the [Reporting Section](#reporting) of the documentation.

#### augur.api.Market.getReputationToken({ tx }[, callback])

Returns the Ethereum contract address of the [Reputation Token (REP)](#rep) for the specified [Market](#market). REP is Staked whenever a First Report is submitted or when users attempt to Challenge the Tentative Outcome of a Market. A Market only accepts one REP contract as the source of Staked REP, and this method returns that contract's address.

#### augur.api.Market.getShareToken({ tx, \_outcome }[, callback])

Returns the [Share](#share) Token's address for the specified `market` and `_outcome`. Every [Outcome](#outcome) of a [Market](#market) has a separate Share Token used to handle trading around that Outcome and this method returns the contract address of the Share Tokens for the specified Outcome.

#### augur.api.Market.getStakeTokenOrZeroByPayoutDistributionHash({ tx, \_payoutDistributionHash }[, callback])

Returns a Stake Token address, or 0 if there is no Stake Token for a specific `_payoutDistributionHash` for a `market`. [Payout Sets](#payout-set) that are valid, that is that they sum of the values in the array equal the [Number of Ticks](#number-of-ticks) for the [Market](#market) and no individual value in the array is greater than the Number of [Ticks](#ticks), become [Payout Distribution Hashes](#payout-distribution-hash). This method returns the Stake Token given a Payout Distribution Hash, much like `getOrCreateStakeToken` returns the Stake Token given a Payout Set.

#### augur.api.Market.getTentativeWinningPayoutDistributionHash({ tx }[, callback])

Returns the tentatively winning [Payout Distribution Hash](#payout-distribution-hash) given a specified `market`. The tentatively winning Payout Distribution Hash is the hash with the most [REP](#rep) staked on it prior to [Market Finalization](#finalized-market). Once the [Market](#market) Finalized the tentatively winning Payout Distribution Hash becomes the Winning Payout Distribution Hash.

#### augur.api.Market.getTotalStake({ tx }[, callback])

Returns the total amount staked on the specified [Market](#market).

#### augur.api.Market.getTotalWinningDisputeBondStake({ tx }[, callback])

Returns the sum of all [Dispute Bonds](#dispute-bond) staked on challenging a [Payout Distribution Hash](#payout-distribution-hash) other than the final Payout Distribution Hash for the specfied [Market](#market).

#### augur.api.Market.getUniverse({ tx }[, callback])

Returns the [Universe](#universe) address of the Universe that the specified `market` is contained within. All [Markets](#market) are created on a specific Universe and new Universes can be created in the event of a [Fork](#fork).

#### augur.api.Market.isContainerForStakeToken({ tx, \_shadyStakeToken }[, callback])

Returns whether the specific `market` is a container for the `_shadyStakeToken` address provided. The `_shadyStakeToken` address will return true if the address is a Stake Token that belongs to the [Market](#market), otherwise false will be returned. Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForShareToken({ tx, \_shadyShareToken }[, callback])

Returns whether the specific `market` is a container for the `_shadyShareToken` address provided. The `_shadyShareToken` address will return true if it's a [Share](#share) Token belonging to the [Market](#market), otherwise it will return false if the address is not a Share Token belonging to this Market. Returns `1` if true, `0` if false.

#### augur.api.Market.isInvalid({ tx }[, callback])

Returns `1` if the [Market](#market) passed as `market` has been [Finalized](#finalized-market) as [Invalid](#invalid-outcome), or returns `0` otherwise. This means the Market is unable to be Finalized to a single [Final Outcome](#final-outcome) due to an unclear Market or an indeterminate [Outcome](#outcome).

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

Returns the amount of [Shares](#share) requested on a specified `_orderId`.

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

Every [Reporting Window](#reporting-window) consists of two phases, the [Reporting Phase](#reporting-phase) and the [Dispute Round Phase](#dispute-round-phase). This method is used to return the timestamp for the end of the Reporting Window's Dispute Round Phase.

#### augur.api.ReportingWindow.getDisputeStartTime({ tx }[, callback])

As stated above, [Reporting Windows](#reporting-window) have two phases, the [Reporting Phase](#reporting-phase) and [Dispute Round Phase](#dispute-round-phase) and this method returns the timestamp of the start of the Dispute Round Phase.

#### augur.api.ReportingWindow.getEndTime({ tx }[, callback])

Returns a timestamp for when this [Reporting Window](#reporting-window) will end. A Reporting Window is considered active for a total of 30 days, then ends, and is no longer considered to be active.

#### augur.api.ReportingWindow.getNumDesignatedReportNoShows({ tx }[, callback])

Returns the number of [Markets](#market) belonging to this [Reporting Window](#reporting-window) in which the [Designated Reporter](#designated-reporter) failed to [Report](#report) during the [Designated Reporting Phase](#designated-reporting-phase). These Markets will have a [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) and [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) up for grabs for the [First Reporter](#first-reporter) because these Markets have yet to receive a Report. This only includes Markets where Designated Reporters failed to Report, and does not include Markets where the Designated Reporter's [Tentative Outcome](#tentative-outcome) was [Challenged](#challenge).

#### augur.api.ReportingWindow.getNumIncorrectDesignatedReportMarkets({ tx }[, callback])

<!-- TODO: Update description -->

Returns the number of [Markets](#market) belonging to this [Reporting Window](#reporting-window) in which [Designated Reporter's](#designated-reporter) [Tentative Outcome](#tentative-outcome) was [Challenged](#challenge) during the [Dispute Round Phase](#dispute-round-phase). This is only the count of Markets where Designated Reporters did [Report](#report) during the [Designated Reporting Phase](#designated-reporting-phase) but the Market didn't get [Finalized](#finalized-market). This doesn't include Markets where the Designated Reporter failed to submit a Report.

#### augur.api.ReportingWindow.getNumInvalidMarkets({ tx }[, callback])

Returns the number of [Markets](#market) that were [Reported](#report) to be Invalid during a [Reporting Window](#reporting-window). Invalid Markets are Markets that aren't clearly defined or doesn't fit one of the [Outcomes](#outcome) set for this Market. [Reporters](#reporter) are encouraged to Report the Market as Invalid if they can't confidently stake their [REP](#rep) into a single Outcome for the Market.

#### augur.api.ReportingWindow.getNumMarkets({ tx }[, callback])

Returns the total number of [Markets](#market) that belong to the [Fee Window](#fee-window).

#### augur.api.ReportingWindow.getParticipationToken({ tx }[, callback])

Returns the Contract Address of the [Participation Token](#participation-token) associated with the specified [Reporting Window](#reporting-window).

#### augur.api.ReportingWindow.getReportingEndTime({ tx }[, callback])

Returns a timestamp of when the [Reporting Phase](#reporting-phase) of a specific [Reporting Window](#reporting-window) will be ending. Reporting Windows are 30 days long and split into two Phases, the Reporting Phase and the [Dispute Round Phase](#dispute-round-phase). The Reporting Phase lasts 27 days, the Dispute Round Phase lasts 3 days.

#### augur.api.ReportingWindow.getReportingStartTime({ tx }[, callback])

Returns a timestamp of when the [Reporting Phase](#reporting-phase) of a specific [Reporting Window](#reporting-window) will be starting. Reporting Windows are 30 days long and split into two Phases, the Reporting Phase and the [Dispute Round Phase](#dispute-round-phase). The Reporting Phase lasts 27 days, the Dispute Round Phase lasts 3 days.

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

This method returns whether the [Reporting Window](#reporting-window) is currently active or not. Reporting Windows are considered active during the Window's [Reporting Phase](#reporting-phase) and [Dispute Round Phase](#dispute-round-phase), which last a total of 30 days. Returns `1` if the `reportingWindow` is active, `0` if not.

#### augur.api.ReportingWindow.isContainerForMarket({ tx, \_shadyMarket }[, callback])

Returns whether the `_shadyMarket` address provided is a [Market](#market) that is set to be [Reported](#report) on during the [Reporting Window](#reporting-window). Markets are assigned a Reporting Window that is the first Reporting Window following the Market's [End Time](#end-time). Returns `1` if the Market belongs to the `reportingWindow`, `0` if not.

#### augur.api.ReportingWindow.isDisputeActive({ tx }[, callback])

Returns whether the [Reporting Window](#reporting-window) is currently in it's [Dispute Round Phase](#dispute-round-phase) or not. The Dispute Round Phase is a 3 day long period that follows the Reporting Window's [Reporting Phase](#reporting-phase), which lasts 27 days. Returns `1` if the Reporting Window's Dispute Round Phase is active, `0` if not.

#### augur.api.ReportingWindow.isForkingMarketFinalized({ tx }[, callback])

Returns whether the [Forked Market](#forked-market) that caused this [Reporting Window](#reporting-window)'s [Universe](#universe) to be created has been [Finalized](#finalized-market) or not. Every Reporting Window belongs to a Universe and all Universes, except for the first Universe, are created because of a [Fork](#fork). Returns `1` if the Forked Market is Finalized, `0` it not.

#### augur.api.ReportingWindow.isReportingActive({ tx }[, callback])

Returns whether the [Reporting Window](#reporting-window) is currently in it's [Reporting Phase](#reporting-phase) or not. The Reporting Phase lasts 27 days at the start of a Reporting Window and is followed by a 3 day [Dispute Round Phase](#dispute-round-phase). Returns `1` if the Reporting Window's Reporting Phase is active, `0` if not.

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

The Reputation Token, known as [REP](#rep), is the key that allows Augur's [Decentralized Oracle](#decentralized-oracle) system to function, and by extension the entirety of Augur. REP has three major functions, it's used to [Report](#report) on the [Outcome](#outcome) of a [Market](#market), [Challenge](#challenge) the [Tentative Outcome](#tentative-outcome) of a Market, and as a bond when creating a Market. [Reporters](#reporter) stake REP on the Outcome of a Market as a show of confidence in their Report. If the Reporter correctly staked on the [Final Outcome](#final-outcome) of the Market they can claim their REP back, earn [Reporting Fees](#reporting-fee) proportional to their staked REP, and a portion of the REP incorrectly staked on other Outcomes.

REP is also used to Challenge the Tentative Outcome of Reports during the [Dispute Round Phase](#dispute-round-phase). If the Challenge successfully changes the Tentative Outcome of a Market and that outcome becomes the Final Outcome, the [Dispute Bond](#dispute-bond) holder can redeem the bond for up to double the REP it cost to place. When creating a Market, the Market Creator is required to specify a Designated Reporter and pay a [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) to ensure the Designated Reporter shows up. If the Reporter doesn't show up, the Market Creator's Designated Report No-Show REP Bond will go to the First Reporter to Report on the Market. Their gas cost for the Report transaction will be covered by the Market Creator's [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) and the Designated Report No-Show REP Bond is added to whatever the First Reporter staked, there by improving her stake and potential rewards if correctly staked.

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

Universe Call API
---------------
```javascript
// Universe Contract Call API Examples:
var universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5";
var _parentPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";

augur.api.Universe.getChildUniverse({
  tx: { to: universe },
  _parentPayoutDistributionHash: _parentPayoutDistributionHash
}, function (error, childUniverse) { console.log(childUniverse); });
// example output:
"0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c"

augur.api.Universe.getCurrentFeeWindow({ tx: { to: universe } }, function (error, currFeeWindow) { console.log(currFeeWindow); });
// example output:
"0x1f90cc6b4e89303e451c9b852827b5791667f570"

augur.api.Universe.getDisputeRoundDurationInSeconds({ tx: { to: universe } }, function (error, disputeRoundDuration) { console.log(disputeRoundDuration); });
// example output:
"604800"

augur.api.Universe.getFeeWindow({ 
  tx: { to: universe }, 
  _feeWindowId: 578,
}, function (error, feeWindow) { console.log(feeWindow); });
// example output:
"0x1f90cc6b4e89303e451c9b852827b5791667f570"

augur.api.Universe.getFeeWindowByTimestamp({ 
  tx: { to: universe },
  _timestamp: 1514500367
}, function (error, feeWindow) { console.log(feeWindow); });
// example output:
"0x1f90cc6b4e89303e451c9b852827b5791667f570"

augur.api.Universe.getFeeWindowForForkEndTime({ tx: { to: universe } }, function (error, feeWindow) { console.log(feeWindow); });
// example output:
"0x1f90cc6b4e89303e451c9b852827b5791667f570"

augur.api.Universe.getFeeWindowId({ 
  tx: { to: universe },
  _timestamp: 1514500367
}, function (error, feeWindowId) { console.log(feeWindowId); });
// example output:
"578"

augur.api.Universe.getForkEndTime({ tx: { to: universe } }, function (error, forkEndTime) { console.log(forkEndTime); });
// example output:
"1489855429"

augur.api.Universe.getForkingMarket({ tx: { to: universe } }, function (error, forkingMarket) { console.log(forkingMarket); });
// example output:
"0x78f7b43150d27c464359e735781c16ac585f52a8"

augur.api.Universe.getForkReputationGoal({ universe }, function (error, forkReputationGoal) { console.log(forkReputationGoal); });
// example output:
"150657"

augur.api.Universe.getNextFeeWindow({ tx: { to: universe } }, function (error, nextFeeWindow) { console.log(nextFeeWindow); });
// example output:
"579"

augur.api.Universe.getOpenInterestInAttoEth({ tx: { to: universe } }, function (error, openInterestInAttoEth) { console.log(openInterestInAttoEth); });
// example output:
"7123876876123"

augur.api.Universe.getParentPayoutDistributionHash({ tx: { to: universe } }, function (error, universeParentPayoutDistributionHash) { console.log(universeParentPayoutDistributionHash); });
// example output:
"0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Universe.getParentUniverse({ tx: { to: universe } }, function (error, parentUniverse) { console.log(parentUniverse); });
// example output:
"0x63c59544b89cce1dd53b1b566862189b25adec41"

augur.api.Universe.getPreviousFeeWindow({ tx: { to: universe } }, function (error, previousFeeWindow) { console.log(previousFeeWindow); });
// example output:
"577"

augur.api.Universe.getReputationToken({ tx: { to: universe } }, function (error, reputationTokenAddress) { console.log(reputationTokenAddress); });
// example output:
"0x2fb561b2bdbcd1ae1995bdd6aff6776d6f4292f2"

augur.api.Universe.getWinningChildUniverse({ tx: { to: universe } }, function (error, winningChildUniverse) { console.log(winningChildUniverse); });
// example output:
"0x432561b2bdbcd1ae1995bdd6aff6776d6f4292f2"

augur.api.Universe.isContainerForFeeToken({
  tx: { to: universe },
  _shadyFeeToken: "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"
}, function (error, isContainerForFeeToken) { console.log(isContainerForFeeToken); });
// example output:
"1"

augur.api.Universe.isContainerForFeeWindow({
  tx: { to: universe },
  _shadyFeeWindow: "0x1233cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"
}, function (error, isContainerForReportingWindow) { console.log(isContainerForReportingWindow); });
// example output:
"1"

augur.api.Universe.isContainerForMarket({
  tx: { to: universe },
  _shadyMarket: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"
}, function (error, isContainerForMarket) { console.log(isContainerForMarket); });
// example output:
"1"

augur.api.Universe.isContainerForReportingParticipant({
  tx: { to: universe },
  _shadyReportingParticipant: "0x6788ff3e9ce1c0459b309fac6dd4e69229b91a41"
}, function (error, isContainerForReportingParticipant) { console.log(isContainerForReportingParticipant); });
// example output:
"1"

augur.api.Universe.isContainerForShareToken({
  tx: { to: universe },
  _shadyShareToken: "0x9328ff3e9ce1c0459b309fac6dd4e69229b91a61"
}, function (error, isContainerForShareToken) { console.log(isContainerForShareToken); });
// example output:
"1"

augur.api.Universe.isForking({ tx: { to: universe } }, function (error, isForking) { console.log(isForking); });
// example output:
"0"

augur.api.Universe.isParentOf({
  tx: { to: universe },
  _shadyChild: "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c"
}, function (error, isParentOf) { console.log(isParentOf); });
// example output:
"1"
```
#### [Universe Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Universe.sol)

The Universe Contract is the contract that defines an Augur [Universe](#universe) and the methods used to interact with them. All of Augur's [Markets](#market), [Order Books](#order-book), [Fee Windows](#reporting-window), and [REP](#rep) belong to a specific Universe. In the rare event that a Market's [Tentative Outcome](#tentative-outcome) is [Challenged](#challenge) with a [Dispute Bond](#dispute-bond) greater than 1.25% of all existing [REP](#rep), a [Fork](#fork) will occur and new Universes will be created. The Universe that originally contained the [Forked Market](#forked-market) will become a [Locked Universe](#locked-universe), thereby not allowing any Market creation to take place in the Locked Universe. The newly created Universes are known as [Child Universes](#child-universe), whereas the original and now Locked Universe is considered those Child Universes' [Parent Universe](#parent-universe).

#### augur.api.Universe.getChildUniverse({ tx, \_parentPayoutDistributionHash }[, callback])

Returns the Ethereum contract address of a [Universe's](#universe) [Child Universe](#child-universe) that has its [Forked Market](#forked-market)'s [Final Outcome](#final-outcome) set to `_parentPayoutDistributionHash` [Payout Distribution Hash](#payout-distribution-hash). The Ethereum contract address is returned as a hexidecimal string.

#### augur.api.Universe.getCurrentFeeWindow({ tx }[, callback])

Returns the Ethereum contract address of the current running [Fee Window](#reporting-window) of a [Universe](#universe). Every Universe has a Fee Window that runs for a duration of 7 days before immediately starting the next Window. The Ethereum contract address is returned as a hexidecimal string.

#### augur.api.Universe.getDisputeRoundDurationInSeconds({ tx }[, callback])

Returns the number of seconds in a [Universe's](#universe) [Dispute Round](#dispute-round).

#### augur.api.Universe.getFeeWindow({ tx, \_feeWindowId  }[, callback])

Returns the Ethereum contract address of the Fee Window `_feeWindowId` in a [Universe](#universe), as a hexidecimal string.

#### augur.api.Universe.getFeeWindowByTimestamp({ tx, \_timestamp }[, callback])

Returns the Ethereum contract address of the Fee Window `_feeWindowId` running at `_timestamp` in a [Universe](#universe), as a hexidecimal string.

#### augur.api.Universe.getFeeWindowForForkEndTime({ tx } [, callback])

Returns the Ethereum contract address of the Fee Window `_feeWindowId` in a [Universe](#universe) once the current [Fork](#fork) ends, as a hexidecimal string.

#### augur.api.Universe.getFeeWindowId({ tx, \_timestamp } [, callback])

Returns the [Fee Window](#fee-window) ID for the [Universe](#universe) specified in `tx` and `_timestamp`, as an integer. This is calculated by dividing the timestamp by the [Universe's](#universe) Fee Window duration in seconds.

#### augur.api.Universe.getForkEndTime({ tx }[, callback])

Returns the timestamp for when the [Fork Phase](#fork-period) ends that was started on the [Universe](#universe) specified in `tx`. If there is no [Forked Market](#forked-market) in the Universe, this function will return 0.

#### augur.api.Universe.getForkingMarket({ tx }[, callback])

Returns the Ethereum contract address of the [Market](#market) that the [Universe](#universe) specified in `tx` is [Forking](#fork) over. This returns the null address (0x0000000000000000000000000000000000000000) if the Universe has never Forked and there is no [Forked Market](#forked-market).

#### augur.api.Universe.getForkReputationGoal({ tx }[, callback])

Returns the estimated amount of [REP](#rep) that must be migrated to one [Child Universe](#child-universe) in order to allow a [Fork](#fork) in the [Universe](#universe) specified in `tx` to be [Finalized](#finalized-market) before the end of the [Fork Phase](#fork-period).

#### augur.api.Universe.getNextFeeWindow({ tx }[, callback])

Returns the Ethereum contract address of the [Fee Window](#fee-window) coming up after the current Fee Window ends in the [Universe](#universe) specified in `tx`. The Ethereum contract address is returned as a hexidecimal string.

#### augur.api.Universe.getOpenInterestInAttoEth({ tx }[, callback])

Returns the total value of all [Complete Sets](#complete-sets) that exist across all [Markets](#market) the [Universe](#universe) specified in `tx`, priced in attoETH. This value is returned as an integer.

#### augur.api.Universe.getParentPayoutDistributionHash({ tx }[, callback])

Returns the [Payout Distribution Hash](#payout-distribution-hash) of a [Universe's](#universe) [Parent Universe](#parent-universe) for a [Child Universe](#child-universe) specified in `tx`. The Payout Distribution Hash is a hash of the winning [Outcome](#outcome) of the [Forked Market](#forked-market).

#### augur.api.Universe.getParentUniverse({ tx }[, callback])

Returns the [Parent Universe](#parent-universe) Ethereum contract address of the [Universe](#universe) specified in `tx`, as a hexidecimal string. When a [Fork](#fork) occurs, [Child Universes](#child-universe) are created and the original [Universe](#universe) that contained the [Forked Market](#forked-market) would become a Parent Universe to the newly created Universes. If this is called on the [Genesis Universe](#genesis-universe), it will return 0, as the first Universe has no Parent Universe.

#### augur.api.Universe.getPreviousFeeWindow({ tx }[, callback])

Returns the Ethereum contract address of the previous [Fee Window](#fee-window) for the [Universe](#universe) specified in `tx`, as a hexidecimal string.

#### augur.api.Universe.getReputationToken({ tx }[, callback])

Returns the Ethereum contract address of the [Reputation Token](#rep) for the [Universe](#universe) specified in `tx`, as a hexidecimal string. REP associated with this contract address are usable within this Universe.

#### augur.api.Universe.getWinningChildUniverse({ tx }[, callback])

Returns the Ethereum contract address of the [Winning Universe](#winning-universe) for a particular [Universe](#universe) that has [Forked](#fork).

#### augur.api.Universe.isContainerForFeeWindow({ tx, \_shadyFeeWindow }[, callback])

Returns whether the specific `universe` is a container for the [Fee Window](#fee-window) `_shadyFeeWindow` Ethereum contract address. Returns `1` if true or `0` if false. Every Fee Window belongs to a [Universe](#universe), and this method is used to see if a specific Fee Window address belongs to the Universe in question.

#### augur.api.Universe.isContainerForMarket({ tx, \_shadyMarket }[, callback])

Returns whether the specific `universe` is a container for the [Market](#market) `_shadyMarket` Ethereum contract address. Returns `1` if true or `0` if false. All Markets are created within a [Universe](#universe), and this function is used to help confirm if a Market exists within the Universe in question.

#### augur.api.Universe.isContainerForShareToken({ tx, \_shadyShareToken }[, callback])

[Shares](#share) are represented within Augur's smart contracts as [ERC-20](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md) tokens called Share Tokens. Returns whether the specific `universe` is a container for the Share Token `_shadyShareToken` Ethereum contract address. Returns `1` if true or `0` if false. 

#### augur.api.Universe.isForking({ tx }[, callback])

Returns `1` if the current [Universe](#universe) is Forking or `0` otherwise.

#### augur.api.Universe.isParentOf({ tx, \_shadyChild }[, callback])

Returns whether the specific `universe` is a container for the `_shadyChild` [Child Universe](#child-universe) Ethereum contract address provided. Returns `1` if true or `0` if false. This function can be used to see if a specific [Universe](#universe) is the [Parent Universe](#parent-universe) to a Child Universe.
