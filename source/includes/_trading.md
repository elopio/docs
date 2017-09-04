Trading
========
<aside class="notice">The Trading section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

Augur allows anyone to create an openly tradable market about any upcoming event. Augur will maintain an order book for each of the markets created. Any trader can place or take an order on the market's order book. When placing an order, if there is an order on the book that will fulfill your order then it will be filled immediately. If there is no matching order, or your order was only partially filled, on the book then your order, or the remainder of what wasn't filled of your order, will be placed on the book until another trader tries to fill the order or we cancel the order and remove it from the book. Orders are executed on a "first come, first served" basis.

The Augur UI will offer users the best prices first when displaying the order book on each market page. Orders are never executed at a worse prices than the limit price set by the user, however they can settle for better than the limit price. Orders can also be partially filled. The UI will automatically include multiple backup/fallback orders to attempt to fill our order in the event that the best order was filled before we sent the transaction. These backup/fallback orders will always fit within the limit price set by the trader.

Trading Example
---------------
Let's use an example binary market trading on the "super big event" coming up. For this example, we want to go long on `Outcome A`. We would submit a bid order to buy `100` shares of `Outcome A` with a limit price of `0.5 ETH`, which will cost `50.0 ETH` plus trading fees and gas used to place the bid order. [Fees](http://docs.augur.net/#trading-fees) will be discussed in more details in the next section.

If there are orders on the order book that match our request for `100` shares of `Outcome A` at a price of `0.5 ETH` or cheaper then Augur will fill those orders. We will now own `100` Shares of `Outcome A` and we will have lost `50.0 ETH` plus trading fees and gas cost.

If no order is available on the order book that would partially or completely fill our order request then a bid order would be created and placed on the order book. Whenever an order is placed on the order book something of value is escrowed by the market. In our example, the value we are giving to the market to place our bid would be our `50.0 ETH` plus fees. If we were attempting to sell shares that we currently owned then we would escrow the shares instead of `ETH`. If we cancel our order we can get our `50.0 ETH` back, but we do lose our fees and gas costs. When someone decides to take our order off the order book, we will get our `100` shares of `Outcome A` transferred to us.

For more information on how to trade using the Augur API, check the [trade](http://docs.augur.net/#trade-tx-api) section of the [Transaction API](http://docs.augur.net/#transaction-api).

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
In this section we breakdown all potential trade situations and their expected outcome. There are two types of orders, bid orders (requesting to buy) and ask orders (requesting to sell). In our examples below we will first go over all the potential trade possibilities around Bid Orders and then we will do the same for Ask Orders. The calculations below use `N` as the number of shares the order is requesting, `X` as the price per share for the order, and `Outcome` for the outcome our order is concerning.

### Bid Order Trading
**Maker of Order Escrowed:** | **Taker of Order Sends:**
---|---
`N` shares of all outcomes except `Outcome` into a **Bid** order at `X` price.<br/> Maker is intending to close a short position for `Outcome`. | No shares, only ETH.<br/> Taker is intending to open a short position for `Outcome`. |
**Maker Value Changes:** | **Taker Value Changes:**
**Gains:** `(marketMaxPrice - X) * N` ETH. <br/>**Loses:** `N` shares in all outcomes except `Outcome`. | **Gains:** `N` shares in all outcomes except `Outcome`. <br/>**Loses:** <span style="white-space: nowrap;">`(marketMaxPrice - X) * N`</span> ETH.

**Maker of Order Escrowed:** | **Taker of Order Sends:**
---|---
`(X - marketMinPrice) * N` ETH into a **Bid** order for `N` shares<br/> of `Outcome` at `X` Price.<br/> Maker is intending to open a long position for `Outcome`. | No shares, only ETH.<br/> Taker is intending to open a short position for `Outcome`.
**Maker Value Changes:** | **Taker Value Changes:**
**Gains:** `N` shares of `Outcome`. <br/>**Loses:** `(X - marketMinPrice) * N` ETH | **Gains:** `N` shares of all outcomes except for `Outcome`. <br/>**Loses:** `(marketMaxPrice - X) * N` ETH.

**Maker of Order Escrowed:** | **Taker of Order Sends:**
---|---
`N` shares of all outcomes except `Outcome` into a **Bid** order for `Outcome` at `X` price.<br/> Maker intends to close a short position for `Outcome`. | `N` shares of `Outcome`, no ETH.<br/> Taker intends to close a long position for `Outcome`.
**Maker Value Changes:** | **Taker Value Changes:**
**Gains:** <br/><span style="white-space: nowrap;">`((marketMaxPrice - X) * N) - (tradingFees / 2)`</span> ETH. <br/>**Loses:** `N` shares in all outcomes except `Outcome`. | **Gains:** <br/><span style="white-space: nowrap;">`((X - marketMinPrice) * N) - (tradingFees / 2)`</span> ETH. <br/>**Loses:** `N` shares in `Outcome`.

**Maker of Order Escrowed:** | **Taker of Order Sends:**
---|---
`(X - marketMinPrice) * N` ETH into a **Bid** order for `N` shares<br/> of `Outcome` at `X` Price.<br/> Maker is intending to open a long position for `Outcome`. |  `N` shares of `Outcome`, no ETH.<br/> Taker intends to close a long position for `Outcome`.
**Maker Value Changes:** | **Taker Value Changes:**
**Gains:** `N` shares in `Outcome`. <br/>**Loses:** `(X - marketMinPrice) * N` ETH | **Gains:** `(X - marketMinPrice) * N` ETH. <br/>**Loses:** `N` shares of `Outcome`.

### Ask Order Trading

**Maker of Order Escrowed:** | **Taker of Order Sends:**
---|---
`N` shares of `Outcome` into a **Ask** order at `X` price.<br/> Maker is intending to close a long position for `Outcome`. | No shares, only ETH.<br/>  Taker is intending to open a long position for `Outcome`.
**Maker Value Changes:** | **Taker Value Changes:**
**Gains:** `(X - marketMinPrice) * N` ETH. <br/>**Loses:** `N` shares in `Outcome` | **Gains:** `N` shares of `Outcome`. <br/>**Loses:** `(X - marketMinPrice) * N` ETH

**Maker of Order Escrowed:** | **Taker of Order Sends:**
---|---
`(marketMaxPrice - X) * N` ETH into an **Ask** order for `N` shares<br/> of `Outcome` at `X` price.<br/> Maker intends to open a short position for `Outcome`. | No shares, only ETH.<br/> Taker would like open a long position for `Outcome`.
**Maker Value Changes:** | **Taker Value Changes:**
**Gains:** `N` shares in all outcomes except `Outcome`. <br/>**Loses:** `(marketMaxPrice - X) * N` ETH | **Gains:** `N` shares of `Outcome`. <br/>**Loses:** `(X - marketMinPrice) * N` ETH.

**Maker of Order Escrowed:** | **Taker of Order Sends:**
---|---
`N` shares of `Outcome` into an **Ask** order at `X` price.<br/> Maker is intending to close a long position for `Outcome`. | Shares in all outcomes except `Outcome`, no ETH.<br/> Taker is intending to close a short position for `Outcome`.
**Maker Value Changes:** | **Taker Value Changes:**
**Gains:** <br/><span style="white-space: nowrap;">`((X - marketMinPrice) * N) - (tradingFees / 2)`</span> ETH. <br/>**Loses:** `N` shares of `Outcome` | **Gains:** <br/><span style="white-space: nowrap;">`((marketMaxPrice - X) * N) - (tradingFees / 2)`</span> ETH. <br/>**Loses:** `N` shares in all outcomes except `Outcome`.

**Maker of Order Escrowed:** | **Taker of Order Sends:**
---|---
`(marketMaxPrice - X) * N` ETH into an **Ask** order for `N` shares<br/> of `Outcome` at `X` price.<br/> Maker intends to open a short position for `Outcome`. | Shares in all outcomes except `Outcome`, no ETH.<br/> Taker is intending to close a short position for `Outcome`.
**Maker Value Changes:** | **Taker Value Changes:**
**Gains:** `N` shares in all outcomes except `Outcome`. <br/>**Loses:** `(marketMaxPrice - X) * N` ETH. | **Gains:** `(marketMaxPrice - X) * N` ETH. <br/>**Loses:** `N` shares in all outcomes except `Outcome`.

<!-- Legacy Trading Section (to be removed)
--------------------------------------

Traders submit bids and asks. Orders are executed if another trader will match/offer better terms. Orders executed first come first served.  UI offers users the best prices first, in case another trader has already picked up those orders by broadcasting their transaction first (UI can also check this in the transaction pool). The UI includes multiple backup/fallback orders in their transactions that the user would still be willing to trade (provided they're within her limit parameters). Orders are never executed at worse prices than their limit prices (but can be better). Orders can be partially filled.

### How to trade

- To go long, buy on exchange via bid or buy a complete set of all outcomes and sell everything else
- To go short buy a whole set and sell the one you don't like (or call the `short_sell` function)
- Spreads still stay tight due to super easy arbitrage from buy/sell complete sets
- To sell shares held buy the other outcomes and turn in a complete set or sell directly via asks.  Whichever is cheaper should be done first.

When someone enters an order in the UI the following happens:

- Find all orders on the book that can fill that order, so price is <= if buying and >= if selling
- Call `trade(max_value, max_amount, trade_ids:arr)` which will return the amount of money that still isn't filled at the end of trading
- If it returns 0 for the max value and amounts the UI is done
- If it returns a positive number, place a buy or sell order on the book for the amount it returns using `buy(amount, price, market_id, outcome)` or `sell(amount, price, market_id, outcome)`
- This will then `log(type=log_add_tx, $market_id, msg.sender, $type, $price, $amount, $outcome, trade_id)` where type is either `BID` or `ASK`, rest is self explanatory
- The UI calls `get_trade_ids(market_id)` to get a list of all open orders on the book
- Then to populate the order books for each outcome in the UI: `get_trade(id)`
- If `> max` or `< min` don't show order

Pending transactions and what orders have already been picked up in pending broadcasted transactions are shown in the book.  These orders are removed if the other side has already been taken in the transaction pool (those orders get executed first on Ethereum anyway based on time precedence and default gas price).  If not processed / taken up, those orders are placed back on the book.  If two orders at same price are placed on the book, the one placed first is executed first. -->
