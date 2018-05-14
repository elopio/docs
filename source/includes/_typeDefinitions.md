API Type Definitions
================

augur.js' functions accept and return a variety of different objects, which are described below.

<!-- TODO: Add glossary links to type definitions -->
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
* **`marketId`** (string|null) Contract address of the Market in which the tranfer took place, as a hexadecimal string (if any).

<a name="AugurEventLog"></a>
### AugurEventLog  (Object)

Note: Other properties will be present in this object, depending on what event type it is. For a list of which values are logged for which events, refer to the [Event Types](#event-types) section.

#### **Properties:** 
* **`address`**  (string) The 20-byte Ethereum contract address of the contract that emitted this event log.
* **`removed`**  (boolean) Whether the transaction this event was created from was removed from the Ethereum blockchain (due to an orphaned block) or never gotten to (due to a rejected transaction).
* **`transactionHash`**  (string) Hash of the transactions this log was created from, as a 32-byte hexadecimal value.
* **`transactionIndex`**  (number) Integer of the transaction's index position in the block.
* **`logIndex`**  (number) Integer of the log index position in the block.
* **`blockNumber`**  (number) Number of the block on the Ethereum blockchain where the event was logged.
* **`blockHash`** (string) Hash of the block on the Ethereum blockchain where the event was logged, as a 32-byte hexadecimal value.
* **`contractName`**  (string) Name of the Solidity contract in which the event is defined.
* **`eventName`**  (string) Name of the event type being logged.

<a name="BetterWorseOrders"></a>
### BetterWorseOrders  (Object)

#### **Properties:** 
* **`betterOrderId`** (string|null) ID of the order with the next best price over the specified order ID, as a hexadecimal string.
* **`worseOrderId`** (string|null) ID of the order with the next worse price over the specified order ID, as a hexadecimal string.

<a name="ClaimReportingFeesInfo"></a>
### ClaimReportingFeesInfo  (Object)

#### **Properties:** 
* **`redeemedFeeWindows`** (Array.&lt;string>|null) Ethereum contract addresses of all successfully redeemed Fee Windows, as hexadecimal strings. Not set if `p.estimateGas` is true.
* **`redeemedDisputeCrowdsourcers`** (Array.&lt;string>|null) Ethereum contract addresses of all successfully redeemed Dispute Crowdsourcers, as hexadecimal strings.  Not set if `p.estimateGas` is true.
* **`redeemedInitialReporters`** (Array.&lt;string>|null) Ethereum contract addresses of all successfully redeemed Initial Reporters, as hexadecimal strings.  Not set if `p.estimateGas` is true.
* **`gasEstimates`** (<a href="#GasEstimateInfo">GasEstimateInfo</a>|null) Object containing a breakdown of gas estimates for all reporting fee redemption transactions. Not set if `p.estimateGas` is false.

<a name="ContractAddresses"></a>
### ContractAddresses  (Object)

#### **Properties:** 
* **`version`** (string) Version of the contracts.
* **`net_version`** (string) Network ID that Augur Node is connected to. 
* **`addresses`** (Object) Object containing the 20-byte Ethereum contract addresses used by Augur, keyed by contract name.

<a name="Category"></a>
### Category  (Object)

#### **Properties:** 
* **`category`** (string) Name of a Category.
* **`popularity`** (string) Category popularity. (The exact method for calculating this value is still pending.)

<a name="ConnectOptions"></a>
### ConnectOptions  (Object)

#### **Properties:** 
* **`ethereumNode`** (<a href="#EthereumNode">EthereumNode</a>) Object containing information on how to connect to a desired Ethereum node, either locally or remotely (hosted).
* **`augurNode`** (string) &lt;optional> Websocket address of an [Augur Node](#augur-node).

<a name="DISPUTE_TOKEN_STATE"></a>
### DISPUTE_TOKEN_STATE  (Object)

Serves as an enum for the state of a Dispute Token.

#### **Properties:** 
* **`ALL`** (string) Dispute Token can be in any state. (If no Dispute Token state is specified, this is the default value.)
* **`UNCLAIMED`** (string) Dispute Token is in a Finalized Market, was staked on the correct Outcome, and has not been claimed yet.
* **`UNFINALIZED`** (string) Dispute Token is in a Market that has not been Finalized.

<a name="DisputeToken"></a>
### DisputeToken  (Object)

#### **Properties:** 
* **`disputeToken`** (string) Contract address of the Dispute Token, as a hexadecimal string.
* **`marketId`** (string) Ethereum address of the Market, as a hexadecimal string.
* **`payout0`** (number|null) Payout numerator 0 of the Dispute Token's payout set.
* **`payout1`** (number|null) Payout numerator 1 of the Dispute Token's payout set.
* **`payout2`** (number|null) Payout numerator 2 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`payout3`** (number|null) Payout numerator 3 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`payout4`** (number|null) Payout numerator 4 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`payout5`** (number|null) Payout numerator 5 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`payout6`** (number|null) Payout numerator 6 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`payout7`** (number|null) Payout numerator 7 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`isInvalid`** (boolean|number) Whether the Market was determined to be invalid.
* **`balance`** (number) Dispute Token balance the owner has staked, in ETH.
* **`winningToken`** (boolean|null) Index of the Payout Numerator that was determined to be the Market's Final Outcome.
* **`tentativeWinning`** (boolean) Index of the Payout Numerator that is tentatively the winning Outcome.
* **`claimed`** (boolean) Whether the Dispute Token has been claimed by the owner.
* **`reportingState`** (<a href="#REPORTING_STATE">REPORTING_STATE</a>) Reporting state of the Market.

<a name="EthereumNode"></a>
### EthereumNode  (Object)

#### **Properties:** 
* **`http`** (string|null) HTTP address of an Ethereum node.
* **`httpAddresses`** (Array.&lt;string>|null) Array of HTTP Ethereum node addresses. (Can be used instead of `http` to specify a list of HTTP addresses to iterate through until a connection is established.)
* **`ws`** (string|null) Websocket address of an Ethereum node.
* **`wsAddresses`** (Array.&lt;string>|null) Array of WebSocket Ethereum node addresses. (Can be used instead of `ws` to specify a list of WebSocket addresses to iterate through until a connection is established.)
* **`ipc`** (string|null) IPC address of an Ethereum node.
* **`ipcAddresses`** (Array.&lt;string>|null) Array of IPC Ethereum node addresses. (Can be used instead of `ipc` to specify a list of IPC addresses to iterate through until a connection is established.)
* **`networkId`** (string|null) Network ID of the current Ethereum node connection. For example, 1 for the Ethereum public main network, 3 for Ropsten (public cross-client Ethereum test network), 4 for Rinkeby (public Geth PoA test network), 42 for Kovan (public Parity PoA test network), etc.

<a name="ExtraInfo"></a>
### ExtraInfo  (Object)

#### **Properties:** 
* **`resolutionSource`** (string|null) Source that should be referenced when determining the Outcome of a Market.
* **`tags`** (Array.&lt;string>|null) Keywords used to tag the Market (maximum of 2).
* **`longDescription`** (string|null) Additional information not included in description of the Market.

<a name="FeeDetails"></a>
### FeeDetails  (Object)

#### **Properties:** 
* **`total`** (<a href="#FeeDetailsTotal">FeeDetailsTotal</a>) Object containing information about the Markets in which a specific user has unclaimed ETH/REP.
* **`feeWindows`** (Array.&lt;string>) Array of FeeWindow contract addresses with unclaimed REP, as hexadecimal strings.
* **`forkedMarket`** (<a href="#ForkedMarket">ForkedMarket</a>|null) ForkedMarket object containing information about the Forked Market (if one exists in the specified universe.)
* **`nonforkedMarkets`** (Array.&lt;<a href="#NonforkedMarket">NonforkedMarket</a>>) Array of NonforkedMarket objects containing unclaimed ETH/REP.

<a name="FeeDetailsTotal"></a>
### FeeDetailsTotal  (Object)

#### **Properties:** 
* **`unclaimedEth`** (string) Amount of unclaimed Reporting Fee attoETH a reporter has in a given Universe or Fee Window.
* **`unclaimedRepStaked`** (string) Amount of unclaimed attoREP a reporter has staked in a given Universe or Fee Window (either in First Public Reports or Crowdsourcers).
* **`unclaimedRepEarned`** (string) Amount of unclaimed attoREP a reporter has earned as Reporting Fees by staking REP in a given Universe or Fee Window (either in First Public Reports or Crowdsourcers).
* **`lostRep`** (string) Description pending.

<a name="FeeWindowCurrent"></a>
### FeeWindowCurrent  (Object)

#### **Properties:** 
* **`endTime`** (number) Unix timestamp for when the Fee Window begins.
* **`feeWindow`** (string) Ethereum contract address of the Fee Window.
* **`feeWindowId`** (number) Unique numerical ID of the Fee Window.
* **`startTime`** (number) Unix timestamp for when the Fee Window begins.
* **`universe`** (string) Ethereum contract address of the Universe to which the Fee Window belongs.
* **`totalStake`** (number) &lt;optional> If a `reporter` was specified, the total amount of attoREP they have Staked in the current Fee Window will be returned as `totalStake`. (This amount includes attoREP Staked on Initial Reports as well as on Dispute Crowdsourcers.)

<a name="ForkedMarket"></a>
### ForkedMarket  (Object)

* **`marketId`** (string) Ethereum contract address of the Forked Market, as a hexadecimal string.
* **`universe`** (string) Ethereum contract address of Universe to which the Forked Market belongs, as a hexadecimal string.
* **`isFinalized`** (boolean) Whether the Forked Market has been Finalized (i.e., the function Market.finalize` has been called on it successfully).
* **`crowdsourcers`** (Array.&lt;<a href="#CrowdsourcerState">CrowdsourcerState</a>>) Array of objects containing information about the Forked Market's DisputeCrowdsourcers.
* **`initialReporter`** (<a href="#InitialReporterState">InitialReporterState</a>|null) Object containing information about the Forked Market's InitialReporter.

<a name="CrowdsourcerState"></a>
### CrowdsourcerState  (Object)

* **`crowdsourcerId`** (string) Ethereum contract address of a DisputeCrowdsourcer belonging to a Forked Market, as a hexadecimal string.
* **`isForked`** (boolean) Whether the DisputeCrowdsourcer has been forked (i.e., has had its `DisputeCrowdsourcer.fork` function called successfully).

<a name="InitialReporterState"></a>
### InitialReporterState  (Object)

* **`initialReporterId`** (string) Ethereum contract address of the InitialReporter belonging to a Forked Market, as a hexadecimal string.
* **`isForked`** (boolean) Whether the InitialReporter has been forked (i.e., has had its `InitialReporter.fork` function called successfully).

<a name="GasEstimateInfo"></a>
### GasEstimateInfo  (Object)

#### **Properties:** 
* **`disputeCrowdsourcers`** (Array.&lt;Object>) Array of objects containing contract address/gas estimate pairs for all DisputeCrowdsourcers.
* **`initialReporters`** (Array.&lt;Object>) Array of objects containing contract address/gas estimate pairs for all InitialReporters.
* **`feeWindows`** (Array.&lt;Object>) Array of objects containing contract address/gas estimate pairs for all FeeWindows.
* **`totals`** (<a href="#GasEstimateTotals">GasEstimateTotals</a>) Object containing total gas estimates for each type of contract.

<a name="GasEstimateTotals"></a>
### GasEstimateTotals  (Object)

#### **Properties:** 
* **`disputeCrowdsourcers`** (BigNumber) Total gas estimate for redeeming all DisputeCrowdsourcer reporting fees.
* **`initialReporters`** (BigNumber) Total gas estimate for redeeming all InitialReporter reporting fees.
* **`feeWindows`** (BigNumber) Total gas estimate for redeeming all FeeWindow reporting fees.
* **`all`** (BigNumber) Sum of all gas estimates.

<a name="InitialReporter"></a>
### InitialReporter  (Object)

#### **Properties:** 
* **`marketId`** (string) Ethereum contract address of the Market for which the Initial Report was submitted.
* **`reporter`** (string) Ethereum address of the Reporter who submitted the Initial Report.
* **`amountStaked`** (number) Amount of attoREP the Reporter Staked in the Initial Report.
* **`initialReporter`** (string) Ethereum address of the InitialReporter contract to which the Initial Report was submitted.
* **`redeemed`** (boolean) Whether the Reporter has redeemed their REP from the InitialReporter contract.
* **`isDesignatedReporter`** (boolean) Whether `reporter` was the Designated Reporter (as opposed to the First Public Reporter).
* **`repBalance`** (number) Amount of REP that the InitialReporter contract has as its balance.

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
* **`minPrice`** (string|number) Minimum price allowed for a share on a Market, in ETH. For binary & categorical Markets, this is 0 ETH. For Scalar Markets, this is the bottom end of the range set by the Market creator.
* **`maxPrice`** (string|number) Maximum price allowed for a share on a Market, in ETH. For binary & categorical Markets, this is 1 ETH. For Scalar Markets, this is the top end of the range set by the Market creator.
* **`cumulativeScale`** (string|number) Difference between maxPrice and minPrice.
* **`author`** (string) Ethereum address of the creator of the Market, as a hexadecimal string.
* **`creationTime`** (number) Timestamp when the Ethereum block containing the Market creation was created, in seconds.
* **`creationBlock`** (number) Number of the Ethereum block containing the Market creation.
* **`creationFee`** (string|number) Fee paid by the Market Creator to create the Market, in ETH.
* **`settlementFee`** (string) Fee extracted when a Complete Set is Settled. It is the combination of the Creator Fee and the Reporting Fee.
* **`reportingFeeRate`** (string|number) Percentage rate of ETH sent to the Fee Window containing the Market whenever shares are settled. Reporting Fees are later used to pay REP holders for Reporting on the Outcome of Markets.
* **`marketCreatorFeeRate`** (string|number) Percentage rate of ETH paid to the Market creator whenever shares are settled.
* **`marketCreatorFeesBalance`** (BigNumber|null) Amount of claimable fees the Market creator has not collected from the Market, in ETH.
* **`marketCreatorMailbox`** (string) Ethereum address of the Market Creator, as a hexadecimal string.
* **`marketCreatorMailboxOwner`** (string) Ethereum address of the Market Creator Mailbox, as a hexadecimal string.
* **`initialReportSize`** (string|number|null) Size of the Designated Report No-Show REP Bond (if the Initial Report was submitted by a First Public Reporter instead of the Designated Reporter).
* **`category`** (string) Name of the category the Market is in.
* **`tags`** (Array.&lt;(string|null)>) Names with which the Market has been tagged.
* **`volume`** (string|number) Trading volume for this Outcome. (Method for calculating this is pending.)
* **`outstandingShares`** (string|number) Total shares in the Market. (Method for calculating this is pending.)
* **`feeWindow`** (string) Contract address of the Fee Window the Market is in, as a hexadecimal string.
* **`endTime`** (number) Timestamp when the Market event ends, in seconds.
* **`finalizationBlockNumber`** (number|null) Ethereum block number in which the Market was Finalized.
* **`finalizationTime`** (number|null) Timestamp when the Market was finalized (if any), in seconds.
* **`reportingState`** (<a href="#REPORTING_STATE">REPORTING_STATE</a>|null) Reporting state name.
* **`forking`** (boolean|number) Whether the Market has Forked.
* **`needsMigration`** (boolean|number) Whether the Market needs to be migrated to its Universe's Child Universe (i.e., the Market is not Finalized, and the Forked Market in its Universe is Finalized).
* **`description`** (string) Description of the Market.
* **`details`** (string|null) Stringified JSON object containing resolutionSource, tags, longDescription, and outcomeNames (for Categorical Markets).
* **`scalarDenomination`** (string|null) Denomination used for the numerical range of a Scalar Market (e.g., dollars, degrees Fahrenheit, parts-per-billion).
* **`designatedReporter`** (string) Ethereum address of the Market's designated report, as a hexadecimal string.
* **`designatedReportStake`** (string|number) Size of the Designated Reporter Stake, in attoETH, that the Designated Reporter must pay to submit the Designated Report for this Market.
* **`resolutionSource`** (string|null) Reference source used to determine the Outcome of the Market event.
* **`numTicks`** (string|number) Number of possible prices, or ticks, between a Market's minimum price and maximum price.
* **`tickSize`** (string|number) Smallest recognized amount by which a price of a security or future may fluctuate in the Market.
* **`consensus`** (<a href="#NormalizedPayout">NormalizedPayout</a>|null) Consensus Outcome for the Market.
* **`outcomes`** (Array.&lt;<a href="#OutcomeInfo">OutcomeInfo</a>>) Array of OutcomeInfo objects.

<a name="MarketPriceTimeSeries"></a>
### MarketPriceTimeSeries  (Object)

#### **Properties:** 
* **`Price`** (<a href="#SingleOutcomePriceTimeSeries">SingleOutcomePriceTimeSeries</a>) time-series for a single Outcome, keyed by Outcome ID.

<a name="MarketsContractAddressRow"></a>
### MarketsContractAddressRow  (Object)

#### **Properties:** 
* **`marketId`** (string) Address of a Market, as a hexadecimal string.

<a name="Meta"></a>
### Meta  (Object)

Authentication metadata for raw transactions.

#### **Properties:** 
* **`signer`** (buffer|function) Private key buffer or a signing function provided by a hardware wallet, of the account initiating the transaction.
* **`accountType`** (string) Type of account that is signing the transaction. Possible values include "privateKey", "ledger", and "uPort".

<a name="NoKeystoreAccount"></a>
### NoKeystoreAccount  (Object)

#### **Properties:** 
* **`address`** (string) This account's Ethereum address, as a hexadecimal string.
* **`privateKey`** (buffer) The private key for this account.
* **`derivedKey`** (buffer) The secret key (derived from the password) used to encrypt this account's private key.

<a name="NonforkedMarket"></a>
### NonforkedMarket  (Object)

* **`marketId`** (string) Ethereum contract address of the non-Forked Market, as a hexadecimal string.
* **`universe`** (string) Ethereum contract address of Universe to which the non-Forked Market belongs, as a hexadecimal string.
* **`crowdsourcersAreDisavowed`** (boolean) Whether the non-Forked Market's DisputeCrowdsourcers have been disavowed (i.e., its `Market.disavowCrowdsourcers` function has been called successfully).
* **`isMigrated`** (boolean) Whether the non-Forked Market has been migrated to the Child Universe of its original Universe (i.e., its `Market.isMigrated` function has been called successfully).
* **`isFinalized`** (boolean) Whether the non-Forked Market has been Finalized (i.e., its `Market.finalize` function has been called successfully).
* **`crowdsourcers`** (Array.&lt;string>) Array of Ethereum contract addresses of the non-Forked Market's DisputeCrowdsourcers, as hexadecimal strings.
* **`initialReporter`** (string|null) Ethereum contract address of the non-Forked Market's InitialReporter, as a hexadecimal string.

<a name="NormalizedPayout"></a>
### NormalizedPayout  (Object)

#### **Properties:** 
* **`isInvalid`** (boolean|number) Whether the Outcome is Invalid.
* **`payout`** (Array.&lt;number|string>) Payout Set for the Dispute Crowdsourcer.

<a name="Order"></a>
### Order  (Object)

#### **Properties:** 
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

<a name="ProfitLoss"></a>
### ProfitLoss  (Object)

#### **Properties:**
  * **`position`**  (string) Net trading position, where the quantity is the number of attoshares. An overall "sell" position is negative, and an overall "buy" position is positive.
  * **`meanOpenPrice`**  (string) Mean price of trades at time of buy-in, in attoETH.
  * **`realized`**  (string) Amount of realized profits or losses from all trades, in attoETH.
  * **`queued`**  (string) Expected profit or loss of the current set of pending trades, in attoETH.
  * **`unrealized`**  (string) Unrealized profit/loss in attoETH, calculated as attoshares held * (last trade price - price on buy-in).

<a name="Report"></a>
### Report  (Object)

#### **Properties:** 
* **`transactionHash`** (string) Hash to look up the reporting transaction receipt.
* **`logIndex`** (number) Number of the log index position in the Ethereum block containing the reporting transaction.
* **`creationBlockNumber`** (number) Number of the Ethereum block containing the reporting transaction.
* **`blockHash`** (string) Hash of the Ethereum block containing the reporting transaction.
* **`creationTime`** (number) Timestamp, in seconds, when the Ethereum block containing the reporting transaction was created.
* **`marketId`** (string) Contract address of the Market, as a hexadecimal string.
* **`feeWindow`** (string) Fee Window the Market is in currently.
* **`payoutNumerators`** (Array.&lt;number>) Array representing the payout set.
* **`amountStaked`** (number) attoREP the Reporter has Staked on the Outcome of their Report.
* **`crowdsourcerId`** (string) Ethereum contract address of the Dispute Crowdsourcer, as a hexadecimal string.
* **`isCategorical`** (boolean) Whether the Market is a Categorical Market.
* **`isScalar`** (boolean) Whether the Market is a Scalar Market.
* **`isInvalid`** (boolean) Whether the Market is [Invalid](#invalid-outcome).
* **`isSubmitted`** (boolean) Whether the Report has been submitted. (This property is vestigial and is always set to true.)

<a name="REPORTING_STATE"></a>
### REPORTING_STATE  (Object)

Serves as an enum for the state of a Market.

#### **Properties:** 
* **`PRE_REPORTING`** (string) Market's end time has not yet come to pass.
* **`DESIGNATED_REPORTING`** (string) Market's end time has occurred, and it is pending a Designated Report.
* **`OPEN_REPORTING`** (string) The Designated Reporter failed to submit a Designated Report within the allotted time, causing the Market to enter the Open Reporting Phase.
* **`CROWDSOURCING_DISPUTE`** (string) An Initial Report for the Market has been submitted, and the Market's Tentative Outcome is open to being Disputed.
* **`AWAITING_NEXT_WINDOW`** (string) Either the Market had an Initial Report submitted in the current Fee Window, or one of the Market's Dispute Crowdsourcers received enough REP to Challenge the Market's Tentative Outcome. In either case, the Market is awaiting the next Fee Window in order to enter another Dispute Round.
* **`FINALIZED`** (string) An Outcome for the Market has been determined.
* **`FORKING`** (string) The Dispute Crowdsourcer for one of the Market's Outcomes received enough REP to reach the Fork Threshold, causing a fork. Users can migrate their REP to the Universe of their choice.
* **`AWAITING_NO_REPORT_MIGRATION`** (string) Either the Designated Report was Disputed, or the Designated Reporter failed to submit a Report, and the Market is waiting for the next reporting phase to begin.
* **`AWAITING_FORK_MIGRATION`** (string) Market is waiting for another Market's Fork to be resolved. This means its Tentative Outcome has been reset to the Outcome submitted in the Initial Report, and all Stake in the Market's Dispute Crowdsourcers has been refunded to the users who Staked on them.
* **`AWAITING_NEXT_WINDOW`** (string) The Market has been Finalized, but the [Post-Finalization Waiting Period](#post-finalization-waiting-period) has not elapsed.

<a name="SimulatedTrade"></a>
### SimulatedTrade  (Object)

#### **Properties:** 
* **`sharesFilled`** (string) Number of Shares Filled by the trade.
* **`settlementFees`** (string) Projected Settlement Fees paid on this trade, as a base-10 string.
* **`gasFees`** (string) Projected gas fees paid on this trade, as a base-10 string.
* **`sharesDepleted`** (string)  Projected number of Shares of the traded Outcome spent on this trade, as a base-10 string.
* **`otherSharesDepleted`** (string) Projected number of Shares of the other (non-traded) Outcomes spent on this trade, as a base-10 string.
* **`tokensDepleted`** (string) Projected number of tokens spent on this trade, as a base-10 string.
* **`shareBalances`** (Array.&lt;string>}  Projected final balances after the trade is complete, as an array of base-10 strings.

<a name="SingleOutcomeOrderBook"></a>
### SingleOutcomeOrderBook  (Object)

#### **Properties:** 
 * **`buy`** (<a href="#SingleOutcomeOrderBookSide">SingleOutcomeOrderBookSide</a>) Buy orders (bids) indexed by order ID.
 * **`sell`** (<a href="#SingleOutcomeOrderBookSide">SingleOutcomeOrderBookSide</a>)  Sell orders (asks) indexed by order ID.

<a name="SingleOutcomeOrderBookSide"></a>
### SingleOutcomeOrderBookSide  (Object)

#### **Properties:** 
* (<a href="#Order">Order</a>) Buy (bid) or sell (ask) orders, indexed by order ID.

<a name="SingleOutcomePriceTimeSeries"></a>
### SingleOutcomePriceTimeSeries  (Object)

#### **Properties:** 
* **`Array`** (Array.&lt;<a href="#TimestampedPrice">TimestampedPrice</a>>) of timestamped price points for this Outcome.

<a name="StakeDetails"></a>
### StakeDetails  (Object)

#### **Properties:** 
* **`payout`** (Array.&lt;number|string>) Payout Set for the Dispute Crowdsourcer.
* **`isInvalid`** (boolean|number) Whether the Outcome is Invalid.
* **`bondSizeCurrent`** (string|null) Amount of attoREP needed to successfully Dispute the Tentative Outcome of this Market. Is null if `tentativeWinning` is true.
* **`bondSizeTotal`** (string|null) Description pending. Is null if `tentativeWinning` is true.
* **`stakeCurrent`** (string|null) Amount of attoREP Staked on this Outcome for the current Dispute Round. Is null if `tentativeWinning` is true.
* **`stakeRemaining`** (string|null) Amount of attoREP that this Outcome must receive in order to become the Tentative Outcome for the Market. Is null if `tentativeWinning` is true.
* **`accountStakeCompleted`** (string|null) Amount of attoREP the specified `account` has Staked in this Outcome during the current Dispute Round. Is null if `tentativeWinning` is true.
* **`accountStakeCurrent`** (string|null) Amount of attoREP the specified `account` has Staked in this Outcome during the current Dispute Round. Is null if `tentativeWinning` is true.
* **`accountStakeTotal`** (string|null) Amount of attoREP the specified `account` has Staked on this Outcome. Is null if `tentativeWinning` is true.
* **`stakeCompleted`** (string) Amount of attoREP Staked in this Outcome to cause it to become the Tentative Outcome (either after the First Public Report or after a Dispute Round ended).
* **`tentativeWinning`** (boolean) Whether this Outcome is the Tenative Outcome for the Market.

<a name="StakeInfo"></a>
### StakeInfo  (Object)

#### **Properties:** 
* **`marketId`** (string) Ethereum contract address of the Market.
* **`disputeRound`** (number|null) Dispute round the Market is currently in. (This will be 0 until someone contributes to a Crowdsourcer.) 
* **`stakeCompletedTotal`** (string) Total attoREP that has been Staked in Crowdsourcers and in the First Public Report.
* **`bondSizeOfNewStake`** (string) Amount of attoREP needed to Dispute the Tentative Outcome of the Market.
* **`stakes`** (Array.&lt;<a href="#StakeDetails">StakeDetails</a>>) 

<a name="TimestampedPrice"></a>
### TimestampedPrice  (Object)

#### **Properties:** 
* **`price`** (number) Display (non-normalized) price, as a base-10 number.
* **`amount`** (number) Display price, as a base-10 number.
* **`timestamp`** (number) Unix timestamp for this price in seconds, as an integer.

<a name="TradeCost"></a>
### TradeCost  (Object)

#### **Properties:** 
* **`cost`** (BigNumber) Wei (attoEther) value needed for this trade.
* **`onChainAmount`** (BigNumber) On-chain number of Shares for this trade.
* **`onChainPrice`** (BigNumber) On-chain price for this trade.

<a name="UnclaimedFeeWindowInfo"></a>
### UnclaimedFeeWindowInfo  (Object)

#### **Properties:** 
* **`startTime`** (number) Unix timestamp when the Fee Window begins.
* **`endTime`** (number) Unix timestamp when the Fee Window ends.
* **`balance`** (number) Balance the user has Staked in the Fee Window, in attoREP.
* **`expectedFees`** (number) Expected Reporting Fees, in attoREP, that will be withdrawn when the user redeems their Stake in the Fee Window.

<a name="UserTrade"></a>
### UserTrade  (Object)

#### **Properties:** 
* **`transactionHash`** (string) Hash to look up the trade transaction receipt.
* **`logIndex`** (number) Number of the log index position in the Ethereum block containing the trade transaction.
* **`type`** (string) Type of trade. Valid values are "buy" and "sell".
* **`price`** (number) Price paid for trade, in attoETH.
* **`amount`** (number) Amount of attoShares that were bought/sold.
* **`maker`** (boolean) Whether the specified user is the order maker (as opposed to filler).
* **`marketCreatorFees`** (number) Amount of fees paid to Market creator, in ETH.
* **`reporterFees`** (number) Amount of fees paid to reporters, in attoETH.
* **`marketId`** (string) Contract address of the Market, as a hexadecimal string.
* **`outcome`** (number) Outcome being bought/sold.
* **`shareToken`** (string) Contract address of the share token that was bought or sold, as a hexadecimal string.
* **`timestamp`** (number) Unix timestamp when the trade was placed.
* **`tradeGroupId`** (number|null) ID logged with each trade transaction (can be used to group trades client-side), as a hexadecimal string.

<a name="UserTradePosition"></a>
### UserTradePosition  (Object)

#### **Properties:** 
* **`marketId`** (string) Contract address of the Market, as a hexadecimal string.
* **`outcome`** (number) Outcome of the shares the user owns.
* **`numShares`** (number) Quantity of shares currently owned by the user.
* **`numSharesAdjusted`** (number) Similar to numShares, but Short Positions are shown as negative for Binary and Scalar Markets.
* **`realizedProfitLoss`** (number) Description pending.
* **`unrealizedProfitLoss`** (number) Description pending.
* **`averagePrice`** (number) Description pending.

<a name="WebSocket"></a>
### WebSocket  (Object)

#### **Properties:** 
* **`binaryType`** (string) String indicating the type of binary data being transmitted by the connection. This should be either "blob" if DOM Blob objects are being used or "arraybuffer" if ArrayBuffer objects are being used.
* **`bufferedAmount`** (number) Number of bytes of data that have been queued using calls to send() but not yet transmitted to the network. This value resets to zero once all queued data has been sent. This value does not reset to zero when the connection is closed; if send() continues to be called, this will continue to climb.
* **`extensions`** (string) Extensions selected by the server. This is currently only the empty string or a list of extensions as negotiated by the connection.
* **`onclose`** (function) Event listener to be called when the WebSocket connection's readyState changes to CLOSED. The listener receives a CloseEvent named "close".
* **`onerror`** (function) Event listener to be called when an error occurs. This is a simple event named "error".
* **`onmessage`** (function) Event listener to be called when a message is received from the server. The listener receives a MessageEvent named "message".
* **`onopen`** (function) Event listener to be called when the WebSocket connection's readyState changes to OPEN; this indicates that the connection is ready to send and receive data. The event is a simple one with the name "open".
* **`protocol`** (string) String indicating the name of the sub-protocol the server selected; this will be one of the strings specified in the protocols parameter when creating the WebSocket object.
* **`readyState`** (number) Current state of the connection (0 for CONNECTING, 1 for OPEN, 2 for CLOSING, 3 for CLOSED).
* **`url`** (string) URL as resolved by the constructor. This is always an absolute URL.

<a name="WsTransport"></a>
### WsTransport  (Object)

#### **Properties:** 
* **`address`** (string) Address to which the WebSocket should connect.
* **`timeout`** (number) Number of milliseconds to wait before timing out.
* **`messageHandler`** (function) Function used to dispatch RPC responses.
* **`workQueue`** (Array) Queue of RPC payloads to send.
* **`awaitingPump`** (boolean) Whether `workQueue` is waiting to be pumped.
* **`connected`** (boolean) Whether the WebSocket has a connection to `address`.
* **`backoffMilliseconds`** (number) Milliseconds to wait before attempting to reconnect (uses exponential backoff).
* **`nextListenerToken`** (number) Counter used to uniquely identify reconnect listeners and disconnect listeners.
* **`reconnectListeners`** (Object) Event listeners for reconnecting.
* **`disconnectListeners`** (Object) Event listeners for disconnecting.
* **`webSocketClient`** (<a href="#WebSocket">WebSocket</a>) WebSocket client object.
