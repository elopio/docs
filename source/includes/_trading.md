Trading
========
Augur allows anyone to create an openly tradable market about any upcoming event. Augur will maintain an order book for each of the markets created. Any trader can place or take an order on the market's order book. When placing an order, if there is an order on the book that will fulfill your order then it will be filled immediately. If there is no matching order, or your order was only partially filled, on the book then your order, or the remainder of what wasn't filled of your order, will be placed on the book until another trader tries to fill the order or we cancel the order and remove it from the book. Orders are executed on a "first come, first served" basis.

The Augur UI will offer users the best prices first when displaying the order book on each market page. Orders are never executed at a worse prices than the limit price set by the user, however they can settle for better than the limit price. Orders can also be partially filled. The UI will automatically include multiple backup/fallback orders to attempt to fill our order in the event that the best order was filled before we sent the transaction. These backup/fallback orders will always fit within the limit price set by the trader.

Trading Example
---------------
Let's use an example binary market trading on the "super big event" coming up. For this example, we want to go long on `Outcome A`. We would submit a bid order to buy `100` shares of `Outcome A` with a limit price of `0.5 ETH`, which will cost `50.0 ETH` plus trading fees and gas used to place the bid order. [Fees](http://docs.augur.net/#trading-fees) will be discussed in more details in the next section.

If there are orders on the order book that match our request for `100` shares of `Outcome A` at a price of `0.5 ETH` or cheaper then Augur will fill those orders. We will now own `100` Shares of `Outcome A` and we will have lost `50.0 ETH` plus trading fees and gas cost.

If no order is available on the order book that would partially or completely fill our order request then a bid order would be created and placed on the order book. Whenever an order is placed on the order book something of value is escrowed by the market. In our example, the value we are giving to the market to place our bid would be our `50.0 ETH` plus fees. If we were attempting to sell shares that we currently owned then we would escrow the shares instead of `ETH`. If we cancel our order we can get our `50.0 ETH` back, but we do lose our fees and gas costs. When someone decides to take our order off the order book, we will get our `100` shares of `Outcome A` transferred to us.

For more information on how to trade using the Augur API, check the [trade](http://docs.augur.net/#trade-tx-api) section of the [Transaction API](http://docs.augur.net/#transaction-api).

Trading Fees
------------
<!--
Outline of this section:

- why do they exist
- how are they set
- how do they effect trades/when are they extracted

-->

Augur extracts Trading Fees in order to be able to reward users who create markets and report on the outcome of those markets. Creating a market costs a small amount of `ETH`. Without some incentive people won't create markets as they get nothing in return for the `ETH` they spent to create it. Augur solves this problem by allowing market creators to set a Trading Fee for their market. The Trading Fee must be between `0` and `50` percent. Once a market is created the Trading Fee cannot be raised, only lowered.

There is also a reporter fee that isn't set by the market creator. The reporter fee is calculated by a recurring market on Augur and is generally targeted to drive REP value up to five times the total open interest in Augur markets. For simplicity, when Trading Fees is mentioned below, we are talking about the combined fees of the Trading Fee set by the market creator and the reporter fee.

Trading Fees are extracted anytime settlement occurs and shares are destroyed. This can happen for two reasons:

- Selling a Complete Set
- Redeeming winning shares in a finalized market

Selling a Complete Set can be thought of as exiting your market positions. Complete Sets are simply a set of shares for each outcome. If you own a set of shares in each outcome, you can settle those shares and receive `ETH` back for their value minus Trading Fees.


Worst Case Loss
---------------
<!--
- Overview of calculations
- Complete Sets - under the hood section?
- all potential outcomes - in a table? -->

<!-- scrap text
-----------
Ideally some time passes and now `Outcome A` is trading at `0.65 ETH` a share. If we sell our `100` shares of `Outcome A` at a limit of `0.65 ETH` a share we will get `65.0 ETH` minus the fees for the market back. If our market has a `1%` trading fee we would get back `64.35 ETH`, and the `0.65 ETH` will be kept by the market to be able to pay out the market creator and reporters.

If there was no orders on the order book in the above example that fit our order then our bid order to buy `100` shares of `A` at `0.5 ETH` will be created and placed on the order book. Our `50.0 ETH` will be held by the market until someone comes along to take our order off the book. A Trader can take the order off the book by providing the `50.0 ETH` required to create new Complete Sets of shares or if they have `100` shares of `Outcome A` they can simply provide that. If someone takes our order by providing the shares we want then they get our `50.0 ETH` back from the market and we would receive our `100` shares of `Outcome A`. If the trader taking our order doesn't have any shares of `Outcome A` but does have `50.0 ETH` they can provide that instead and the market will create new Complete Sets of shares.

Complete Sets are simply a set of shares for each outcome, so a Complete Set would be any amount of shares in every outcome of our market as long as each share is worth that amount. In other words, `1.0` share of every outcome is a complete set, but so is `0.25` shares of each outcome, or `500.0` shares of each outcome. In our above example, the maker of the original bid order for `100` Shares of `Outcome A` will get their `100` shares of `Outcome A` from the complete sets created. Our trader who is taking the order and provided the rest of the `ETH` to create the Complete Sets will receive `100` shares in `Outcome B`.

When one person has a Complete Set they can sell the Complete Set of shares back to the market and the market will return ETH equal to the cost of a Complete Set. Complete Set costs are calculated by the maximum display price for the market minus the minimum display price. So if the max is `1 ETH` and the min is `0 ETH` then a Complete Set costs `1 ETH` (1 - 0 = 1). For Binary and Categorical Market types, the Complete Set Cost should always be `1 ETH`. A Scalar Market could be setup to have a maximum value of `100,000` and a minimum value of `4,500`. In this case, in order to purchase `1` complete set it would cost `95,500.00 ETH` (100,000 - 4,500 = 95,500). For a market with that large of a spread, it might make sense to only purchase thousandths or millionths of a share instead so the prices are a bit more reasonable.

 -->

Legacy Trading Section (to be removed)
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

Pending transactions and what orders have already been picked up in pending broadcasted transactions are shown in the book.  These orders are removed if the other side has already been taken in the transaction pool (those orders get executed first on Ethereum anyway based on time precedence and default gas price).  If not processed / taken up, those orders are placed back on the book.  If two orders at same price are placed on the book, the one placed first is executed first.
