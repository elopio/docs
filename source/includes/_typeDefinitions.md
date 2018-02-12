API Type Definitions
================

augur.js' functions accept and return a variety of different objects, which are described below.

<!-- TODO: Add glossary links to type definitions -->
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
* **`contractName`**  (string) Name of the Solidity contract in which the event is defined.
* **`eventName`**  (string) Name of the event type being logged.

<a name="BetterWorseOrders"></a>
### BetterWorseOrders  (Object)

#### **Properties:** 
* **`betterOrderID`** (string|null) ID of the order with the next best price over the specified order ID, as a hexadecimal string.
* **`worseOrderID`** (string|null) ID of the order with the next worse price over the specified order ID, as a hexadecimal string.

<a name="ContractAddresses"></a>
### ContractAddresses  (Object)

#### **Properties:** 
* **`version`** (string) Version of the contracts.
* **`addresses`** (Object) Object containing the 20-byte Ethereum contract addresses used by Augur, keyed by contract name.

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
* **`payout0`** (number|null) Payout numerator 0 of the Dispute Token's payout set.
* **`payout1`** (number|null) Payout numerator 1 of the Dispute Token's payout set.
* **`payout2`** (number|null) Payout numerator 2 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`payout3`** (number|null) Payout numerator 3 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`payout4`** (number|null) Payout numerator 4 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`payout5`** (number|null) Payout numerator 5 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`payout6`** (number|null) Payout numerator 6 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`payout7`** (number|null) Payout numerator 7 of the Dispute Token's payout set. (Set to null for Binary and Scalar Markets.)
* **`isInvalid`** (boolean|number) Whether the Market was determined to be invalid.
* **`disputeToken`** (string) Contract address of the Dispute Token, as a hexadecimal string.
* **`marketID`** (string) Ethereum address of the Market, as a hexadecimal string.
* **`amountStaked`** (number) Amount the Dispute Token owner has staked, in ETH.
* **`claimed`** (boolean) Whether the Dispute Token has been claimed by the owner.
* **`winningToken`** (boolean|null) Description pending.
* **`ReportingState`** (<a href="#REPORTING_STATE">REPORTING_STATE</a>) Reporting state of the Market.

<a name="EthereumNode"></a>
### EthereumNode  (Object)

#### **Properties:** 
* **`http`** (string|null) HTTP address of an Ethereum node.
* **`httpAddresses`** (Array.&lt;string>|null) Array of HTTP Ethereum node addresses. (Can be used instead of `http` to specify a list of HTTP addresses to iterate through until a connection is established.)
* **`ws`** (string|null) Websocket address of an Ethereum node.
* **`wsAddresses`** (Array.&lt;string>|null) Array of websocket Ethereum node addresses. (Can be used instead of `ws` to specify a list of websocket addresses to iterate through until a connection is established.)
* **`ipc`** (string|null) IPC address of an Ethereum node.
* **`ipcAddresses`** (Array.&lt;string>|null) Array of IPC Ethereum node addresses. (Can be used instead of `ipc` to specify a list of IPC addresses to iterate through until a connection is established.)
* **`networkID`** (string|null) Description pending.

<a name="ExtraInfo"></a>
### ExtraInfo  (Object)

#### **Properties:** 
* **`resolutionSource`** (string|null) Source that should be referenced when determining the Outcome of a Market.
* **`tags`** (Array.&lt;string>|null) Keywords used to tag the Market (maximum of 2).
* **`longDescription`** (string|null) Additional information not included in description of the Market.

<a name="FeeWindowCurrent"></a>
### FeeWindowCurrent  (Object)

#### **Properties:** 
* **`endBlockNumber`** (number|null) Block in which the Fee Window became inactive.
* **`endTime`** (number) Unix timestamp for when the Fee Window begins.
* **`feeWindow`** (string) Ethereum contract address of the Fee Window.
* **`feeWindowID`** (number) Unique numerical ID of the Fee Window.
* **`startBlockNumber`** (number) Block in which the Fee Window became active.
* **`startTime`** (number) Unix timestamp for when the Fee Window begins.
* **`universe`** (string) Ethereum contract address of the Universe to which the Fee Window belongs.
* **`totalStake`** (number) &lt;optional> If a `reporter` was specified, the total amount of attoREP they have Staked in the current Fee Window will be returned as `totalStake`. (This amount includes attoREP Staked on Initial Reports as well as on Dispute Crowdsourcers.)

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

<a name="MarketCreatorFee"></a>
### MarketCreatorFee  (Object)

#### **Properties:** 
* **`marketID`** (string) Address of a Market, as a hexadecimal string.
* **`unclaimedFee`** (number|string) Fee available to be claimed from the Market, by the Market Creator, in attoETH.

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
* **`settlementFee`** (string) Fee extracted when a Complete Set is Settled. It is the combination of the Creator Fee and the Reporting Fee.
* **`reportingFeeRate`** (number) Percentage rate of ETH sent to the Fee Window containing the Market whenever shares are settled. Reporting Fees are later used to pay REP holders for Reporting on the Outcome of Markets.
* **`marketCreatorFeeRate`** (number) Percentage rate of ETH paid to the Market creator whenever shares are settled.
* **`marketCreatorFeesCollected`** (number|null) Amount of fees the Market creator collected from the Market, in ETH.
* **`initialReportSize`** (number|null) Size of the Designated Report No-Show REP Bond (if the Initial Report was submitted by a First Public Reporter instead of the Designated Reporter).
* **`category`** (string) Name of the category the Market is in.
* **`tags`** (Array.&lt;(string|null)>) Names with which the Market has been tagged.
* **`volume`** (number) Trading volume for this Outcome. (Method for calculating this is pending.)
* **`outstandingShares`** (number) Total shares in the Market. (Method for calculating this is pending.)
* **`reportingState`** (<a href="#REPORTING_STATE">REPORTING_STATE</a>|null) Reporting state name.
* **`feeWindow`** (string) Contract address of the Fee Window the Market is in, as a hexadecimal string.
* **`endDate`** (number) Timestamp when the Market event ends, in seconds.
* **`finalizationTime`** (number|null) Timestamp when the Market was finalized (if any), in seconds.
* **`description`** (string) Description of the Market.
* **`extraInfo`** (string|null) Stringified JSON object containing resolutionSource, tags, longDescription, and outcomeNames (for categorical Markets).
* **`designatedReporter`** (string) Ethereum address of the Market's designated report, as a hexadecimal string.
* **`designatedReportStake`** (number) Size of the Designated Reporter Stake, in attoETH, that the Designated Reporter must pay to submit the Designated Report for this Market.
* **`resolutionSource`** (string|null) Reference source used to determine the Outcome of the Market event.
* **`numTicks`** (number) Number of possible prices, or ticks, between a Market's minimum price and maximum price.
* **`tickSize`** (number) Smallest recognized amount by which a price of a security or future may fluctuate in the Market.
* **`consensus`** (number|null) Consensus Outcome for the Market.
* **`outcomes`** (Array.&lt;<a href="#OutcomeInfo">OutcomeInfo</a>>) Array of OutcomeInfo objects.

<a name="MarketOrderBook"></a>
### MarketOrderBook  (Object)

#### **Properties:** 
* **`1`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 1 of this market.
* **`2`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 2 of this market.
* **`3`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 3 of this market.
* **`4`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 4 of this market.
* **`5`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 5 of this market.
* **`6`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 6 of this market.
* **`7`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 7 of this market.
* **`8`** (<a href="#SingleOutcomeOrderBook">SingleOutcomeOrderBook</a>|undefined) 1 Full order book (buy and sell) for outcome 8 of this market.

<a name="MarketPriceTimeSeries"></a>
### MarketPriceTimeSeries  (Object)

#### **Properties:** 
* **`Price`** (<a href="#SingleOutcomePriceTimeSeries">SingleOutcomePriceTimeSeries</a>) time-series for a single Outcome, keyed by Outcome ID.

<a name="MarketsContractAddressRow"></a>
### MarketsContractAddressRow  (Object)

#### **Properties:** 
* **`creationTime`** (number) Unix time when the Market was created.
* **`marketID`** (string) Address of a Market, as a hexadecimal string.

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

<a name="ProfitLoss"></a>
### ProfitLoss  (Object)

#### **Properties:**
  * **`position`**  (string) Description pending.
  * **`meanOpenPrice`**  (string) Description pending.
  * **`realized`**  (string) Description pending.
  * **`queued`**  (string) Description pending.
  * **`unrealized`**  (string) Description pending.

<a name="Report"></a>
### Report  (Object)

#### **Properties:** 
* **`transactionHash`** (string) Hash to look up the reporting transaction receipt.
* **`logIndex`** (number) Number of the log index position in the Ethereum block containing the reporting transaction.
* **`creationBlockNumber`** (number) Number of the Ethereum block containing the reporting transaction.
* **`blockHash`** (string) Hash of the Ethereum block containing the reporting transaction.
* **`creationTime`** (number) Timestamp, in seconds, when the Ethereum block containing the reporting transaction was created.
* **`marketID`** (string) Contract address of the Market, as a hexadecimal string.
* **`feeWindow`** (string) Fee Window the Market is in currently.
* **`payoutNumerators`** (Array.&lt;number>) Array representing the payout set.
* **`amountStaked`** (number) Description the reporter has staked on the Outcome of their report.
* **`crowdsourcerID`** (string) Ethereum contract address of the Dispute Crowdsourcer, as a hexadecimal string.
* **`isCategorical`** (boolean) Whether the Market is a Categorical Market.
* **`isScalar`** (boolean) Whether the Market is a Scalar Market.
* **`isInvalid`** (boolean) Whether the Market is [Invalid](#invalid-outcome).
* **`isSubmitted`** (boolean) Description pending.

<a name="REPORTING_STATE"></a>
### REPORTING_STATE  (Object)

Serves as an enum for the state of a stake token.

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

<a name="ScryptParams"></a>
### ScryptParams  (Object)

#### **Properties:** 
* **`dklen`** (number) Key length in bytes (usually 32).
* **`n`** (number) Number of scrypt iterations used to derive the secret key (usually 262144).
* **`p`** (number) Parallelization factor, determines relative CPU cost (usually 1).
* **`r`** (number) Block size factor used for scrypt's hash, determines relative memory cost (usually 8).
* **`salt`** (string) The dklen-byte salt used for this account, as a hexadecimal string.

<a name="SingleOutcomeOrderBook"></a>
### SingleOutcomeOrderBook  (Object)

#### **Properties:** 
 * **`buy`** (<a href="#SingleOutcomeOrderBookSide">SingleOutcomeOrderBookSide</a>) Buy orders (bids) indexed by order ID.
 * **`sell`** (<a href="#SingleOutcomeOrderBookSide">SingleOutcomeOrderBookSide</a>)  Sell orders (asks) indexed by order ID.

<a name="SingleOutcomeOrderBookSide"></a>
### SingleOutcomeOrderBookSide  (Object)

#### **Properties:** 
* **`Buy`**|**`Sell`** (<a href="#Order">Order</a>) bid or ask orders, indexed by order ID.

<a name="SingleOutcomePriceTimeSeries"></a>
### SingleOutcomePriceTimeSeries  (Object)

#### **Properties:** 
* **`Array`** (Array.&lt;<a href="#TimestampedPrice">TimestampedPrice</a>>) of timestamped price points for this Outcome.

<a name="StakeDetails"></a>
### StakeDetails  (Object)

#### **Properties:** 
* **`totalStaked`** (string) 
* **`size`** (string) 
* **`amountStaked`** (string) 

<a name="StakeInfo"></a>
### StakeInfo  (Object)

#### **Properties:** 
* **`marketID`** (number) 
* **`stake`** (Array.&lt;<a href="#StakeDetails">StakeDetails</a>>) 

<a name="TimestampedPrice"></a>
### TimestampedPrice  (Object)

#### **Properties:** 
* **`price`** (number) Display (non-normalized) price, as a base-10 number.
* **`amount`** (number) Display price, as a base-10 number.
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
* **`tradeGroupID`** (number|null) ID logged with each trade transaction by Augur's UI so that trades can be grouped client-side.

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

<a name="WebSocket"></a>
### WebSocket  (Object)

* **`binaryType`** (string) Description pending.
* **`bufferedAmount`** (number) Description pending.
* **`extensions`** (string) Description pending.
* **`onclose`** (function) Description pending.
* **`onerror`** (function) Description pending.
* **`onmessage`** (function) Description pending.
* **`onopen`** (function) Description pending.
* **`protocol`** (string) Description pending.
* **`readyState`** (number) Description pending.
* **`url`** (string) Description pending.

<a name="WsTransport"></a>
### WsTransport  (Object)

#### **Properties:** 
* **`address`** (string) WebSocket address to connect to.
* **`timeout`** (number) Description pending.
* **`messageHandler`** (function) Description pending.
* **`workQueue`** (Array) Description pending.
* **`awaitingPump`** (boolean) Description pending.
* **`connected`** (boolean) Description pending.
* **`backoffMilliseconds`** (number) Description pending.
* **`nextListenerToken`** (number) Description pending.
* **`reconnectListeners`** (Object) Description pending.
* **`disconnectListeners`** (Object) Description pending.
* **`webSocketClient`** (<a href="#WebSocket">WebSocket</a>) WebSocket client object.
