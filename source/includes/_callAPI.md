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


Dispute Bond Token Call API
---------------------------
```javascript
// Dispute Bond Token Contract Call API Examples:
var disputeBondToken = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
var _address = "0x3d62bafc1791752393384b902f813da861ddedd9";

augur.api.DisputeBondToken.balanceOf({
  disputeBondToken: disputeBondToken,
  _address: _address
}, function (balance) { /* ... */ })
// example output:
balance = "1"

augur.api.DisputeBondToken.getBondHolder({ disputeBondToken: disputeBondToken }, function (bondHolder) { /* ... */ })
// example output:
bondHolder = "0x3d62bafc1791752393384b902f813da861ddedd9"

augur.api.DisputeBondToken.getBondRemainingToBePaidOut({ disputeBondToken: disputeBondToken }, function (bondRemainingToBePaidOut) { /* ... */ })
// example output:
bondRemainingToBePaidOut = "1100000000000000000000"

augur.api.DisputeBondToken.getUniverse({ disputeBondToken: disputeBondToken }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.DisputeBondToken.getDisputedPayoutDistributionHash({ disputeBondToken: disputeBondToken }, function (disputedPayoutDistributionHash) { /* ... */ })
// example output:
disputedPayoutDistributionHash = "0xff89be2020af3cb2ca244eb862df2d350300904a96039eb53cbacf380f13f21b"

augur.api.DisputeBondToken.getMarket({ disputeBondToken: disputeBondToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.DisputeBondToken.getReputationToken({ disputeBondToken: disputeBondToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.DisputeBondToken.getTotalSupply({ disputeBondToken: disputeBondToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "1"

augur.api.DisputeBondToken.getTypeName({ disputeBondToken: disputeBondToken }, function (typeName) { /* ... */ })
// example output:
typeName = "DisputeBondToken";
```
#### [Dispute Bond Token Contract Code](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/reporting/DisputeBondToken.sol)

The [Dispute Bond](#dispute-bond) Token is used by [REP](#rep) holders to [Challenge](#challenge) the [Outcome](#outcome) of [Markets Awaiting Finalization](#market-awaiting-finalization). The Dispute Bond is only purchasable with REP. Only one Dispute Bond needs to be purchased for a Market per round of [Reporting](#reporting) in order for the Market to move to the next state of [Reporting](#report) in the next upcoming [Reporting Window](#reporting-window). If a Challenge is successful, which means the [Final Outcome](#final-outcome) of the [Market](#market) is something other than the [Proposed Outcome](#proposed-outcome) that was disputed, then the Bond Holder is entitled to up to 2x the Dispute Bond cost in REP. After 2x the cost of the Dispute Bond is paid to the Bond Holder any remaining REP is redistributed as normal to other Reporters who correctly staked on the Final Outcome.

#### augur.api.DisputeBondToken.balanceOf({ disputeBondToken, \_address }[, callback])

This method will returns the balance for a specified `disputeBondToken` owned by the provided `_address`. Returns `1` or `0` as you cannot own more than `1` [Dispute Bond](#dispute-bond) Token, and only one is required for a [Market](#market)'s [Proposed Outcome](#proposed-outcome) to be considered [Challenged](#challenged).

#### augur.api.DisputeBondToken.getBondHolder({ disputeBondToken }[, callback])

This transaction will return the account address of the bond holder for a specified `disputeBondToken`. This is the person who owns/purchased the bond and would be the person to refund if they correctly [Challenged](#challenge) the [Proposed Outcome](#proposed-outcome) of a [Market Awaiting Finalization](#market-awaiting-finalization).

#### augur.api.DisputeBondToken.getBondRemainingToBePaidOut({ disputeBondToken }[, callback])

This transaction will return the amount of REP, denoted in attotokens, of bond remaining to be paid out for a specified `disputeBondToken`. Bonds are paid back to the Bond Holder if they correctly [Challenged](#challenge) a [Proposed Outcome](#proposed-outcome) and the [Final Outcome](#final-outcome) for the [Market](#market) is something different than the Challenged Proposed Outcome. Otherwise, the Bond is paid out to [Reporters](#reporters) who confirmed the Proposed Outcome to be the Final Outcome of the Market.

#### augur.api.DisputeBondToken.getUniverse({ disputeBondToken }[, callback])

This transaction will return the [Universe](#universe) that contained the [Market](#market) that a specified `disputeBondToken` belongs to.

#### augur.api.DisputeBondToken.getDisputedPayoutDistributionHash({ disputeBondToken }[, callback])

This transaction will return the [Payout Distribution Hash](#payout-distribution-hash) for the dispute payout given a specified `disputeBondToken`. The Payout Distribution Hash in this case refers the Hash that was the [Proposed Outcome](#proposed-outcome) but was [Challenged](#challenged) by the [Dispute Bond](#dispute-bond).

#### augur.api.DisputeBondToken.getMarket({ disputeBondToken }[, callback])

This transaction will return the [Market](#market) that a specified `disputeBondToken` belongs to. All Dispute Bond Tokens belong to a Market, and there can only be one Dispute Bond Token per [Dispute Phase](#dispute-phase) for that Market.

#### augur.api.DisputeBondToken.getReputationToken({ disputeBondToken }[, callback])

This transaction will return the [Reputation Token](#rep) address used by the [Universe](#universe) that contains [Market](#market) of the specified `disputeBondToken`.

#### augur.api.DisputeBondToken.getTotalSupply({ disputeBondToken }[, callback])

This transaction will return the total supply of a specified `disputeBondToken`. This should always return `1` as only 1 Dispute Bond Token can be purchased per [Dispute Phase](#dispute-phase) and therefore every Dispute Bond Token is unique.

#### augur.api.DisputeBondToken.getTypeName({ disputeBondToken }[, callback])

Returns the type name of the specified `disputeBondToken`, this will always return "DisputeBondToken".

Market Call API
----------------
```javascript
// Market Contract Call API Examples:
var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Market.getAllReportersDisputeBondToken({ market: market }, function (allReportersDisputeBondToken) { /* ... */ })
// example output:
allReportersDisputeBondToken = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";

augur.api.Market.getDenominationToken({ market: market }, function (denominationToken) { /* ... */ })
// example output:
denominationToken = "0x30e3852448f4ab5d62bbf7544ca3c92daca5c957"

augur.api.Market.getDesignatedReportDisputeDueTimestamp({ market: market }, function (designatedReporterDisputeDueTimestamp) { /* ... */ })
// example output:
designatedReporterDisputeDueTimestamp = "1500907130"

augur.api.Market.getDesignatedReportDueTimestamp({ market: market }, function (designatedReporterDueTimestamp) { /* ... */ })
// example output:
designatedReporterDueTimestamp = "1500647930"

augur.api.Market.getDesignatedReporterDisputeBondToken({ market: market }, function (designatedReporterDisputeBondToken) { /* ... */ })
// example output:
designatedReporterDisputeBondToken = "0xe783e32cfeea2d2be8df1ae6e978e6d4de63fe26"

augur.api.Market.getEndTime({ market: market }, function (endTime) { /* ... */ })
// example output:
endTime = "1500388730";

augur.api.Market.getFinalizationTime({ market: market }, function (finalizationTime) { /* ... */ })
// example output:
finalizationTime = "1500647930";

augur.api.Market.getFinalPayoutDistributionHash({ market: market }, function (finalPayoutDistributionHash) { /* ... */ })
// example output:
finalPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Market.getFinalWinningReportingToken({ market: market }, function (winningReportingToken) { /* ... */ })
// example output:
winningReportingToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.Market.getLimitedReportersDisputeBondToken({ market: market }, function (limitedReportersDisputeBondToken) { /* ... */ })
// example output:
limitedReportersDisputeBondToken = "0x0151f9153f236359ea3605cef0fffff90aba0327";

augur.api.Market.getMarketCreatorSettlementFeeInAttoethPerEth({ market: market }, function (marketCreatorSettlementFee) { /* ... */ })
// example output:
marketCreatorSettlementFee = "20000000000000000"

augur.api.Market.getNumberOfOutcomes({ market: market }, function (numOutcomes) { /* ... */ })
// example output:
numOutcomes = "2"

augur.api.Market.getNumTicks({ market: market }, function (numTicks) { /* ... */ })
// example output:
numTicks = "1000"

var _payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";
augur.api.Market.getPayoutDistributionHashStake({ market: market, _payoutDistributionHash: _payoutDistributionHash }, function (payoutDenominator) { /* ... */ })
// example output:
payoutDenominator = "1100000000000000000"

augur.api.Market.getReportingState({ market: market }, function (reportingState) { /* ... */ })
// example output:
reportingState = "0";

augur.api.Market.getReportingToken({ market: market }, function (reportingToken) { /* ... */ })
// example output:
reportingToken = "0x5caa66408617f77601d0dc19c163621e7f4b8b38"

augur.api.Market.getReportingTokenOrZeroByPayoutDistributionHash({ market: market, _payoutDistributionHash: _payoutDistributionHash }, function (reportingTokenOrZero) { /* ... */ })
// example output:
reportingTokenOrZero = "0x5caa66408617f77601d0dc19c163621e7f4b8b38"

augur.api.Market.getReportingWindow({ market: market }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.Market.getShareToken({
  market: market,
  _outcome: 1
}, function (shareToken) { /* ... */ })
// example output:
shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054"

augur.api.Market.getTentativeWinningPayoutDistributionHash({ market: market }, function (tentativeWinningPayoutDistributionHash) { /* ... */ })
// example output:
tentativeWinningPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.Market.getTypeName({ market: market }, function (typeName) { /* ... */ })
// example output:
typeName = "Market";

augur.api.Market.getUniverse({ market: market }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.Market.getWinningPayoutDistributionHashFromFork({ market: market }, function (winningPayoutDistributionHashFromFork) { /* ... */ })
// example output:
winningPayoutDistributionHashFromFork = "0xab912273ffce2cb2ca24ce2162df2d342322904a96039eb53cba0e34b8b9fc12"

var disputeBondToken = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
augur.api.Market.isContainerForDisputeBondToken({
  market: market,
  _shadyTarget: disputeBondToken
}, function (isContainerForShareToken) { /* ... */ })
// example output:
isContainerForDisputeBondToken = "1"

var reportingToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
augur.api.Market.isContainerForReportingToken({
  market: market,
  _shadyTarget: reportingToken
}, function (isContainerForReportingToken) { /* ... */ })
// example output:
isContainerForReportingToken = "1"

var shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
augur.api.Market.isContainerForShareToken({
  market: market,
  _shadyTarget: shareToken
}, function (isContainerForShareToken) { /* ... */ })
// example output:
isContainerForShareToken = "1"
```
#### [Market Contract Code](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/reporting/Market.sol)

#### augur.api.Market.getAllReportersDisputeBondToken({ market }[, callback])

Returns the [All Reporting](#all-reporting) phase's [Dispute Bond Token](#dispute-bond) address for the `market`. If this [Market](#market) hasn't gone through an All Reporting phase or there was no [Challenge](#challenge) to the [Proposed Outcome](#proposed-outcome) after All Reporting then this will return an empty address `0x0`.

#### augur.api.Market.getDenominationToken({ market }[, callback])

Returns the address of the token used to denominate the specified `market`. A Denomination Token is the ERC20 Token used as the currency to trade on the [Outcome](#outcome) of a [Market](#market).

#### augur.api.Market.getDesignatedReportDisputeDueTimestamp({ market }[, callback])

Returns the timestamp for when a specific `market`'s [Designated Report Phase](#designated-report-phase) and [Designated Dispute Phase](#designated-dispute-phase) should end. Can be up to 6 days after the [Market](#market)'s [End Time](#end-time), or as little as 3 days after the End Time, depends on how quickly the [Designated Reporter](#designated-reporter) [Reports](#report).

#### augur.api.Market.getDesignatedReportDueTimestamp({ market }[, callback])

Returns the timestamp for when a specific `market`'s  [Designated Report Phase](#designated-report-phase) should be completed but doesn't count the [Designated Dispute Phase](#designated-dispute-phase). If the [Designated Reporter](#designated-reporter) has already submit their [Report](#report) for the Designated Report Phase, then this will return the timestamp for when that Report was submit to the [Market](#market).

#### augur.api.Market.getDesignatedReporterDisputeBondToken({ market }[, callback])

Returns the [Dispute Bond](#dispute-bond) Token address for the [Designated Reporting](#designated-reporting) round for a specified `market`. When a Designated [Report](#report) is [Challenged](#challenge) a Dispute Bond is created and this function is designed to return that bond address. This returns 0 if the [Market](#market) doesn't have a Designated Report or if the Designated Report hasn't been Challenged.

#### augur.api.Market.getEndTime({ market }[, callback])

Returns the timestamp for when the specified `market`'s event has come to pass. When the [Market](#market)'s [End Time](#end-time) passes the Market either enters the [Designated Report Phase](#designated-report-phase) if a [Designated Reporter](#designated-reporter) is assigned or the Market will await the next [Reporting Window](#reporting-window) to begin a round of [Limited Reporting](#limited-reporting).

#### augur.api.Market.getFinalizationTime({ market }[, callback])

Returns the timestamp for when the specified `market` was [Finalized](#finalized-market). A Finalized Market has a [Final Outcome](#final-outcome) set from successful [Market Resolution](#market-resolution) which cannot be [Challenged](#challenge) and the Market is considered [Settled](#settlement).

#### augur.api.Market.getFinalPayoutDistributionHash({ market }[, callback])

Returns the final [Payout Distribution Hash](#payout-distribution-hash) for a specified `market`. The Final Payout Distribution Hash determines how a [Finalized Market](#finalized-market) payouts out for a specific [Share](#shares) of an [Outcome](#outcome).

#### augur.api.Market.getFinalWinningReportingToken({ market }[, callback])

Returns the Reporting Token address for the [Final Outcome](#final-outcome) of a specific `market`. If the [Market](#market) isn't [Settled](#market-settlement) then this will return a null address, `0x0`.

#### augur.api.Market.getLimitedReportersDisputeBondToken({ market }[, callback])

Returns the [Dispute Bond](#dispute-bond) Token address for the [Limited Reporting](#limited-reporting) round for a specified `market`. When a Limited [Report](#report) is [Challenged](#challenge) a Dispute Bond is created and this function is designed to return that bond address. This returns 0 if the [Market](#market) doesn't have a Limited Report or if the Limited Report hasn't been Challenged.

#### augur.api.Market.getMarketCreatorSettlementFeeInAttoethPerEth({ market }[, callback])

Returns the [Creator Fee](#creator-fee) set by the [Market Creator](#market-creator), denominated in attotokens per [settlement](#settlement) of a [Complete Set](#complete-set), for the `market`.

#### augur.api.Market.getNumberOfOutcomes({ market }[, callback])

Returns the number of [Outcomes](#outcome) for a specified `market`. The number of outcomes is the number of potential results of the [Market](#market).

#### augur.api.Market.getNumTicks({ market }[, callback])

Returns the [Number of Ticks](#number-of-ticks) set for a specific `market`. The Number of Ticks represents the number of valid price points between the [Market](#market)'s [Minimum Price](#minimum-display-price) and [Maximum Price](#maximum-display-price).

#### augur.api.Market.getPayoutDistributionHashStake({ market, \_payoutDistributionHash }[, callback])

Returns the staked amount of [REP](#rep), in attorep, for a specified `_payoutDistributionHash` of a `market`. [Reporters](#reporter) who stake REP on the same [Outcome](#outcome) during the [Reporting Phase](#reporting-phase) will have identical [Payout Distribution Hashs](#payout-distribution-hash). This method returns the total amount of staked REP for a specific Payout Distribution Hash. The Hash with the most REP staked on [it is considered the Proposed Outcome](#proposed-outcome) while the [Market](#market) is [Awaiting Finalization](#awaiting-finalization). If the [Dispute Phase](#dispute-phase) passes without a [Challenge](#challenge) to the Proposed Outcome then the Market is [Finalized](#finalized-market) and the Proposed Outcome becomes the [Final Outcome](#final-outcome).

#### augur.api.Market.getReportingState({ market }[, callback])

Returns the current [Reporting](#report) State that the `market` is in. This method returns a number between 0 and 12, which corresponds to the various Reporting States outlined in the [Reporting Section](#reporting) of the documentation.

#### augur.api.Market.getReportingToken({ market, \_payoutNumerators }[, callback])

Returns the Reporting Token address for `_payoutNumerators` on the `market` specified.

#### augur.api.Market.getReportingTokenOrZeroByPayoutDistributionHash({ market, \_payoutDistributionHash }[, callback])

Returns a Reporting Token address, or 0 if there is no reporting token for that [Payout Distribution Hash](#payout-distribution-hash), for a specific `_payoutDistributionHash` for a `market`.

#### augur.api.Market.getReportingWindow({ market }[, callback])

Returns the reporting window address for the specified `market`.

#### augur.api.Market.getShareToken({ market, \_outcome }[, callback])

Returns the share token's address for the specified `market` and `_outcome`.

#### augur.api.Market.getTentativeWinningPayoutDistributionHash({ market }[, callback])

Returns the tentatively winning Payout Distribution Hash given a specified `market`.

#### augur.api.Market.getTypeName({ market }[, callback])

Returns the type name for the specified `market`, should always return "Market".

#### augur.api.Market.getUniverse({ market }[, callback])

Returns the [Universe](#universe) address of the Universe that the specified `market` is contained within.

#### augur.api.Market.getWinningPayoutDistributionHashFromFork({ market }[, callback])

Returns the winning [Payout Distribution Hash](#payout-distribution-hash), which is used to identify the Winning [Universe](#universe), after a [Fork](#fork) for a given `market`.

#### augur.api.Market.isContainerForDisputeBondToken({ market, \_shadyTarget }[, callback])

Returns wether the specific `market` is a container for the `_shadyTarget` address provided. A `_shadyTarget` will return true if it is a automated report dispute bond token, a limited reporter dispute bond token, or an all reporters dispute bond token for this `market`. Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForReportingToken({ market, \_shadyTarget }[, callback])

Returns wether the specific `market` is a container for the `_shadyTarget` address provided, which is the reporting token address we intend to check. Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForShareToken({ market, \_shadyTarget }[, callback])

Returns wether the specific `market` is a container for the `_shadyTarget` address provided, which is the share token address we intend to check. Returns `1` if true, `0` if false.

Registration Token Call API
---------------------------
```javascript
// Registration Token Contract Call API Examples:
var registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
var _owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
var _spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.RegistrationToken.allowance({
  registrationToken: registrationToken,
  _owner: _owner,
  _spender: _spender
}, function (allowance) { /* ... */ })
// example output:
allowance = "1";

augur.api.RegistrationToken.balanceOf({
  registrationToken: registrationToken,
  _owner: _owner
}, function (balance) { /* ... */ })
// example output:
balance = "1";

augur.api.RegistrationToken.getUniverse({ registrationToken: registrationToken }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.RegistrationToken.getPeakSupply({ registrationToken: registrationToken }, function (peakSuppy) { /* ... */ })
// example output:
peakSuppy = "234"

augur.api.RegistrationToken.getReportingWindow({ registrationToken: registrationToken }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.RegistrationToken.getReputationToken({ registrationToken: registrationToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.RegistrationToken.getTotalSupply({ registrationToken: registrationToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "215";

augur.api.RegistrationToken.getTypeName({ registrationToken: registrationToken }, function (typeName) { /* ... */ })
// example output:
typeName = "RegistrationToken";
```
#### [Registration Token Contract Code](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/reporting/RegistrationToken.sol)

#### augur.api.RegistrationToken.allowance({ registrationToken, \_owner, \_spender }[, callback])

Returns the allowance that a specified `_spender` can spend of the `_owner`'s `registrationToken`s.

#### augur.api.RegistrationToken.balanceOf({ registrationToken, \_owner }[, callback])

Returns the token balance for the specified `registrationToken` owned by the `_owner` provided.

#### augur.api.RegistrationToken.getUniverse({ registrationToken }[, callback])

Returns the Universe address for the specified `registrationToken`'s Reporting Window.

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
var reportingToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
var _owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
var _spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.ReportingToken.allowance({
  reportingToken: reportingToken,
  _owner: _owner,
  _spender: _spender
}, function (allowance) { /* ... */ })
// example output:
allowance = "1"

augur.api.ReportingToken.balanceOf({
  reportingToken: reportingToken,
  _owner: _owner
}, function (balance) { /* ... */ })
// example output:
balance = "1"

augur.api.ReportingToken.getUniverse({ reportingToken: reportingToken }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReportingToken.getMarket({ reportingToken: reportingToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.ReportingToken.getPayoutDistributionHash({ reportingToken: reportingToken }, function (payoutDistributionHash) { /* ... */ })
// example output:
payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.ReportingToken.getPayoutNumerator({
  reportingToken: reportingToken,
  index: 0
}, function (payoutNumerator) { /* ... */ })
// example output:
payoutNumerator = "1"

augur.api.ReportingToken.getRegistrationToken({ reportingToken: reportingToken }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api.ReportingToken.getReportingWindow({ reportingToken: reportingToken }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.ReportingToken.getReputationToken({ reportingToken: reportingToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.ReportingToken.getTotalSupply({ reportingToken: reportingToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "210"

augur.api.ReportingToken.getTypeName({ reportingToken: reportingToken }, function (typeName) { /* ... */ })
// example output:
typeName = "ReportingToken";
```
#### [Reporting Token Contract Code](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/reporting/ReportingToken.sol)

#### augur.api.ReportingToken.allowance({ reportingToken, \_owner, \_spender }[, callback])

Returns the allowance that a specified `_spender` can spend of the `_owner`'s `reportingToken`s.

#### augur.api.ReportingToken.balanceOf({ reportingToken, \_owner }[, callback])

Returns the token balance for the specified `reportingToken` owned by the provided `_owner`.

#### augur.api.ReportingToken.getUniverse({ reportingToken }[, callback])

Returns the Universe address for the specified `reportingToken`'s Reporting Window.

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
var reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.ReportingWindow.getUniverse({ reportingWindow: reportingWindow }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReportingWindow.getDisputeEndTime({ reportingWindow: reportingWindow }, function (disputeEndTime) { /* ... */ })
// example output:
disputeEndTime = "1500907130"

augur.api.ReportingWindow.getDisputeStartTime({ reportingWindow: reportingWindow }, function (disputeStartTime) { /* ... */ })
// example output:
disputeStartTime = "1500647930"

augur.api.ReportingWindow.getEndTime({ reportingWindow: reportingWindow }, function (endTime) { /* ... */ })
// example output:
endTime = "1500388730"

augur.api.ReportingWindow.getMaxReportsPerLimitedReporterMarket({ reportingWindow: reportingWindow }, function (maxReportsPerLimitedReporterMarket) { /* ... */ })
// example output:
maxReportsPerLimitedReporterMarket = "9"

var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.ReportingWindow.getNumberOfReportsByMarket({
  reportingWindow: reportingWindow,
  _market: _market
}, function (numOfReportsByMarket) { /* ... */ })
// example output:
numOfReportsByMarket = "10";

augur.api.ReportingWindow.getRegistrationToken({ reportingWindow: reportingWindow }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api.ReportingWindow.getReportingEndTime({ reportingWindow: reportingWindow }, function (reportingEndTime) { /* ... */ })
// example output:
reportingEndTime = "1500647900"

augur.api.ReportingWindow.getReportingStartTime({ reportingWindow: reportingWindow }, function (reportingStartTime) { /* ... */ })
// example output:
reportingStartTime = "14998315100"

var _reporter = "0x2cd999e2f90dfc237ccbc52e2a469e1e11221f75";
augur.api.ReportingWindow.getReportsByReporter({
  reportingWindow: reportingWindow,
  _reporter: _reporter
}, function (reportsByReporter) { /* ... */ })
// example output:
reportsByReporter = "0x03198001d1c223b2fdb1866703a7c2e4d5313f80"

augur.api.ReportingWindow.getReputationToken({ reportingWindow: reportingWindow }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.ReportingWindow.getRequiredReportsPerReporterForlimitedReporterMarkets({ reportingWindow: reportingWindow }, function (requiredReportsPerReporterForLimitedReporterMarkets) { /* ... */ })
// example output:
requiredReportsPerReporterForLimitedReporterMarkets = "2"

augur.api.ReportingWindow.getStartTime({ reportingWindow: reportingWindow }, function (startTime) { /* ... */ })
// example output:
startTime = "14995895900"

augur.api.ReportingWindow.getTargetReportsPerLimitedReporterMarket({ reportingWindow: reportingWindow }, function (targetReportsPerLimitedReporterMarket) { /* ... */ })
// example output:
targetReportsPerLimitedReporterMarket = "3"

augur.api.ReportingWindow.getTargetReportsPerReporter({ reportingWindow: reportingWindow }, function (targetReportsPerReporter) { /* ... */ })
// example output:
targetReportsPerReporter = "5"

augur.api.ReportingWindow.isActive({ reportingWindow: reportingWindow }, function (isActive) { /* ... */ })
// example output:
isActive = "1"

augur.api.ReportingWindow.isContainerForMarket({
  reportingWindow: reportingWindow,
  _shadyMarket: _market
}, function (isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1"

var _registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
augur.api.ReportingWindow.isContainerForRegistrationToken({
  reportingWindow: reportingWindow,
  _shadyRegistrationToken: _registrationToken
}, function (isContainerForRegistrationToken) { /* ... */ })
// example output:
isContainerForRegistrationToken = "1"

augur.api.ReportingWindow.isDisputeActive({ reportingWindow: reportingWindow }, function (isDisputeActive) { /* ... */ })
// example output:
isDisputeActive = "1"

augur.api.ReportingWindow.isDoneReporting({
  reportingWindow: reportingWindow,
  _reporter: _reporter
}, function (isDoneReporting) { /* ... */ })
// example output:
isDoneReporting = "1"

augur.api.ReportingWindow.isReportingActive({ reportingWindow: reportingWindow }, function (isReportingActive) { /* ... */ })
// example output:
isReportingActive = "1"
```
#### [Reporting Window Contract Code](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/reporting/ReportingWindow.sol)

#### augur.api.ReportingWindow.getUniverse({ reportingWindow }[, callback])

Returns the universe address that contains the specified `reportingWindow`.

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
var reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
var _owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
var _spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.ReputationToken.allowance({
  reputationToken: reputationToken,
  _owner: _owner,
  _spender: _spender
}, function (allowance) { /* ... */ })
// example output:
allowance = "200"

augur.api.ReputationToken.assertReputationTokenIsLegit({ _shadyReputationToken: reputationToken }, function (isLegitREPToken) { /* ... */ })
// example output:
isLegitREPToken = true;

augur.api.ReputationToken.balanceOf({
  reputationToken: reputationToken,
  _owner: _owner
}, function (balance) { /* ... */ })
// example output:
balance = "1000"

augur.api.ReputationToken.getUniverse({ reputationToken: reputationToken }, function (universe) { /* ... */ })
// example output:
universe = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReputationToken.getTopMigrationDestination({ reputationToken: reputationToken }, function (topMigrationDestination) { /* ... */ })
// example output:
topMigrationDestination = "0x1aa30942000ac72dee6580e1ac32d1d01ac1af00"

augur.api.ReputationToken.getTotalSupply({ reputationToken: reputationToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "11000000"

augur.api.ReputationToken.getTypeName({ reputationToken: reputationToken }, function (typeName) { /* ... */ })
// example output:
reputationToken = "ReputationToken";
```
#### [Reputation Token Contract Code](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/reporting/ReputationToken.sol)

#### augur.api.ReputationToken.allowance({ reputationToken, \_owner, \_spender }[, callback])

Returns the allowance that a specified `_spender` can spend of the `_owner`'s `reputationToken`s.

#### augur.api.ReputationToken.assertReputationTokenIsLegit({ reputationToken, \_shadyReputationToken }[, callback])

Returns true or false depending on if the `_shadyReputationToken` provided is actually a legitimate token belonging to the correct universe for a specified `reputationToken`.

#### augur.api.ReputationToken.balanceOf({ reputationToken, \_owner }[, callback])

Returns the token balance of the specified `reputationToken` owned by the `_owner` provided.

#### augur.api.ReputationToken.getUniverse({ reputationToken }[, callback])

Returns the Universe address for the specified `reputationToken`.

#### augur.api.ReputationToken.getTopMigrationDestination({ reputationToken }[, callback])

Returns the top migration destination address for the specified `reputationToken`.

#### augur.api.ReputationToken.getTotalSupply({ reputationToken }[, callback])

Returns the current total supply of the specified `reputationToken`.

#### augur.api.ReputationToken.getTypeName({ reputationToken }[, callback])

Returns the type name for a specified `reputationToken`, this should always return "ReputationToken".

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

augur.api.Orders.getBestOrderWorstOrderHash({
  _market: _market,
  _outcome: _outcome,
  _type: _type
}, function (bestOrderWorstOrderHash) { /* ... */ })
// example output:
bestOrderWorstOrderHash = "0x502caca9a38d2456d8ac2d743b49982a857222b53039f7eee8de94e5d91aec99"

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

augur.api.Orders.getOrderMoneyEscrowed({ _orderId: _orderId }, function (orderMoneyEscrowed) { /* ... */ })
// example output:
orderMoneyEscrowed = "5000000000000000000"

augur.api.Orders.getOrderMaker({ _orderId: _orderId }, function (owner) { /* ... */ })
// example output:
owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";

augur.api.Orders.getOrderSharesEscrowed({ _orderId: _orderId }, function (orderSharesEscrowed) { /* ... */ })
// example output:
orderSharesEscrowed = "0";

augur.api.Orders.getOutcome({ _orderId: _orderId }, function (orderOutcome) { /* ... */ })
// example output:
orderOutcome = "1";

augur.api.Orders.getPrice({ _orderId: _orderId }, function (price) { /* ... */ })
// example output:
price = "500000000000000000";

augur.api.Orders.getTradeType({ _orderId: _orderId }, function (type) { /* ... */ })
// example output:
type = "1";

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
#### [Orders Contract Code](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/trading/Orders.sol)

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

augur.api.OrdersFetcher.getOrder({ _orderId: _orderId }, function (order) { /* ... */ })
// example output:
order = [ "10000000000000000000",
          "500000000000000000",
          "0x438f2aeb8a16745b1cd711e168581ebce744ffaa",
          "5000000000000000000",
          "0",
          "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0",
          "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e",
          "42000000000000"  ]

augur.api.OrdersFetcher.getOrderIds({
  _type: _type,
  _market: _market,
  _outcome: _outcome,
  _startingOrderId: _orderId,
  _numOrdersToLoad: 2
}, function (orderIDs) { /* ... */ })
// example output:
orderIDs = [
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870",
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0"]
```
#### [Orders Fetcher Contract Code](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/trading/OrdersFetcher.sol)

#### augur.api.OrdersFetcher.ascendOrderList({ \_type, \_fxpPrice, \_lowestOrderId }[, callback])

Returns an array containing the order IDs that should be the better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_lowestOrderId` is an order ID expected to be a worse price than the `_fxpPrice` specified for an order of `_type`.

#### augur.api.OrdersFetcher.descendOrderList({ \_type, \_fxpPrice, \_highestOrderId }[, callback])

Returns an array containing the order IDs that should be set to better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_highestOrderId` is an order ID expected to be a better price than the `_fxpPrice` specified for an order of `_type`.

#### augur.api.OrdersFetcher.findBoundingOrders({ \_type, \_fxpPrice, \_bestOrderId, \_worstOrderId, \_betterOrderId, \_worseOrderId }[, callback])

Returns an array containing the order IDs that should be set to better Order ID and worse Order ID respectively for an order inserted at `_fxpPrice`. `_betterOrderId` and `_worseOrderId` should be orders that are better or worse than the `_fxpPrice` for an order of `_type`. `_bestOrderId` and `_worstOrderId` should be the best and worst order IDs on the order book for the specified `_type`.

#### augur.api.OrdersFetcher.getOrder({ \_orderId }[, callback])

Returns a length 8 array containing information about a specified `_orderId`. Information returned includes: amount of attoshares, fixed point display price, owner address, tokens escrowed, shares escrowed, better order id, worse order id, and gas price.

#### augur.api.OrdersFetcher.getOrderIDs({ \_type, \_startingOrderId, \_numOrdersToLoad }[, callback])

Returns an array of order IDs of `_type` starting from the `_startingOrderId` order ID specified. The array will be of length `_numOrdersToLoad`.

Share Token Call API
--------------------
```javascript
// Share Token Contract Call API Examples:
var shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
var _owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
var _spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.ShareToken.allowance({
  shareToken: shareToken,
  _owner: _owner,
  _spender: _spender
}, function (allowance) { /* ... */ })
// example output:
allowance = "100"

augur.api.ShareToken.balanceOf({
  shareToken: shareToken,
  _owner: _owner
}, function (balance) { /* ... */ })
// example output:
balance = "1000"

augur.api.ShareToken.getDecimals({ shareToken: shareToken }, function (decimals) { /* ... */ })
// example output:
decimals = "18"

augur.api.ShareToken.getMarket({ shareToken: shareToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.ShareToken.getName({ shareToken: shareToken }, function (name) { /* ... */ })
// example output:
name = "Shares"

augur.api.ShareToken.getOutcome({ shareToken: shareToken }, function (outcome) { /* ... */ })
// example output:
outcome = "1"

augur.api.ShareToken.getSymbol({ shareToken: shareToken }, function (symbol) { /* ... */ })
// example output:
symbol = "SHARES"

augur.api.ShareToken.isShareToken({ shareToken: shareToken }, function (isShareToken) { /* ... */ })
// example output:
isShareToken = "1"

augur.api.ShareToken.getTotalSupply({ shareToken: shareToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "50000"

augur.api.ShareToken.getTypeName({ shareToken: shareToken }, function (typeName) { /* ... */ })
// example output:
typeName = "ShareToken";
```
#### [Share Token Contract Code](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/trading/ShareToken.sol)

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
  _endTime: _endTime,
  _hasDesignatedReporter: 0
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

augur.api.Universe.getTypeName({ universe: universe }, function (typeName) { /* ... */ })
// example output:
typeName = "Universe";

var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.Universe.isContainerForMarket({
  universe: universe,
  _shadyTarget: market
}, function (isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1";

var registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
augur.api.Universe.isContainerForRegistrationToken({
  universe: universe,
  _shadyTarget: registrationToken
}, function (isContainerForRegistrationToken) { /* ... */ })
// example output:
isContainerForRegistrationToken = "1";

var reportingToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
augur.api.Universe.isContainerForReportingToken({
  universe: universe,
  _shadyTarget: reportingToken
}, function (isContainerForReportingToken) { /* ... */ })
// example output:
isContainerForReportingToken = "1";

var reportingWindow = "578";
augur.api.Universe.isContainerForReportingWindow({
  universe: universe,
  _shadyTarget: reportingWindow
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
#### [Universe Contract Code](https://github.com/AugurProject/augur-core/blob/develop/source/contracts/reporting/Universe.sol)

The Universe Contract is the contract that defines an Augur [Universe](#universe) and the methods used to interact with them. All of Augur's [Markets](#market), [Order Books](#order-book), [Reporting Windows](#reporting-window), and [REP](#rep) belong to a specific Universe. In the rare event that an [All Reporting](#all-reporting) Market's [Proposed Outcome](#proposed-outcome) is [Challenged](#challenge) during the [Dispute Phase](#dispute-phase) of a Reporting Window then a [Fork](#fork) will occur and new Universes will be created. The Universe that originally contained the [Forked Market](#forked-market) will become a [Locked Universe](#locked-universe), thereby not allowing any Market creation to take place in the Locked Universe. The newly created Universes are known as [Child Universes](#child-universe), whereas the original and now Locked Universe is considered those Child Universes' [Parent Universe](#parent-universe).

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

#### augur.api.Universe.getReportingWindowByMarketEndTime({ universe, \_endTime, \_hasDesignatedReporter }[, callback])

Returns the [Reporting Window](#reporting-window) address on the specific `universe` given an `_endTime` and if the [Market](#market) we are checking for has an [Designated Reporter](#designated-reporter) or not (`_hasDesignatedReporter`). `_hasDesignatedReporter` should be `0` for Markets without an Designated Reporter, `1` for Markets with an Designated Reporter.

#### augur.api.Universe.getReportingWindowByTimestamp({ universe, \_timestamp }[, callback])

Returns the [Reporting Window](#reporting-window) Contract Address for a specific `universe` and provided `_timestamp`.

#### augur.api.Universe.getReportingWindowId({ universe, \_timestamp }[, callback])

Returns the [Reporting Window](#reporting-window) Id for a specific `universe` and provided `_timestamp`. This is calculated by dividing the timestamp by the [Universes'](#universe) Reporting Window duration in seconds.

#### augur.api.Universe.getReputationToken({ universe }[, callback])

Returns the [Reputation Token](#rep) Address for a specific `universe`. This is the REP usable within this Universe.

#### augur.api.Universe.getTypeName({ universe }[, callback])

Returns the specified `universe`'s type name, which should always return "Universe".

#### augur.api.Universe.isContainerForMarket({ universe, \_shadyTarget }[, callback])

Returns wether the specific `universe` is a container for the [Market](#market) `_shadyTarget` address provided. Returns `1` if true, `0` if false. All Markets are created within a [Universe](#universe), and this function is used to help confirm if a Market is actually attached to the Universe in question.

#### augur.api.Universe.isContainerForRegistrationToken({ universe, \_shadyTarget }[, callback])

Returns wether the specific `universe` is a container for the [Registration Token](#registration-token) `_shadyTarget` address provided. Returns `1` if true, `0` if false. All [Reporting Windows](#reporting-window) are attached to a Universe, and all Registration Tokens belong to a specific Reporting Window. This method is used to confirm that the Registration Token belongs to a Reporting Window belonging to this Universe.

#### augur.api.Universe.isContainerForReportingToken({ universe, \_shadyTarget }[, callback])

Returns wether the specific `universe` is a container for the [Reporting](#reporting) Token `_shadyTarget` address provided. Returns `1` if true, `0` if false. [Reporting Tokens](#reporting-tokens) are 1:1 exchangeable for [REP](#rep) by [Reporters](#reporter) to represent their staked REP for a [Report](#report). This method is designed determine wether the Universe contains the Reporting Token specified.

#### augur.api.Universe.isContainerForReportingWindow({ universe, \_shadyTarget }[, callback])

Returns wether the specific `universe` is a container for the [Reporting Window](#reporting-window) `_shadyTarget` Contract Address provided. Returns `1` if true, `0` if false. All Reporting Windows belong to a Universe and this method is used to see if a specific Reporting Window Address belongs to the Universe in question.

#### augur.api.Universe.isParentOf({ universe, \_shadyChild }[, callback])

Returns wether the specific `universe` is a container for the `_shadyChild` [Child Universe](#child-universe) Address provided. Returns `1` if true, `0` if false. If you want to see if a specific [Universe](#universe) is the [Parent Universe](#parent-universe) to a Child Universe you would use this function to determine that.
