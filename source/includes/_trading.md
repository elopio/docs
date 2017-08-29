Trading
========

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
