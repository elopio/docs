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

Augur's Call API is made up of "getter" methods that retrieve information from the blockchain (using Ethereum's `eth_call` RPC) but does not write information to the blockchain. The Call API is for more granular "gets" than the [Simplified API](#simplified-api) allows for. The Call API is directly mapped to the Augur Contracts and their publicly exposed methods. All Call API functions will accept two arguments, a `params` object with key/value pairs that match inputs for the contract method and a callback function. The Augur API functions are attached to the `augur.api` object and follow a pattern of `augur.api.<Contract Name>.<Contract Method>(<Params Object>, <Callback Function>)`. Although technically optional, the Augur team **strongly recommends** using a callback. Without the callback, the calls will be synchronous, which can lock up the web browser until they complete.

<aside class="warning">Synchronous HTTP RPC is generally not recommended, especially if augur.js is running in a browser. Synchronous RPC requests block the main JavaScript thread, which essentially freezes the browser!</aside>

Augur Call API
---------------------------
```javascript
var augur = "0xd6d6eaefcfaf7ea1e17c4768a554d57800699eb7";
augur.api.Augur.isKnownUniverse({ 
  tx: { to: augur },
  _universe: "0x22d6eaefcfaf7ea1e17c4768a554d57800699ecc"
}, function (error, isKnownUniverse) { console.log(isKnownUniverse); });
// example output:
"1"
```
#### [Augur Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol)

The Augur contract maintains the existing [Universes](#universe) within Augur and handles much of Augur's event logging.

### augur.api.Augur.isKnownUniverse({ tx, _universe }[, callback])

Augur keeps track of the [Genesis Universe](#genesis-universe) and all [Child Universes](#child-universe) internally. This function returns `1` if the specified `_universe` is in that list of known [Universes](#universe), or `0` otherwise.

Dispute Crowdsourcer Call API
-------------------------
```javascript
// Dispute Crowdsourcer Contract Call API Examples:
augur.api.DisputeCrowdsourcer.getFeeWindow({ tx: { to: disputeCrowdsourcer } }, function (error, feeWindow) { console.log(feeWindow); });
// example output:
"0x1f90cc6b4e89303e451c9b852827b5791667f570"

augur.api.DisputeCrowdsourcer.getMarket({ tx: { to: disputeCrowdsourcer } }, function (error, market) { console.log(market); });
// example output:
"0xaa90cc6b4e89303e451c9b852827b5791667f5aa"

augur.api.DisputeCrowdsourcer.getPayoutDistributionHash({ tx: { to: disputeCrowdsourcer } }, function (error, payoutDistributionHash) { console.log(payoutDistributionHash); });
// example output:
"0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.DisputeCrowdsourcer.getPayoutNumerator({ tx: { to: disputeCrowdsourcer } }, function (error, payoutNumerator) { console.log(payoutNumerator); });
// example output:
"1000"

augur.api.DisputeCrowdsourcer.getReputationToken({ tx: { to: disputeCrowdsourcer } }, function (error, reputationToken) { console.log(reputationToken); });
// example output:
"0xff90cc6b4e89303e451c9b852827b5791667f5ff"

augur.api.DisputeCrowdsourcer.getSize({ tx: { to: disputeCrowdsourcer } }, function (error, size) { console.log(size); });
// example output:

augur.api.DisputeCrowdsourcer.getStake({ tx: { to: disputeCrowdsourcer } }, function (error, stake) { console.log(stake); });
// example output:
"78123523"

augur.api.DisputeCrowdsourcer.isDisavowed({ tx: { to: disputeCrowdsourcer } }, function (error, isDisavowed) { console.log(isDisavowed); });
// example output:
"1"

augur.api.DisputeCrowdsourcer.isInvalid({ tx: { to: disputeCrowdsourcer } }, function (error, isInvalid) { console.log(isInvalid); });
// example output:
"1"
```
#### [Dispute Crowdsourcer Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/DisputeCrowdsourcer.sol)

#### augur.api.DisputeCrowdsourcer.getFeeWindow({ tx }[, callback])

Returns the Ethereum address of the [Fee Window](#fee-window) to which the specified [Dispute Crowdsourcer](#dispute-crowdsourcer) belongs, as a hexadecimal string.

#### augur.api.DisputeCrowdsourcer.getMarket({ tx }[, callback])

Returns the [Market](#market) Ethereum address for the specified [Dispute Crowdsourcer](#dispute-crowdsourcer), as a hexadecimal string.

#### augur.api.DisputeCrowdsourcer.getPayoutDistributionHash({ tx }[, callback])

Returns the [Payout Distribution Hash](#payout-distribution-hash) for the specified [Dispute Crowdsourcer](#dispute-crowdsourcer), as a 32-byte hexadecimal string.

#### augur.api.DisputeCrowdsourcer.getPayoutNumerator({ tx }[, callback])

Returns the [Payout Numerator](#payout-set) for the specified [Dispute Crowdsourcer](#dispute-crowdsourcer), as an unsigned integer.

#### augur.api.DisputeCrowdsourcer.getReputationToken({ tx }[, callback])

Returns the [Reputation Token (REP)](#rep) Ethereum address for the specified [Dispute Crowdsourcer](#dispute-crowdsourcer), as a hexadecimal string. In order to [Challenge](#challenge) a [Tentative Outcome](#tentative-outcome) by [Staking](#dispute-stake) on a Dispute Crowdsourcer, Reporters need REP. A Dispute Crowdsourcer only accepts one REP contract as the source of Staked REP, and this method returns that contract's address.

#### augur.api.DisputeCrowdsourcer.getSize({ tx }[, callback])

Description pending. Add example JS response.

#### augur.api.DisputeCrowdsourcer.getStake({ tx }[, callback])

Description pending. Update JS example response to atto.

#### augur.api.DisputeCrowdsourcer.isDisavowed({ tx }[, callback])

Description pending.

#### augur.api.DisputeCrowdsourcer.isInvalid({ tx }[, callback])

Description pending.

Fee Window Call API
-------------------------
```javascript
// Fee Window Contract Call API Examples:
var feeWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.FeeWindow.getAvgReportingGasPrice({ tx: { to: feeWindow } }, function (error, avgReportingGasPrice) { console.log(avgReportingGasPrice); });
// example output:
"340000"

augur.api.FeeWindow.getEndTime({ tx: { to: feeWindow } }, function (error, endTime) { console.log(endTime); });
// example output:
"1500388730"

augur.api.FeeWindow.getNumDesignatedReportNoShows({ tx: { to: feeWindow } }, function (error, numDesignatedReportNoShows) { console.log(numDesignatedReportNoShows); });
// example output:
"2"

augur.api.FeeWindow.getNumIncorrectDesignatedReportMarkets({ tx: { to: feeWindow } }, function (error, numIncorrectDesignatedReportMarkets) { console.log(numIncorrectDesignatedReportMarkets); });
// example output:
"10"

augur.api.FeeWindow.getNumInvalidMarkets({ tx: { to: feeWindow } }, function (error, numInvalidMarkets) { console.log(numInvalidMarkets); });
// example output:
"3"

augur.api.FeeWindow.getNumMarkets({ tx: { to: feeWindow } }, function (error, numMarkets) { console.log(numMarkets); });
// example output:
"65"

augur.api.FeeWindow.getReputationToken({ tx: { to: feeWindow } }, function (error, reputationToken) { console.log(reputationToken); });
// example output:
"0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.FeeWindow.getStartTime({ tx: { to: feeWindow } }, function (error, startTime) { console.log(startTime); });
// example output:
"14995895900"

augur.api.FeeWindow.getUniverse({ tx: { to: feeWindow } }, function (error, universe) { console.log(universe); });
// example output:
"0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.FeeWindow.isActive({ tx: { to: feeWindow } }, function (error, isActive) { console.log(isActive); });
// example output:
"1"

var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.FeeWindow.isContainerForMarket({
  tx: { to: feeWindow },
  _shadyMarket: _market
}, function (error, isContainerForMarket) { console.log(isContainerForMarket); });
// example output:
"1"

augur.api.FeeWindow.isForkingMarketFinalized({ tx: { to: feeWindow } }, function (error, isForkingMarketFinalized) { console.log(isForkingMarketFinalized); });
// example output:
"0"

augur.api.FeeWindow.isOver({ tx: { to: feeWindow } }, function (error, isOver) { console.log(isOver); });
// example output:
"0"
```
#### [Fee Window Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/FeeWindow.sol)

#### augur.api.FeeWindow.getAvgReportingGasPrice({ tx }[, callback ])

Returns the average amount of gas spent to submit either a [Designated Report](#designated-report) or [First Public Report](#first-public-report) in the specified [Fee Window](#fee-window).

#### augur.api.FeeWindow.getEndTime({ tx }[, callback])

Returns a Unix timestamp for when the specified [Fee Window](#fee-window) will end. A Fee Window is considered active for a total of 7 days, then ends, and is no longer considered to be active.

#### augur.api.FeeWindow.getNumDesignatedReportNoShows({ tx }[, callback])

Returns the number of [Markets](#market) belonging to the specified [Fee Window](#fee-window) in which the [Designated Reporter](#designated-reporter) failed to [Report](#report) during the [Designated Reporting Phase](#designated-reporting-phase). These Markets will have a [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) and [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) up for grabs for the [First Public Reporter](#first-public-reporter) because these Markets have yet to receive a [Report](#report). This only includes Markets where Designated Reporters failed to Report, and does not include Markets where the Designated Reporter's [Tentative Outcome](#tentative-outcome) was [Challenged](#challenge).

#### augur.api.FeeWindow.getNumIncorrectDesignatedReportMarkets({ tx }[, callback])

Returns the number of [Unfinalized Markets](#finalized-market) belonging to the specified [Fee Window](#fee-window) in which [Designated Reporter's](#designated-reporter) [Tentative Outcome](#tentative-outcome) was [Challenged](#challenge) during the current [Dispute Round Phase](#dispute-round-phase), or the [Designated Reporter](#designated-reporter) failed to submit a [Designated Report](#designated-report).

#### augur.api.FeeWindow.getNumInvalidMarkets({ tx }[, callback])

Returns the number of [Markets](#market) that were [Reported](#report) to be [Invalid](#invalid-outcome) during the specified [Fee Window](#fee-window). [Invalid](#invalid-outcome) Markets are Markets that aren't clearly defined or do not fit one of the [Outcomes](#outcome) set for this Market. [Reporters](#reporter) are encouraged to [Report](#report) the Market as Invalid if they can't confidently Stake their [REP](#rep) into a single Outcome for the Market.

#### augur.api.FeeWindow.getNumMarkets({ tx }[, callback])

Returns the total number of [Markets](#market) that are in the [Dispute Round Phase](#dispute-round-phase) for the specified [Fee Window](#fee-window).

#### augur.api.FeeWindow.getReputationToken({ tx }[, callback])

Returns the [Reputation Token (REP)](#rep) Ethereum address for the specified [Fee Window](#fee-window), as a hexadecimal string. Every Fee Window has a [Dispute Phase](#dispute-phase) where [Reporters](#reporter) can [Challenge](#challenge) the [Tentative Outcome](#tentative-outcome) of [Markets](#market). In order to Challenge a Tentative Outcome, Reporters need to [Stake](#dispute-stake) REP. A Fee Window only accepts one REP contract as the source of Staked REP, and this method returns that contract's address.

#### augur.api.FeeWindow.getStartTime({ tx }[, callback])

Returns a Unix timestamp of when a [Fee Window](#fee-window) becomes active and starts. A Fee Window is considered active for a total of 7 days, then ends, and is no longer considered to be active. Only active Fee Windows allow [Reporters](#reporter) to [Challenge](#challenge) the [Tentative Outcomes](#tentative-outcome) of [Markets](#market) contained in the Fee Window.

#### augur.api.FeeWindow.getUniverse({ tx }[, callback])

Returns the [Universe](#universe) Ethereum address that the specified [Fee Window](#fee-window) belongs to, as a hexadecimal string. Every Fee Window belongs to a specific Universe in which they were created and can operate.

#### augur.api.FeeWindow.isActive({ tx }[, callback])

This method returns whether the specified [Fee Window](#fee-window) is currently active. Fee Windows are considered active during the Window's [Dispute Round Phase](#dispute-round-phase), which last a total of 7 days. Returns `1` if the specified Fee Window is active, or `0` otherwise.

#### augur.api.FeeWindow.isContainerForMarket({ tx, \_shadyMarket }[, callback])

Returns whether the `_shadyMarket` address provided is a [Market](#market) that is set to be [Reported](#report) on during the [Fee Window](#fee-window). Markets are assigned a Fee Window that is the first Fee Window following the Market's [End Time](#end-time). Returns `1` if the Market belongs to the specified Fee Window, or `0` if not.

#### augur.api.FeeWindow.isForkingMarketFinalized({ tx }[, callback])

Returns whether the [Forked Market](#forked-market) that caused this [Fee Window's](#fee-window) [Universe](#universe) to be created has been [Finalized](#finalized-market). Every Fee Window belongs to a Universe and all Universes, except for the first Universe, are created because of a [Fork](#fork). Returns `1` if the Forked Market is Finalized, or `0` otherwise.

#### augur.api.FeeWindow.isOver({ tx }[, callback])

Returns whether the 7-day [Fee Window](#fee-window) specified has ended.

Initial Reporter Call API
-------------------------
```javascript
// Initial Reporter Contract Call API Examples:
augur.api.InitialReporter.designatedReporterShowed({ tx: { to: initialReporter } }, function (error, designatedReporterShowed) { console.log(designatedReporterShowed); });
// example output:
"1"

augur.api.InitialReporter.designatedReporterWasCorrect({ tx: { to: initialReporter } }, function (error, designatedReporterWasCorrect) { console.log(designatedReporterWasCorrect); });
// example output:
"1"

augur.api.InitialReporter.getDesignatedReporter({ tx: { to: initialReporter } }, function (error, designatedReporter) { console.log(designatedReporter); });
// example output:
"0xca3edca4ed326bbcb77e914b379913b12d49654d"

augur.api.InitialReporter.getFeeWindow({ tx: { to: initialReporter } }, function (error, feeWindow) { console.log(feeWindow); });
// example output:
"0x1f90cc6b4e89303e451c9b852827b5791667f570"

augur.api.InitialReporter.getMarket({ tx: { to: disputeCrowdsourcer } }, function (error, market) { console.log(market); });
// example output:
"0xaa90cc6b4e89303e451c9b852827b5791667f5aa"

augur.api.InitialReporter.getPayoutDistributionHash({ tx: { to: disputeCrowdsourcer } }, function (error, payoutDistributionHash) { console.log(payoutDistributionHash); });
// example output:
"0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.InitialReporter.getPayoutNumerator({ tx: { to: disputeCrowdsourcer } }, function (error, payoutNumerator) { console.log(payoutNumerator); });
// example output:
"1000"

augur.api.InitialReporter.getReportTimestamp({ tx: { to: initialReporter } }, function (error, reportTimestamp) { console.log(reportTimestamp); });
// example output:
"1514956848"

augur.api.InitialReporter.getReputationToken({ tx: { to: initialReporter } }, function (error, reputationToken) { console.log(reputationToken); });
// example output:
"0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.InitialReporter.getSize({ tx: { to: disputeCrowdsourcer } }, function (error, size) { console.log(size); });
// example output:

augur.api.InitialReporter.getStake({ tx: { to: initialReporter } }, function (error, stake) { console.log(stake); });
// example output:
"78000"

augur.api.InitialReporter.isDisavowed({ tx: { to: disputeCrowdsourcer } }, function (error, isDisavowed) { console.log(isDisavowed); });
// example output:
"1"

augur.api.InitialReporter.isInvalid({ tx: { to: disputeCrowdsourcer } }, function (error, isInvalid) { console.log(isInvalid); });
// example output:
"1"
```
#### [Initial Reporter Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/InitialReporter.sol)

The Initial Reporter contract handles functionality related to both [Designated Reporting](#designated-reporting) and [Open Reporting](open-reporting).

#### augur.api.InitialReporter.designatedReporterShowed({ tx }[, callback])

Returns `1` if the [Designated Reporter](#designated-reporter) submitted a [Report](#report) within the [Designated Reporting Phase](#designated-reporting-phase), or `0` otherwise.

#### augur.api.InitialReporter.designatedReporterWasCorrect({ tx }[, callback])

Returns `1` if the [Payout Distribution Hash](#payout-distribution-hash) submitted in the [Designated Report](#designated-report) is the same as the winning Payout Distribution Hash for the specified InitialReporter contract.

#### augur.api.InitialReporter.getDesignatedReporter({ tx }[, callback])

Returns the Ethereum address for the [Designated Reporter](#designated-reporter) set for the specified InitialReporter contract, as a hexadecimal string.

#### augur.api.InitialReporter.getFeeWindow({ tx }[, callback])

Returns the Ethereum address of the [Fee Window](#fee-window) to which the specified InitialReporter contract belongs, as a hexadecimal string.

#### augur.api.InitialReporter.getMarket({ tx }[, callback])

Returns the [Market](#market) Ethereum address for the specified InitialReporter contract, as a hexadecimal string.

#### augur.api.InitialReporter.getPayoutDistributionHash({ tx }[, callback])

Returns the [Payout Distribution Hash](#payout-distribution-hash) for the specified InitialReporter contract, as a 32-byte hexadecimal string.

#### augur.api.InitialReporter.getPayoutNumerator({ tx }[, callback])

Returns the [Payout Numerator](#payout-set) for the specified InitialReporter contract, as an unsigned integer.

#### augur.api.InitialReporter.getReportTimestamp({ tx }[, callback])

Description pending.

#### augur.api.InitialReporter.getReputationToken({ tx }[, callback])

Returns the [Reputation Token (REP)](#rep) Ethereum address for the specified InitialReporter contract. In order to [Challenge](#challenge) a [Tentative Outcome](#tentative-outcome) by [Staking](#dispute-stake) on a [Dispute Crowdsourcer](#dispute-crowdsourcer), Reporters need REP. A Dispute Crowdsourcer only accepts one REP contract as the source of Staked REP, and this method returns that contract's address.

#### augur.api.InitialReporter.getSize({ tx }[, callback])

Description pending. Add example JS response.

#### augur.api.InitialReporter.getStake({ tx }[, callback])

Description pending. Update JS example response to atto.

#### augur.api.InitialReporter.isDisavowed({ tx }[, callback])

Description pending.

#### augur.api.InitialReporter.isInvalid({ tx }[, callback])

Description pending.

Market Call API
----------------
```javascript
// Market Contract Call API Examples:
var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Market.derivePayoutDistributionHash({
  tx: { to: market } },
  _payoutNumerators: [ 0, 1000 ],
  _invalid: false,
});
// example output:
"1"

augur.api.Market.designatedReporterShowed({ tx: { to: market } }, function (error, designatedReporterShowed) { console.log(designatedReporterShowed); });
// example output:
"1"

augur.api.Market.designatedReporterWasCorrect({ tx: { to: market } }, function (error, designatedReporterWasCorrect) { console.log(designatedReporterWasCorrect); });
// example output:
"1"

augur.api.Market.getDenominationToken({ tx: { to: market } }, function (error, denominationToken) { console.log(denominationToken); });
// example output:
"0x30e3852448f4ab5d62bbf7544ca3c92daca5c957"

augur.api.Market.getDesignatedReporter({ tx: { to: market } }, function (error, designatedReporter) { console.log(designatedReporter); });
// example output:
"0xca3edca4ed326bbcb77e914b379913b12d49654d"

augur.api.Market.getEndTime({ tx: { to: market } }, function (error, endTime) { console.log(endTime); });
// example output:
"1500388730";

augur.api.Market.getFeeWindow({ 
  tx: { to: market }, 
  _feeWindowId: 578,
}, function (error, feeWindow) { console.log(feeWindow); });
// example output:
"0x1f90cc6b4e89303e451c9b852827b5791667f570"

augur.api.Market.getFinalizationTime({ tx: { to: market } }, function (error, finalizationTime) { console.log(finalizationTime); });
// example output:
"1500647930";

augur.api.Market.getForkingMarket({ tx: { to: market } }, function (error, forkedMarket) { console.log(forkedMarket); });
// example output:
"0x0"

augur.api.Market.getMarketCreatorMailbox({ tx: { to: market } }, function (error, marketCreatorMailbox) { console.log(marketCreatorMailbox); });
// example output:
"0xeabdeaefcfaf7ea1e17c4768a554d5780069eabd"

augur.api.Market.getMarketCreatorSettlementFeeDivisor({ tx: { to: market } }, function (error, marketCreatorSettlementFee) { console.log(marketCreatorSettlementFee); });
// example output:
"20000000000000000"

augur.api.Market.getNumberOfOutcomes({ tx: { to: market } }, function (error, numOutcomes) { console.log(numOutcomes); });
// example output:
"2"

augur.api.Market.getNumTicks({ tx: { to: market } }, function (error, numTicks) { console.log(numTicks); });
// example output:
"1000"

augur.api.Market.getReputationToken({ tx: { to: market } }, function (error, reputationToken) { console.log(reputationToken); });
// example output:
"0x23b17188ce3c491f6ab4427258d92452be5c8045"

augur.api.Market.getShareToken({
  tx: { to: market },
  _outcome: 1
}, function (error, shareToken) { console.log(shareToken); });
// example output:
"0x18b17188ce3c491f6ab4427258d92452be5c8054"

augur.api.Market.getStakeInOutcome({ 
  tx: { to: market }, 
  _payoutDistributionHash: "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a" 
}, function (error, stakeInOutcome) { console.log(stakeInOutcome); });
// example output:
"12378786123"

augur.api.Market.getTotalStake({ tx: { to: market } }, function (error, totalStake) { console.log(totalStake); });
// example output:
"1000"

augur.api.Market.getUniverse({ tx: { to: market } }, function (error, universe) { console.log(universe); });
// example output:
"0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.Market.getWinningPayoutDistributionHash({ tx: { to: market } }, function (error, winningDistributionHash) { console.log(winningDistributionHash); });
// example output:
"0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.Market.getWinningPayoutNumerator({
  tx: { to: market },
  _outcome: "0"
}, function (error, getWinningPayoutNumerator) { console.log(getWinningPayoutNumerator); });
// example output:
"1000"

augur.api.Market.isContainerForReportingParticipant({
  tx: { to: market },
  _shadyReportingParticipant: "0x18b17188ce3c491f6ab4427258d92452be5c8054"
}, function (error, isContainerForReportingParticipant) { console.log(isContainerForReportingParticipant); });
// example output:
"1"

augur.api.Market.isContainerForShareToken({
  tx: { to: market },
  _shadyShareToken: "0x18b17188ce3c491f6ab4427258d92452be5c8054"
}, function (error, isContainerForShareToken) { console.log(isContainerForShareToken); });
// example output:
"1"

augur.api.Market.isFinalized({
  tx: { to: market },
}, function (error, isFinalized) { console.log(isFinalized); });
// example output:
"1"

augur.api.Market.isInvalid({
  tx: { to: market }
}, function (error, isInvalid) { console.log(isInvalid); });
// example output:
"1"
```
#### [Market Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Market.sol)

#### augur.api.Market.derivePayoutDistributionHash({ tx, \_payoutNumerators, \_invalid, onSent, onSuccess, onFailed })

Returns the [Payout Distribution Hash](#payout-distribution-hash) of the specified [Market](#market) for the `_payoutNumerators` and `_invalid` passed in. If the [Payout Set](#payout-set) (referred to as Payout Numerators in the function), is valid, then a Payout Distribution Hash is created by hashing the Payout Set and `_invalid` using the keccak256 algorithm.

#### augur.api.Market.designatedReporterShowed({ tx }[, callback]) 

Returns `1` if the [Designated Reporter](#designated-reporter) submitted a [Report](#report) within the [Designated Reporting Phase](#designated-reporting-phase), or `0` otherwise.

#### augur.api.Market.designatedReporterWasCorrect({ tx }[, callback]) 

Returns `1` if the [Payout Distribution Hash](#payout-distribution-hash) submitted in the [Designated Report](#designated-report) is the same as the winning Payout Distribution Hash for the specified [Market](#market).

#### augur.api.Market.getDenominationToken({ tx }[, callback])

Returns the Ethereum address of the token used to denominate the specified [Market](#market), as a hexadecimal string. A Denomination Token is the [ERC-20 Token](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md) used as the currency to trade on the [Outcome](#outcome) of a [Market](#market). Currently, this function will always return the address of a [Cash](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/Cash.sol) contract; however, Augur will eventually support other types of Denomination Tokens.

#### augur.api.Market.getDesignatedReporter({ tx }[, callback])

Returns the Ethereum address for the [Designated Reporter](#designated-reporter) set for the specified [Market](#market), as a hexadecimal string. Every [Market](#market) is required to have an assigned Designated Reporter, which is set by the [Market Creator](#market-creator) during Market creation.

#### augur.api.Market.getEndTime({ tx }[, callback])

Returns the Unix timestamp for when the specified [Market's](#market) event has come to pass. When the Market's [End Time](#end-time) passes, the Market enters the [Designated Reporting Phase](#designated-reporting-phase).

#### augur.api.Market.getFeeWindow({ tx, \_feeWindowId  }[, callback])

Returns the Ethereum address of the [Fee Window](#fee-window) `_feeWindowId` in a [Market](#market), as a hexidecimal string.

#### augur.api.Market.getFinalizationTime({ tx }[, callback])

Returns the timestamp for when the specified [Market](#market) was [Finalized](#finalized-market). A Finalized Market has a [Final Outcome](#final-outcome) set from successful [Market Resolution](#market-resolution) which cannot be [Challenged](#challenge), and the Market is considered [Settled](#settlement).

#### augur.api.Market.getForkingMarket({ tx }[, callback])

Returns the Ethereum address of the [Forked Market](#forked-market) for the [Universe](#universe) that contains the specified [Market](#market) address. If the [Market](#market) address specified belongs in a Universe that hasn't had a [Fork](#fork) take place, this will return `0x0`.

#### augur.api.Market.getMarketCreatorMailbox({ tx }[, callback])

Returns the Ethereum address of the [Market Creator Mailbox](#market-creator-mailbox) for `market`. [Market Creators](#market-creator) can use this address to collect their fees.

#### augur.api.Market.getMarketCreatorSettlementFeeDivisor({ tx }[, callback])

Returns the [Creator Fee](#creator-fee) set by the [Market Creator](#market-creator), denominated in [attotokens](#atto-prefix) per [Settlement](#settlement) of a [Complete Set](#complete-set), for the specified [Market](#market).

#### augur.api.Market.getNumberOfOutcomes({ tx }[, callback])

Returns the number of [Outcomes](#outcome) for a specified [Market](#market). The number of Outcomes is the number of potential results for the [Market](#market) event.

#### augur.api.Market.getNumTicks({ tx }[, callback])

Returns the [Number of Ticks](#number-of-ticks) set for a specific [Market](#market). The Number of Ticks represents the number of valid price points between the [Market](#market)'s [Minimum Price](#minimum-display-price) and [Maximum Price](#maximum-display-price).

#### augur.api.Market.getReputationToken({ tx }[, callback])

Returns the Ethereum address of the [Reputation Token (REP)](#rep) for the specified [Market](#market), as a hexadecimal string. REP is Staked whenever an [Initial Report](#initial-report) is submitted or when users attempt to [Challenge](#challenge) the [Tentative Outcome](#tentative-outcome) of a [Market](#market). A Market only accepts one REP contract as the source of Staked REP, and this method returns that contract's address.

#### augur.api.Market.getShareToken({ tx, \_outcome }[, callback])

Returns the Ethereum address of the [Share Token](#share-token-call-api) for the specified [Market](#market) and [Outcome](#outcome). Every Outcome of a Market has a separate Share Token used to handle trading around that Outcome and this method returns the contract address of the Share Tokens for the specified Outcome.

#### augur.api.Market.getStakeInOutcome({ tx, \_payoutDistributionHash }[, callback])

Returns the amount of [attoREP](#atto-prefix) that has been Staked on the [Outcome](#outcome) with the [Payout Distribution Hash](#payout-distribution-hash) `_payoutDistributionHash` in the specified [Market](#market).

#### augur.api.Market.getTotalStake({ tx }[, callback])

Returns the total amount staked on the specified [Market](#market).

#### augur.api.Market.getUniverse({ tx }[, callback])

Returns the Etherem address of the [Universe](#universe) that the specified `market` is contained within. All [Markets](#market) are created in a specific Universe, and new Universes are created if a [Fork](#fork) occurs.

#### augur.api.Market.getWinningPayoutDistributionHash({ tx }[, callback])

Returns the winning [Payout Distribution Hash](#payout-distribution-hash) for a particular [Market](#market), as a 32-byte hexadecimal string.

#### augur.api.Market.getWinningPayoutNumerator({ tx, \_outcome }[, callback])

Returns the winning [Payout Numerator](#payout-set) for [Outcome](#outcome) `_outcome` in a particular [Market](#market), as an unsigned integer.

#### augur.api.Market.isContainerForReportingParticipant({ tx, \_shadyReportingParticipant }[, callback])

Returns whether the specified [Market](#market) is a container for the Ethereum address of Reporting Participant contract `_shadyReportingParticipant`. If the Market is a container for the Reporting Participant contract, this function returns `1`; otherwise, it returns `0`. Both the [DisputeCrowdsourcer](#dispute-crowdsourcer-call-api) and [InitialReporter](#initial-reporter-call-api) classes in Augur's Solidity smart contracts are considered Reporting Participants, since they have the parent class `BaseReportingParticipant`.

#### augur.api.Market.isContainerForShareToken({ tx, \_shadyShareToken }[, callback])

Returns whether the specified [Market](#market) is a container for the [Share Token](#share-token-call-api) Ethereum address `_shadyShareToken`. This function will return `1` if `_shadyShareToken` is a Share Token belonging to the [Market](#market), otherwise it will return `0`.

#### augur.api.Market.isFinalized({ tx }[, callback])
Returns `1` if the [Market](#market) has been [Finalized](#finalized-market) (that is, its winning [Payout Distribution Hash](#payout-distribution-hash) is set), or `0` otherwise.

#### augur.api.Market.isInvalid({ tx }[, callback])

Returns `1` if the specified [Market](#market) has been [Finalized](#finalized-market) as [Invalid](#invalid-outcome), or returns `0` otherwise.

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
}, function (error, isNotBetterPrice) { console.log(isNotBetterPrice); });
// example output:
"1"

augur.api.Orders.assertIsNotWorsePrice({
  tx: { to: orders },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _worstOrderId: _orderId
}, function (error, isNotWorstPrice) { console.log(isNotWorstPrice); });
// example output:
"0"

augur.api.Orders.getAmount({ 
  tx: { to: orders }, 
  _orderId: _orderId 
}, function (error, amount) { console.log(amount); });
// example output:
"15"

augur.api.Orders.getBestOrderId({
  tx: { to: orders },
  _type: _type,
  _market: _market,
  _outcome: _outcome
}, function (error, bestOrderID) { console.log(bestOrderID); });
// example output:
"0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

augur.api.Orders.getBetterOrderId({ 
  tx: { to: orders },
  _orderId: "0x49cb49f610b5f6e31ee163a8ad65f964af1088e38c8a1b07f1218177b5e006b5"
}, function (error, betterOrderId) { console.log(betterOrderId); });
// example output:
"0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

augur.api.Orders.getLastOutcomePrice({
  tx: { to: orders },
  _market: _market,
  _outcome: _outcome
}, function (error, lastOutcomePrice) { console.log(lastOutcomePrice); });
// example output:
"490000000000000000"

augur.api.Orders.getMarket({ 
  tx: { to: orders },
  _orderId: _orderId
}, function (error, market) { console.log(market); });
// example output:
"0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Orders.getOrderCreator({ 
  tx: { to: orders },
  _orderId: _orderId 
}, function (error, creator) { console.log(creator); });
// example output:
"0x438f2aeb8a16745b1cd711e168581ebce744ffaa";

augur.api.Orders.getOrderMoneyEscrowed({ 
  tx: { to: orders },
  _orderId: _orderId 
}, function (error, orderMoneyEscrowed) { console.log(orderMoneyEscrowed); });
// example output:
"5000000000000000000"

augur.api.Orders.getOrderSharesEscrowed({ 
  tx: { to: orders },
  _orderId: _orderId
}, function (error, orderSharesEscrowed) { console.log(orderSharesEscrowed); });
// example output:
"0"

augur.api.Orders.getOrderType({ 
  tx: { to: orders },
  _orderId: _orderId 
}, function (error, orderType) { console.log(orderType); });
// example output:
"1"

augur.api.Orders.getOutcome({ 
  tx: { to: orders },
  _orderId: _orderId 
}, function (error, outcome) { console.log(outcome); });
// example output:
"1"

augur.api.Orders.getPrice({ 
  tx: { to: orders },
  _orderId: _orderId 
}, function (error, price) { console.log(price); });
// example output:
"500000000000000000"

augur.api.Orders.getVolume({ 
  tx: { to: orders },
  _market: _market 
}, function (error, volume) { console.log(volume); });
// example output:
"100000000000000000000000"

augur.api.Orders.getWorseOrderId({ 
  tx: { to: orders },
  _orderId: "0x4b538f4de2517f7d7bbb227161981c51c40bf725da9941b3dc02e6c14cafd1f1" 
}, function (error, worseOrderID) { console.log(worseOrderID); });
// example output:
"0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api.Orders.getWorstOrderId({
  tx: { to: orders },
  _type: _type,
  _market: _market,
  _outcome: _outcome
}, function (error, worstOrderID) { console.log(worstOrderID); });
// example output:
"0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api.Orders.isBetterPrice({
  tx: { to: orders },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _orderId: _orderId
}, function (error, isBetterPrice) { console.log(isBetterPrice); });
// example output:
"0"

augur.api.Orders.isWorsePrice({
  tx: { to: orders },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _orderId: _orderId
}, function (error, isWorsePrice) { console.log(isWorsePrice); });
// example output:
"1"
```
#### [Orders Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/Orders.sol)

#### augur.api.Orders.assertIsNotBetterPrice({ tx, \_type, \_fxpPrice, \_betterOrderId }[, callback])

Returns whether the specified `_fxpPrice` is not a better price than the `_betterOrderId` for a given [Order](#order) `_type`. Returns `1` if true, `0` if false.

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
}, function (error, ascendingOrderList) { console.log(ascendingOrderList); });
// example output:
[ "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870",
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0"]

augur.api.OrdersFetcher.descendOrderList({
  tx: { to: ordersFetcher },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _highestOrderId: _orderId
}, function (error, decendingOrderList) { console.log(decendingOrderList); });
// example output:
[ "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e",
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"]

augur.api.OrdersFetcher.findBoundingOrders({
  tx: { to: ordersFetcher },
  _type: _type,
  _fxpPrice: _fxpPrice,
  _bestOrderId: _orderId,
  _worstOrderId: 0,
  _betterOrderId: 0,
  _worseOrderId: 0
}, function (error, boundingOrders) { console.log(boundingOrders); });
// example output:
[ "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0",
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e"]
```
#### [Orders Fetcher Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/OrdersFetcher.sol)

#### augur.api.OrdersFetcher.ascendOrderList({ tx, \_type, \_fxpPrice, \_lowestOrderId }[, callback])

Returns an array containing the order IDs that should be the better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_lowestOrderId` is an order ID expected to be a worse price than the `_fxpPrice` specified for an order of `_type`.

#### augur.api.OrdersFetcher.descendOrderList({ tx, \_type, \_fxpPrice, \_highestOrderId }[, callback])

Returns an array containing the order IDs that should be set to better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_highestOrderId` is an order ID expected to be a better price than the `_fxpPrice` specified for an order of `_type`.

#### augur.api.OrdersFetcher.findBoundingOrders({ tx, \_type, \_fxpPrice, \_bestOrderId, \_worstOrderId, \_betterOrderId, \_worseOrderId }[, callback])

Returns an array containing the order IDs that should be set to better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_betterOrderId` and `_worseOrderId` should be orders that are better or worse than the `_fxpPrice` for an order of `_type`. `_bestOrderId` and `_worstOrderId` should be the best and worst order IDs on the order book for the specified `_type`.

Reputation Token Call API
-------------------------
```javascript
// Reputation Token Contract Call API Examples:
var reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";

augur.api.ReputationToken.getUniverse({ tx: { to: reputationToken } }, function (error, universe) { console.log(universe); });
// example output:
"0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReputationToken.getTopMigrationDestination({ tx: { to: reputationToken } }, function (error, topMigrationDestination) { console.log(topMigrationDestination); });
// example output:
"0x1aa30942000ac72dee6580e1ac32d1d01ac1af00"
```
#### [Reputation Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/ReputationToken.sol)

The Reputation Token, known as [REP](#rep), is the key that allows Augur's [Decentralized Oracle](#decentralized-oracle) system to function, and by extension the entirety of Augur. REP has three major functions, it's used to [Report](#report) on the [Outcome](#outcome) of a [Market](#market), [Challenge](#challenge) the [Tentative Outcome](#tentative-outcome) of a Market, and as a [Bond](#no-show-rep-bond) when creating a Market. [Reporters](#reporter) Stake REP on the Outcome of a Market as a show of confidence in their Report. If the Reporter correctly staked on the [Final Outcome](#final-outcome) of the Market they can claim their REP back, earn [Reporting Fees](#reporting-fee) proportional to their staked REP, and a portion of the REP incorrectly staked on other Outcomes.

REP is also used to Challenge the Tentative Outcome of Reports during the [Dispute Round Phase](#dispute-round-phase). If the Challenge successfully changes the Tentative Outcome of a Market and that outcome becomes the Final Outcome, the [Dispute Bond](#dispute-bond) holder can redeem the bond for up to double the REP it cost to place. When creating a Market, the Market Creator is required to specify a Designated Reporter and pay a [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) to ensure the Designated Reporter shows up. If the Designated Reporter doesn't show up, the Market Creator's Designated Report No-Show REP Bond will go to the First Public Reporter to Report on the Market. Their gas cost for the Report transaction will be covered by the Market Creator's [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond), and the Designated Report No-Show REP Bond is added to whatever the First Public Reporter staked, there by improving her stake and potential rewards if correctly staked.

The Reputation Token is an ERC-20 token that implements all of the required functions listed in the [ERC-20 Token Standard](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md). It does not, however, implement the optional functions.

#### augur.api.ReputationToken.getUniverse({ tx }[, callback])

Returns the [Universe](#universe) address for the [REP](#rep) address provided. All of Augur's information belongs to a Universe and each Universe has it's own REP contract. New Universes are created in the event of a [Fork](#fork).

#### augur.api.ReputationToken.getTopMigrationDestination({ tx }[, callback])

As mentioned in the previous method description, all of Augur's [Universes](#universe) have their own [REP](#rep) contract, specific to that Universe. In the event of a [Fork](#fork) occurring, REP holders are expected to migrate their REP to one of the newly created [Child Universes](#child-universe). The Child Universe with the most REP migrated to it at the end of the 60 day [Fork Period](#fork-period) will be the only Universe that allows for [Settlement](#settlement) on the [Forked Market](#forked-market) and will be the Universe that all pending [Markets](#market) will migrate to for [Reporting](#report). What this method does is returns the Universe address of the currently "winning" migration destination. In other words, this returns the Universe with the most REP migrated to it so far.

Share Token Call API
--------------------
```javascript
// Share Token Contract Call API Examples:
var shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";

augur.api.ShareToken.getMarket({ tx: { to: shareToken } }, function (error, market) { console.log(market); });
// example output:
"0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.ShareToken.getOutcome({ tx: { to: shareToken } }, function (error, outcome) { console.log(outcome); });
// example output:
"1"
```
#### [Share Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/ShareToken.sol)

The Share Token is an ERC-20 token that implements all of the required functions listed in the [ERC-20 Token Standard](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md). It does not, however, implement the optional functions. Within Augur, it represents [Shares](#share) in [Market](#market) [Outcomes](#outcome).

#### augur.api.ShareToken.getMarket({ tx }[, callback])

Returns the [Market](#market) Ethereum address for the specified ShareToken.

#### augur.api.ShareToken.getOutcome({ tx }[, callback])

Returns the [Outcome](#outcome) of the [Market](#market) that the specified ShareToken is for.

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

augur.api.Universe.getForkReputationGoal({ tx: { to: universe } }, function (error, forkReputationGoal) { console.log(forkReputationGoal); });
// example output:
"150657"

augur.api.Universe.getInitialReportStakeSize({ tx: { to: universe } }, function (error, initialReportStakeSize) { console.log(initialReportStakeSize); });
// example output:
"0x000000000000000000000000000000000000000000000000026d285191d096ea"

augur.api.Universe.getNextFeeWindow({ tx: { to: universe } }, function (error, nextFeeWindow) { console.log(nextFeeWindow); });
// example output:
"0x45659544b89cce1dd53b1b566862189b25adec49"

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

augur.api.Universe.getRepMarketCapInAttoeth({ tx: { to: universe } }, function (error, repMarketCapInAttoeth) { console.log(repMarketCapInAttoeth); });
// example output:
"985864880000000000"

augur.api.Universe.getReputationToken({ tx: { to: universe } }, function (error, reputationTokenAddress) { console.log(reputationTokenAddress); });
// example output:
"0x2fb561b2bdbcd1ae1995bdd6aff6776d6f4292f2"

augur.api.Universe.getTargetRepMarketCapInAttoeth({ tx: { to: universe } }, function (error, targetRepMarketCapInAttoeth) { console.log(targetRepMarketCapInAttoeth); });
// example output:
"985865890000000000"

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
}, function (error, isContainerForFeeWindow) { console.log(isContainerForFeeWindow); });
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

Returns the Ethereum address of a [Universe's](#universe) [Child Universe](#child-universe) that has its [Forked Market](#forked-market)'s [Final Outcome](#final-outcome) set to `_parentPayoutDistributionHash` [Payout Distribution Hash](#payout-distribution-hash). The Ethereum address is returned as a hexidecimal string.

#### augur.api.Universe.getCurrentFeeWindow({ tx }[, callback])

Returns the Ethereum address of the current running [Fee Window](#reporting-window) of a [Universe](#universe). Every Universe has a Fee Window that runs for a duration of 7 days before immediately starting the next Window. The Ethereum address is returned as a hexidecimal string.

#### augur.api.Universe.getDisputeRoundDurationInSeconds({ tx }[, callback])

Returns the number of seconds in a [Universe's](#universe) [Dispute Round](#dispute-round).

#### augur.api.Universe.getFeeWindow({ tx, \_feeWindowId  }[, callback])

Returns the Ethereum address of the Fee Window `_feeWindowId` in a [Universe](#universe), as a hexidecimal string.

#### augur.api.Universe.getFeeWindowByTimestamp({ tx, \_timestamp }[, callback])

Returns the Ethereum address of the Fee Window `_feeWindowId` running at `_timestamp` in a [Universe](#universe), as a hexidecimal string.

#### augur.api.Universe.getFeeWindowForForkEndTime({ tx } [, callback])

Returns the Ethereum address of the Fee Window `_feeWindowId` in a [Universe](#universe) once the current [Fork](#fork) ends, as a hexidecimal string.

#### augur.api.Universe.getFeeWindowId({ tx, \_timestamp } [, callback])

Returns the [Fee Window](#fee-window) ID for the [Universe](#universe) specified in `tx` and `_timestamp`, as an integer. This is calculated by dividing the timestamp by the [Universe's](#universe) Fee Window duration in seconds.

#### augur.api.Universe.getForkEndTime({ tx }[, callback])

Returns the timestamp for when the [Fork Phase](#fork-period) ends that was started on the [Universe](#universe) specified in `tx`. If there is no [Forked Market](#forked-market) in the Universe, this function will return 0.

#### augur.api.Universe.getForkingMarket({ tx }[, callback])

Returns the Ethereum address of the [Market](#market) that the [Universe](#universe) specified in `tx` is [Forking](#fork) over. This returns the null address (0x0000000000000000000000000000000000000000) if the Universe has never Forked and there is no [Forked Market](#forked-market).

#### augur.api.Universe.getForkReputationGoal({ tx }[, callback])

Returns the estimated amount of [REP](#rep) that must be migrated to one [Child Universe](#child-universe) in order to allow a [Fork](#fork) in the [Universe](#universe) specified in `tx` to be [Finalized](#finalized-market) before the end of the [Fork Phase](#fork-period).

#### augur.api.Universe.getInitialReportStakeSize({ tx }[, callback])

Returns either the size of the [No-Show REP Bond](#no-show-rep-bond) or the size of the Stake placed on the [Designated Report](#designated-report) (whichever is greater), in [attoREP](#atto-prefix), as a hexadecimal string.

#### augur.api.Universe.getNextFeeWindow({ tx }[, callback])

Returns the Ethereum address of the [Fee Window](#fee-window) coming up after the current Fee Window ends in the [Universe](#universe) specified in `tx`. The Ethereum address is returned as a hexadecimal string.

#### augur.api.Universe.getOpenInterestInAttoEth({ tx }[, callback])

Returns the total value of all [Complete Sets](#complete-sets) that exist across all [Markets](#market) the [Universe](#universe) specified in `tx`, priced in [attoETH](#atto-prefix). This value is returned as an integer.

#### augur.api.Universe.getParentPayoutDistributionHash({ tx }[, callback])

Returns the [Payout Distribution Hash](#payout-distribution-hash) of a [Universe's](#universe) [Parent Universe](#parent-universe) for a [Child Universe](#child-universe) specified in `tx`. The Payout Distribution Hash is a hash of the winning [Outcome](#outcome) of the [Forked Market](#forked-market).

#### augur.api.Universe.getParentUniverse({ tx }[, callback])

Returns the [Parent Universe](#parent-universe) Ethereum address of the [Universe](#universe) specified in `tx`, as a hexidecimal string. When a [Fork](#fork) occurs, [Child Universes](#child-universe) are created and the original [Universe](#universe) that contained the [Forked Market](#forked-market) would become a Parent Universe to the newly created Universes. If this is called on the [Genesis Universe](#genesis-universe), it will return 0, as the first Universe has no Parent Universe.

#### augur.api.Universe.getPreviousFeeWindow({ tx }[, callback])

Returns the Ethereum address of the previous [Fee Window](#fee-window) for the [Universe](#universe) specified in `tx`, as a hexidecimal string.

#### augur.api.Universe.getRepMarketCapInAttoeth({ tx }[, callback])

Returns an estimate for the market cap of [REP](#rep), priced in [attoETH](#atto-prefix). This estimate is updated manually by the Augur team, roughly once every [Fee Window](#fee-window). It is used by Augur to set the price of the [Reporting Fee](#reporting-fee).

#### augur.api.Universe.getReputationToken({ tx }[, callback])

Returns the Ethereum address of the [Reputation Token](#rep) for the [Universe](#universe) specified in `tx`, as a hexidecimal string. REP associated with this contract address are usable within this Universe.

#### augur.api.Universe.getTargetRepMarketCapInAttoeth({ tx }[, callback])

Returns the [REP](#rep) market cap that Augur targets when calculating [Reporting Fees](#reporting-fee), in [attoETH](#atto-prefix). Augur attempts to set Reporting Fees such that the REP market cap equals 5 times the amount of [Open Interest](#open-interest). 

#### augur.api.Universe.getWinningChildUniverse({ tx }[, callback])

Returns the Ethereum address of the [Winning Universe](#winning-universe) for a particular [Universe](#universe) that has [Forked](#fork).

#### augur.api.Universe.isContainerForFeeWindow({ tx, \_shadyFeeWindow }[, callback])

Returns whether the specific `universe` is a container for the [Fee Window](#fee-window) `_shadyFeeWindow` Ethereum address. Returns `1` if true or `0` if false. Every Fee Window belongs to a [Universe](#universe), and this method is used to see if a specific Fee Window address belongs to the Universe in question.

#### augur.api.Universe.isContainerForMarket({ tx, \_shadyMarket }[, callback])

Returns whether the specific `universe` is a container for the [Market](#market) `_shadyMarket` Ethereum address. Returns `1` if true or `0` if false. All Markets are created within a [Universe](#universe), and this function is used to help confirm if a Market exists within the Universe in question.

#### augur.api.Universe.isContainerForReportingParticipant({ tx, \_shadyReportingParticipant }[, callback])

Returns `1` if the specified [Universe](#universe) is a container for `_shadyReportingParticipant` Ethereum address provided, as a hexadecimal string. Otherwise, this function returns `0`. Both the `DisputeCrowdsourcers` and `InitialReporter` classes in Augur's Solidity smart contracts are considered Reporting Participants, since they have the parent class `BaseReportingParticipant`.

#### augur.api.Universe.isContainerForShareToken({ tx, \_shadyShareToken }[, callback])

[Shares](#share) are represented within Augur's smart contracts as [ERC-20](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md) tokens called Share Tokens. Returns whether the specific `universe` is a container for the Share Token `_shadyShareToken` Ethereum address. Returns `1` if true or `0` if false. 

#### augur.api.Universe.isForking({ tx }[, callback])

Returns `1` if the current [Universe](#universe) is Forking or `0` otherwise.

#### augur.api.Universe.isParentOf({ tx, \_shadyChild }[, callback])

Returns whether the specific `universe` is a container for the `_shadyChild` [Child Universe](#child-universe) Ethereum address provided. Returns `1` if true or `0` if false. This function can be used to see if a specific [Universe](#universe) is the [Parent Universe](#parent-universe) to a Child Universe.
