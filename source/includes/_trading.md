Trading
========
<aside class="notice">The Trading section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

Augur allows anyone to create an openly tradable market about any upcoming event. Augur will maintain an order book for each of the markets created. Any trader can place or take an order on the market's order book. When placing an order, if there is an order on the book that will fulfill your order then it will be filled immediately. If there is no matching order, or your order was only partially filled, on the book then your order, or the remainder of what wasn't filled of your order, will be placed on the book until another trader tries to fill the order or we cancel the order and remove it from the book. Orders are executed on a "first come, first served" basis.

The Augur UI will offer users the best prices first when displaying the order book on each market page. Orders are never executed at a worse prices than the limit price set by the user, however they can settle for better than the limit price. Orders can also be partially filled. The UI will automatically include multiple backup/fallback orders to attempt to fill our order in the event that the best order was filled before we sent the transaction. These backup/fallback orders will always fit within the limit price set by the trader.

Trading Example
---------------
Let's use an example binary market trading on the "super big event" coming up. For this example, we want to go long on `Outcome A`. We would submit a bid order to buy `100` shares of `Outcome A` with a limit price of `0.5 ETH`, which will cost `50.0 ETH` plus trading fees and gas used to place the bid order. [Fees](#trading-fees) will be discussed in more details in the next section.

If there are orders on the order book that match our request for `100` shares of `Outcome A` at a price of `0.5 ETH` or cheaper then Augur will fill those orders. We will now own `100` Shares of `Outcome A` and we will have lost `50.0 ETH` plus trading fees and gas cost.

If no order is available on the order book that would partially or completely fill our order request then a bid order would be created and placed on the order book. Whenever an order is placed on the order book something of value is escrowed by the market. In our example, the value we are giving to the market to place our bid would be our `50.0 ETH` plus fees. If we were attempting to sell shares that we currently owned then we would escrow the shares instead of `ETH`. If we cancel our order we can get our `50.0 ETH` back, but we do lose our fees and gas costs. When someone decides to take our order off the order book, we will get our `100` shares of `Outcome A` transferred to us.

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

Creating a market costs a small amount of `ETH`. Without some incentive people won't create markets as they get nothing in return for the `ETH` they spent to create it. Augur solves this problem by allowing market creators to set a Trading Fee for their market. The Trading Fee must be between `0` and `50` percent. Once a market is created the Trading Fee cannot be raised, only lowered.

Augur extracts Trading Fees in order to be able to reward users who create markets, but it also extracts Fees to help support the reporting system. The Reporter Fee isn't set by the market creator like the Trading Fee but is instead set by Augur.

Augur will automatically create a market once a month to use as a data feed for the REP price. This market will be a normal market and it will go through the normal resolution process. Once the REP price is known, Augur can calculate the REP market cap by multiply by the total supply of REP.

The contracts will track the open interest of all markets passively as people interact with Augur, which allows Augur to always have access to the open interest numbers. Once the open interest and REP market caps are known Augur can then wether the REP market cap is too high or too low. Augur then calculates the new Reporter Fee using the following formula:

`current_reporting_fee * (augur_open_interest * 5 / rep_market_cap)`

The current plan is to update this value once a month. If updates where to occur too frequently, the market will not have time to adjust to the new fees. If we update too infrequently, then we are at risk of the security model becoming invalid due to a sudden growth in Augur that isn't yet account for by the fee system.

For simplicity, going forward Trading Fees will refer to both the Trading Fee and the Reporting Fee combined. Trading Fees are extracted anytime settlement occurs and shares are destroyed. This can happen for two reasons:

- Selling a Complete Set
- Redeeming winning shares in a finalized market

Selling a Complete Set can be thought of as exiting your market position. Complete Sets are simply a set of shares for each outcome. If you own a set of shares in each outcome, you can settle those shares and receive `ETH` back for their value minus Trading Fees.

Calculating Trades
------------------
<!--
- Overview of calculations
- Complete Sets - under the hood section?
- all potential outcomes - in a table? -->
In this section we breakdown all potential trade situations and their expected outcome. There are two types of orders, bid orders (requesting to buy) and ask orders (requesting to sell). In our examples below we will first go over all the potential trade possibilities around Bid Orders and Ask Orders. The calculations below use `N` as the number of Shares the order is requesting, `X` as the price per Share for the order, and `Outcome` for the outcome our order is concerning.

### Bid Order Trading

Maker of Bid Order | Taker of Bid Order
--- | ---
**Escrows:** `N` Shares of all outcomes except `Outcome` at `X` Price.<br/> **Intent:** close a short position for `Outcome`. | **Sends:** ETH.<br/> **Intent:** open a short position for `Outcome`.
**Gains:** `(marketMaxPrice - X) * N` ETH. <br/>**Loses:** `N` Shares in all outcomes except `Outcome`. | **Gains:** `N` Shares in all outcomes except `Outcome`. <br/>**Loses:** <span style="white-space: nowrap;">`(marketMaxPrice - X) * N`</span> ETH.

Maker of Bid Order | Taker of Bid Order
--- | ---
**Escrows:** `(X - marketMinPrice) * N` ETH for `N` Shares of <br/>`Outcome` at `X` Price.<br/> **Intent:** open a long position for `Outcome`. | **Sends:**: ETH.<br/> **Intent:** open a short position for `Outcome`.
**Gains:** `N` Shares of `Outcome`. <br/>**Loses:** `(X - marketMinPrice) * N` ETH | **Gains:** `N` Shares of all outcomes except for `Outcome`. <br/>**Loses:** `(marketMaxPrice - X) * N` ETH.

Maker of Bid Order | Taker of Bid Order
--- | ---
**Escrows:** `N` Shares of all outcomes except `Outcome` at `X` Price.<br/> **Intent:** close a short position for `Outcome`. | **Sends:** `N` Shares of `Outcome`.<br/>**Intent:** close a long position for `Outcome`.
**Gains:** <br/><span style="white-space: nowrap;">`((marketMaxPrice - X) * N) - (tradingFees / 2)`</span> ETH. <br/>**Loses:** `N` Shares in all outcomes except `Outcome`. | **Gains:** <br/><span style="white-space: nowrap;">`((X - marketMinPrice) * N) - (tradingFees / 2)`</span> ETH. <br/>**Loses:** `N` Shares in `Outcome`.

Maker of Bid Order | Taker of Bid Order
--- | ---
**Escrows:** `(X - marketMinPrice) * N` ETH for `N` Shares<br/> of `Outcome` at `X` Price.<br/>**Intent:** open a long position for `Outcome`. | **Sends:** `N` Shares of `Outcome`.<br/> **Intent:** close a long position for `Outcome`.
**Gains:** `N` Shares in `Outcome`. <br/>**Loses:** `(X - marketMinPrice) * N` ETH | **Gains:** `(X - marketMinPrice) * N` ETH. <br/>**Loses:** `N` Shares of `Outcome`.


### Ask Order Trading

Maker of Ask Order | Taker of Ask Order
--- | ---
**Escrows:** `N` Shares of `Outcome` at `X` Price.<br/> **Intent:** close a long position for `Outcome`. | **Sends:** ETH.<br/>**Intent:** open a long position for `Outcome`.
**Gains:** `(X - marketMinPrice) * N` ETH. <br/>**Loses:** `N` Shares in `Outcome` | **Gains:** `N` Shares of `Outcome`. <br/>**Loses:** `(X - marketMinPrice) * N` ETH

Maker of Ask Order | Taker of Ask Order
--- | ---
**Escrows:** `(marketMaxPrice - X) * N` ETH for `N` Shares<br/> of `Outcome` at `X` Price.<br/> **Intent:** open a short position for `Outcome`. | **Sends:** ETH.<br/> **Intent:** open a long position for `Outcome`.
**Gains:** `N` Shares in all outcomes except `Outcome`. <br/>**Loses:** `(marketMaxPrice - X) * N` ETH | **Gains:** `N` Shares of `Outcome`. <br/>**Loses:** `(X - marketMinPrice) * N` ETH.

Maker of Ask Order | Taker of Ask Order
--- | ---
**Escrows:** `N` Shares of `Outcome` at `X` Price.<br/> **Intent:** close a long position for `Outcome`. | **Sends:** Shares in all outcomes except `Outcome`. <br/> **Intent:** close a short position for `Outcome`.
**Gains:** <br/><span style="white-space: nowrap;">`((X - marketMinPrice) * N) - (tradingFees / 2)`</span> ETH. <br/>**Loses:** `N` Shares of `Outcome` | **Gains:** <br/><span style="white-space: nowrap;">`((marketMaxPrice - X) * N) - (tradingFees / 2)`</span> ETH. <br/>**Loses:** `N` Shares in all outcomes except `Outcome`.

Maker of Ask Order | Taker of Ask Order
--- | ---
**Escrows:** `(marketMaxPrice - X) * N` ETH for `N` Shares<br/> of `Outcome` at `X` Price.<br/> **Intent:** open a short position for `Outcome`. | **Sends:** Shares in all outcomes except `Outcome`.<br/> **Intent:** close a short position for `Outcome`.
**Gains:** `N` Shares in all outcomes except `Outcome`. <br/>**Loses:** `(marketMaxPrice - X) * N` ETH. | **Gains:** `(marketMaxPrice - X) * N` ETH. <br/>**Loses:** `N` Shares in all outcomes except `Outcome`.
