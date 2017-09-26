Trading
========
<aside class="notice">The Trading section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

Augur allows anyone to create an openly tradable [Market](#market) about any upcoming event. Augur will maintain an [Order Book](#order-book) for each of the markets created. Any trader can place or take an [Order](#order) on the Market's Order Book. When placing an Order, if there is an Order on the book that will fulfill your Order then it will be [Filled](#fill-order) immediately. If there is no matching Order, or your Order was only partially Filled, the remainder of what wasn't filled of your order will be placed on the Order Book. If you are the [Maker](#maker) of the Order you can cancel it to remove it from the Order Book. Orders are executed on a "first come, first served" basis.

The Augur UI will offer users the best prices first when displaying the Order Book on each Market page. Orders are never executed at a worse price than the limit price set by the trader, however they can settle for better than the limit price. Orders can also be partially filled. The UI will automatically include multiple backup/fallback Orders to attempt to fill our Order in the event that the best Order was filled before we sent the transaction. These backup/fallback Orders will always fit within the limit price set by the trader.

Trading Example
---------------
Let's use an example [Binary Market](#binary-market) trading on the "super big event" coming up. For this example, we want to go long on `Outcome A`. We would submit a [Bid Order](#bid-order) to buy `100` [Shares](#shares) of `Outcome A` with a limit price of `0.5 ETH`, which will cost `50.0 ETH` plus [Trading Fees](#trading-fees) and GAS used to place the Bid Order. [Fees](#trading-fees) will be discussed in more details in the next section.

If there are Orders on the Order Book that match our request for `100` Shares of `Outcome A` at a price of `0.5 ETH` or cheaper then Augur will fill those Orders. We will now own `100` Shares of `Outcome A` and we will have lost `50.0 ETH` plus Trading Fees and GAS cost.

If no Order is available on the Order Book that would partially or completely fill our Order request then a Bid Order would be placed on the Order Book. Whenever an Order is placed on the Order Book something of value is escrowed by the Market. In our example, the value we are giving to the Market to place our bid would be our `50.0 ETH` plus fees. If we were attempting to sell Shares that we currently owned then we would escrow the Shares instead of `ETH`. If we cancel our Order we can get our `50.0 ETH` back, the only currency lost would be the GAS used to pay to place the trade and cancel the trade. When a [Taker](#taker) decides to fill our Order on the Order Book, we will get our `100` shares of `Outcome A` transferred to us.

For more information on how to trade using the Augur API, check the [trade](#trade-tx-api) section of the [Transaction API](#transaction-api).

<!--
- Trading Fees
- Reporting Fees and how they work
- Complete Sets
- Worst Case Loss outline and examples

  -->
Trading Fees
------------
<!--
Outline of this section:

- why do they exist
- how are they set
- how do they effect trades/when are they extracted

-->

Creating a Market costs a small amount of `ETH`. Without some incentive people won't create Markets as they get nothing in return for the `ETH` they spent to create it. Augur solves this problem by allowing [Market Creators](#market-creator) to set a [Trading Fee](#trading-fee) for their Market. The Trading Fee must be between `0` and `50` percent. Once a Market is created the Trading Fee cannot be raised, only lowered.

Augur extracts Trading Fees in order to be able to reward users who create Markets, but it also extracts Fees to help support the [Reporting System](#reporting). The [Reporting Fee](#reporting-fee) isn't set by the Market Creator like the Trading Fee but is instead set by an automated Augur Market.

Augur will automatically create a Market once every Thirty (30) days to use as a data feed for the [REP](#rep) price. This Market will be a normal Market and it will go through the normal Reporting process. Once the REP price is known, Augur can calculate the REP Market cap and then figure out the appropriate Reporting Fee to ensure the security of the Reporting System.

The Augur contracts will track the open interest of all Markets passively as people interact with Augur, which allows Augur to always have access to the open interest numbers. Once the open interest and REP market caps are known Augur can then determine wether the Reporting Fee is too high or too low. Augur then calculates the new Reporter Fee using the following formula:

`current_reporting_fee * (augur_open_interest * 5 / rep_market_cap)`

The Reporting Fee will be updated once every 30 days. If updates where to occur too frequently, the market will not have time to adjust to the new fees. If we update too infrequently, then we are at risk of the security model becoming invalid due to a sudden growth in Augur that isn't yet account for by the fee system.

For simplicity, going forward Trading Fees will refer to both the Trading Fee and the Reporting Fee combined. Trading Fees are extracted anytime [Settlement](#settlement) occurs and Shares are destroyed. This can happen for two reasons:

- Selling a [Complete Set](#complete-set)
- Redeeming shares for the finalized outcome in a [Finalized Market](#finalized-market)

Selling a Complete Set can be thought of as exiting your market [Position](#position). Complete Sets are a set of Shares for each [Outcome](#outcome). Complete Sets are priced differently for each market, and are determined by the [Market Denominator](#market-denominator), which represents the amount of attoeth it costs to purchase a Complete Set for the Market. If you own a set of Shares in each Outcome, you can Settle those Shares and receive `ETH` back for their value minus Trading Fees.

Calculating Trades
------------------
<!--
- Overview of calculations
- Complete Sets - under the hood section?
- all potential outcomes - in a table? -->
In this section we break down all potential trade situations and their expected result. There are two types of [Orders](#order), [Bid Orders](#bid-order) (requesting to buy) and [Ask Orders](#ask-order) (requesting to sell). In our examples below we will go over all the potential trade possibilities around Bid Orders and Ask Orders. Orders are placed on the [Order Book](#order-book) by [Makers](#maker) and contain four important details: The Maker of the Order, the Price of the Order, The amount of [Shares](#shares) or ETH escrowed, and the [Outcome](#outcome) we plan to trade on. The Price can be any value between 0 and the [Market Denominator](#market-denominator). The calculations below use `Num_Shares` as the number of Shares the Order is requesting, `Price` as the price per Share for the Order, `Outcome` for the Outcome our Order is trading on, `marketDenominator` as the Market Denominator, and `feeRate` as the [Trading Fees](#trading-fees) extracted during the [Settlement](#settlement) of Shares.

The formulas for determining the payout and the fees required to be paid by each side of an Order are as follows:

**Total payout for closing a Long Position**(`totalPayoutClosingLong`):

`Num_Shares * Price / marketDenominator`

**Total payout for closing a Short Position**(`totalPayoutClosingShort`):

`Num_Shares * (marketDenominator - Price) / marketDenominator`

**Fees paid for closing a Long Position**(`longPositionFeesPaid`):

`totalPayoutClosingLong * (1 - feeRate)`

**Fees paid for closing a Short Position**(`shortPositionFeesPaid`):

`totalPayoutClosingShort * (1 - fee_rate)`

Below are some more examples of specific order situations and their results:

### Bid Order Trading

Maker of Bid Order | Taker of Bid Order
--- | ---
**Escrows:** `Num_Shares` of all outcomes except `Outcome`<br/> **Order Details:** `Price`,`Maker`,`Outcome`<br/> **Intent:** close a short position for `Outcome`. | **Sends:** ETH.<br/> **Intent:** open a short position for `Outcome`.
**Gains:** `totalPayoutClosingShort` ETH. <br/>**Loses:** `Num_Shares` in all outcomes except `Outcome`. | **Gains:** `Num_Shares` in all outcomes except `Outcome`. <br/>**Loses:** <span style="white-space: nowrap;">`totalPayoutClosingShort`</span> ETH.

Maker of Bid Order | Taker of Bid Order
--- | ---
**Escrows:** `(P - minPrice) * N` ETH for `N` Shares of <br/>`Outcome` at `P` Price.<br/> **Intent:** open a long position for `Outcome`. | **Sends:**: ETH.<br/> **Intent:** open a short position for `Outcome`.
**Gains:** `N` Shares of `Outcome`. <br/>**Loses:** `(P - minPrice) * N` ETH | **Gains:** `N` Shares of all outcomes except for `Outcome`. <br/>**Loses:** `(maxPrice - P) * N` ETH.

Maker of Bid Order | Taker of Bid Order
--- | ---
**Escrows:** `N` Shares of all outcomes except `Outcome` at `P` Price.<br/> **Intent:** close a short position for `Outcome`. | **Sends:** `N` Shares of `Outcome`.<br/>**Intent:** close a long position for `Outcome`.
**Gains:** <br/><span style="white-space: nowrap;">`totalPayoutClosingShort - shortPositionFeesPaid`</span> ETH. <br/>**Loses:** `N` Shares in all outcomes except `Outcome`. | **Gains:** <br/><span style="white-space: nowrap;">`(range - fees) * (P - minPrice) * N`</span> ETH. <br/>**Loses:** `N` Shares in `Outcome`.

Maker of Bid Order | Taker of Bid Order
--- | ---
**Escrows:** `(P - minPrice) * N` ETH for `N` Shares<br/> of `Outcome` at `P` Price.<br/>**Intent:** open a long position for `Outcome`. | **Sends:** `N` Shares of `Outcome`.<br/> **Intent:** close a long position for `Outcome`.
**Gains:** `N` Shares in `Outcome`. <br/>**Loses:** `(P - minPrice) * N` ETH | **Gains:** `(P - minPrice) * N` ETH. <br/>**Loses:** `N` Shares of `Outcome`.


### Ask Order Trading

Maker of Ask Order | Taker of Ask Order
--- | ---
**Escrows:** `N` Shares of `Outcome` at `P` Price.<br/> **Intent:** close a long position for `Outcome`. | **Sends:** ETH.<br/>**Intent:** open a long position for `Outcome`.
**Gains:** `(P - minPrice) * N` ETH. <br/>**Loses:** `N` Shares in `Outcome` | **Gains:** `N` Shares of `Outcome`. <br/>**Loses:** `(P - minPrice) * N` ETH

Maker of Ask Order | Taker of Ask Order
--- | ---
**Escrows:** `(maxPrice - P) * N` ETH for `N` Shares<br/> of `Outcome` at `P` Price.<br/> **Intent:** open a short position for `Outcome`. | **Sends:** ETH.<br/> **Intent:** open a long position for `Outcome`.
**Gains:** `N` Shares in all outcomes except `Outcome`. <br/>**Loses:** `(maxPrice - P) * N` ETH | **Gains:** `N` Shares of `Outcome`. <br/>**Loses:** `(P - minPrice) * N` ETH.

Maker of Ask Order | Taker of Ask Order
--- | ---
**Escrows:** `N` Shares of `Outcome` at `P` Price.<br/> **Intent:** close a long position for `Outcome`. | **Sends:** Shares in all outcomes except `Outcome`. <br/> **Intent:** close a short position for `Outcome`.
**Gains:** <br/><span style="white-space: nowrap;">`(range - fees) * (P - minPrice) * N`</span> ETH. <br/>**Loses:** `N` Shares of `Outcome` | **Gains:** <br/><span style="white-space: nowrap;">`(range - fees) * (maxPrice - P) * N`</span> ETH. <br/>**Loses:** `N` Shares in all outcomes except `Outcome`.

Maker of Ask Order | Taker of Ask Order
--- | ---
**Escrows:** `(maxPrice - P) * N` ETH for `N` Shares<br/> of `Outcome` at `P` Price.<br/> **Intent:** open a short position for `Outcome`. | **Sends:** Shares in all outcomes except `Outcome`.<br/> **Intent:** close a short position for `Outcome`.
**Gains:** `N` Shares in all outcomes except `Outcome`. <br/>**Loses:** `(maxPrice - P) * N` ETH. | **Gains:** `(maxPrice - P) * N` ETH. <br/>**Loses:** `N` Shares in all outcomes except `Outcome`.
