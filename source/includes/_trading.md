Trading
========
<aside class="notice">The Trading section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

Augur allows anyone to create an openly tradable [Market](#market) about any upcoming event. Augur will maintain an [Order Book](#order-book) for each of the Markets created. Any trader can place or take an [Order](#order) on the Market's Order Book. When placing an trade, if there is an Order on the book that will fulfill your trade request then it will be [Filled](#fill-order) immediately. If there is no matching Order, or your trade request can only be partially Filled, the remainder of what wasn't filled of your trade will be placed on the Order Book as an Order. If you are the [Creator](#order-creator) of the Order you can cancel it to remove it from the Order Book. Orders are executed on a "first come, first served" basis.

The Augur UI will offer users the best prices first when displaying the Order Book on each Market page. Orders are never executed at a worse price than the limit price set by the trader, however they can settle for better than the limit price. Orders can also be partially filled. The UI will automatically include multiple backup/fallback Orders to attempt to fill our Order in the event that the best Order was filled before we sent the transaction. These backup/fallback Orders will always fit within the limit price set by the trader.

Trading Example
---------------
Let's use an example [Binary Market](#binary-market) trading on the "super big event" coming up. For this example, we want to go long on `Outcome A`. We would submit a trade request to buy `100` [Shares](#share) of `Outcome A` with a limit price of `0.5 ETH`, which will cost `50.0 ETH` plus gas used to send the transaction.

If there are Orders on the Order Book that match our request for `100` Shares of `Outcome A` at a price of `0.5 ETH`, or cheaper, then Augur will fill those Orders. We will now own `100` Shares of `Outcome A` and we will have lost `50.0 ETH` plus gas cost.

If no Order is available on the Order Book that would partially or completely fill our trade request then a [Bid Order](#bid-order) would be placed on the Order Book. Whenever an Order is placed on the Order Book something of value is escrowed by the Market. In our example, the value we are giving to the Market to place our bid would be our `50.0 ETH`. If we were attempting to sell Shares that we currently owned then we would escrow the Shares instead of `ETH`. If we cancel our Order we can get our `50.0 ETH` back, the only currency lost would be the gas used to pay to place the trade and cancel the trade. When a [Filler](#order-filler) decides to fill our Order on the Order Book, we will get our `100` shares of `Outcome A` transferred to us.

For more information on how to trade using the Augur API, check the [trade](#trade-tx-api) section of the [Transaction API](#transaction-api).

Settlement Fees Explained
-------------------------

Creating a Market costs a small amount of `ETH`. Without some incentive people won't create Markets as they get nothing in return for the `ETH` they spent to create it. Augur solves this problem by allowing [Market Creators](#market-creator) to set a [Creator Fee](#creator-fee) for their Market. The Creator Fee must be between `0` and `50` percent. Once a Market is created the Creator Fee cannot be raised, only lowered.

Augur also extracts Fees to help support the [Reporting System](#reporting) known as the [Reporting Fee](#reporting-fee). [Settlement Fees](#settlement-fees) refer to both the Creator Fee and the Reporting Fee combined. The Reporting Fee isn't set by the Market Creator, like the Creator Fee, but is instead set by an automated Augur Market.

Augur will automatically create a Market once every Thirty (30) days to use as a data feed for the [REP](#rep) price. This Market will act just like a regular Market and it will go through the [Dispute Rounds](#dispute-round) process in order to be [Resolved](#market-resolution). Once the REP price is known, Augur can calculate the REP Market cap and the appropriate Reporting Fee to ensure the security of the Reporting System.

The Augur contracts will track the open interest of all Markets passively as people interact with Augur, which allows Augur to always have access to the open interest numbers. Once the open interest and REP market caps are known Augur can then determine wether the Reporting Fee is too high or too low. Augur then calculates the new Reporter Fee using the following formula:

`current_reporting_fee * (augur_open_interest * 5 / rep_market_cap)`

The Reporting Fee will be updated once every 30 days, or once every [Reporting Window](#reporting-window). If updates where to occur too frequently, the market will not have time to adjust to the new fees. If we update too infrequently, then we are at risk of the security model becoming invalid due to a sudden growth in Augur that isn't yet accounted for by the fee system.

Settlement Fees are extracted anytime [Settlement](#settlement) occurs and Shares are destroyed. This can happen for two reasons:

- Selling a [Complete Set](#complete-set)
- Redeeming shares for the finalized outcome in a [Finalized Market](#finalized-market)

Selling a Complete Set can be thought of as exiting your market [Position](#position). Complete Sets are a set of Shares for each [Outcome](#outcome). Complete Sets are priced differently for each Market, and are determined by the [Number of Ticks](#number-of-ticks), which represents all possible price points, or [Ticks](#tick), for a Market as well as the amount of attoeth it costs to purchase a Complete Set for the Market. If you own a set of Shares in each Outcome, you can Settle those Shares and receive `ETH` back for their value minus Settlement Fees.

Calculating Trades
------------------

In this section we break down all potential trade situations and their expected result. There are two types of [Orders](#order), [Bid Orders](#bid-order) (requesting to buy) and [Ask Orders](#ask-order) (requesting to sell). In our examples below we will go over all the potential trade possibilities around Bid Orders and Ask Orders. Orders are placed on the [Order Book](#order-book) by [Order Creators](#order-creator) and contain four important details: The Creator of the Order, the price of the Order, The amount of [Shares](#share) or ETH escrowed, and the [Outcome](#outcome) we plan to trade on. The price can be any value between 0 and the [Number of Ticks](#number-of-ticks). The calculations below use `num_shares` as the number of Shares the Order is requesting, `price` as the price per Share for the Order, `outcome` for the Outcome our Order is trading on, `num_ticks` as the Number of Ticks, and `fee_rate` as the [Settlement Fees](#settlement-fees) extracted during the [Settlement](#settlement) of Shares.

The Formulas for determining how much opening a Long or Short position costs are as follows:

**Cost of Opening a Long Position**(`open_long_position_cost`):

`num_shares * price`

**Cost of Opening a Short Position**(`open_short_position_cost`):

`num_shares * (num_ticks - price)`

The formulas for determining the payout and the fees required to be paid by each side of an Order are as follows:

**Total payout for closing a Long Position**(`total_payout_closing_long`):

`num_shares * price / num_ticks`

**Total payout for closing a Short Position**(`total_payout_closing_short`):

`num_shares * (num_ticks - price) / num_ticks`

**Fees paid for closing a Long Position**(`long_position_fees`):

`total_payout_closing_long * fee_rate`

**Fees paid for closing a Short Position**(`short_position_fees`):

`total_payout_closing_short * fee_rate`

Below are some more examples of specific order situations and their results:

### Bid Order Trading

Creator of Bid Order | Filler of Bid Order
--- | ---
**Escrows:** `num_shares` of all outcomes except `outcome`<br/> **Order Details:** `price`, `creator`, `outcome`, `escrow`<br/> **Intent:** close a short position for `outcome`. | **Sends:** `open_short_position_cost` ETH.<br/> **Intent:** open a short position for `outcome`.
**Gains:** `total_payout_closing_short` ETH. <br/>**Loses:** `num_shares` in all outcomes except `outcome`. | **Gains:** `num_shares` in all outcomes except `outcome`. <br/>**Loses:** <span style="white-space: nowrap;">`total_payout_closing_short`</span> ETH.

Creator of Bid Order | Filler of Bid Order
--- | ---
**Escrows:** `open_long_position_cost` ETH<br/> **Order Details:** `price`, `creator`, `outcome`, `escrow` <br/> **Intent:** open a long position for `outcome`. | **Sends:**: `open_short_position_cost` ETH.<br/> **Intent:** open a short position for `outcome`.
**Gains:** `num_shares` of `outcome`. <br/>**Loses:** `open_long_position_cost` ETH | **Gains:** `num_shares` of all outcomes except for `outcome`. <br/>**Loses:** `open_short_position_cost` ETH.

Creator of Bid Order | Filler of Bid Order
--- | ---
**Escrows:** `num_shares` of all outcomes except `outcome`<br/> **Order Details:** `price`, `creator`, `outcome`, `escrow` <br/> **Intent:** close a short position for `outcome`. | **Sends:** `num_shares` of `outcome`.<br/>**Intent:** close a long position for `outcome`.
**Gains:** <span style="white-space: nowrap;">`total_payout_closing_short - short_position_fees`</span> ETH. <br/>**Loses:** `num_shares` in all outcomes except `outcome`. | **Gains:** <span style="white-space: nowrap;">`total_payout_closing_long - long_position_fees`</span> ETH. <br/>**Loses:** `num_shares` in `outcome`.

Creator of Bid Order | Filler of Bid Order
--- | ---
**Escrows:** `open_long_position_cost` ETH<br/> **Order Details:** `price`, `creator`, `outcome`, `escrow` <br/>**Intent:** open a long position for `outcome`. | **Sends:** `num_shares` of `outcome`.<br/> **Intent:** close a long position for `outcome`.
**Gains:** `num_shares` in `outcome`. <br/>**Loses:** `open_long_position_cost` ETH | **Gains:** `total_payout_closing_short` ETH. <br/>**Loses:** `num_shares` of `outcome`.


### Ask Order Trading

Creator of Ask Order | Filler of Ask Order
--- | ---
**Escrows:** `num_shares` of `outcome`.<br/> **Order Details:** `price`, `creator`, `outcome`, `escrow` <br/> **Intent:** close a long position for `outcome`. | **Sends:** `open_long_position_cost` ETH.<br/>**Intent:** open a long position for `outcome`.
**Gains:** `total_payout_closing_long` ETH. <br/>**Loses:** `num_shares` in `outcome` | **Gains:** `num_shares` of `outcome`. <br/>**Loses:** `open_long_position_cost` ETH

Creator of Ask Order | Filler of Ask Order
--- | ---
**Escrows:** `open_short_position_cost` ETH.<br/> **Order Details:** `price`, `creator`, `outcome`, `escrow` <br/> **Intent:** open a short position for `outcome`. | **Sends:** `open_long_position_cost` ETH.<br/> **Intent:** open a long position for `outcome`.
**Gains:** `num_shares` in all outcomes except `outcome`. <br/>**Loses:** `open_short_position_cost` ETH | **Gains:** `num_shares` of `outcome`. <br/>**Loses:** `open_long_position_cost` ETH.

Creator of Ask Order | Filler of Ask Order
--- | ---
**Escrows:** `num_shares` of `outcome`.<br/> **Order Details:** `price`, `creator`, `outcome`, `escrow` <br/> **Intent:** close a long position for `outcome`. | **Sends:** Shares in all outcomes except `outcome`. <br/> **Intent:** close a short position for `outcome`.
**Gains:** <span style="white-space: nowrap;">`total_payout_closing_long - long_position_fees`</span> ETH. <br/>**Loses:** `num_shares` of `outcome` | **Gains:** <span style="white-space: nowrap;">`total_payout_closing_short - short_position_fees`</span> ETH. <br/>**Loses:** `num_shares` in all outcomes except `outcome`.

Creator of Ask Order | Filler of Ask Order
--- | ---
**Escrows:** `open_short_position_cost` ETH.<br/> **Order Details:** `price`, `creator`, `outcome`, `escrow` <br/> **Intent:** open a short position for `outcome`. | **Sends:** Shares in all outcomes except `outcome`.<br/> **Intent:** close a short position for `outcome`.
**Gains:** `num_shares` in all outcomes except `outcome`. <br/>**Loses:** `open_short_position_cost` ETH. | **Gains:** `total_payout_closing_short` ETH. <br/>**Loses:** `num_shares` in all outcomes except `outcome`.
