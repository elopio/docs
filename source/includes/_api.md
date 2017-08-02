API
===
```javascript
// After installing, just require augur.js to use it.
var Augur = require("augur.js");
var augur = new Augur();

// Attempt to connect to a local Ethereum node
augur.connect({http: "http://127.0.0.1:8545"});

// Connect to Augur's public node with websocket support
augur.connect({http: "https://eth3.augur.net", ws: "wss://ws.augur.net"});

// Connect to a local Ethereum node with websocket and IPC support
augur.connect({
    http: "http://127.0.0.1:8545",
    ws: "ws://127.0.0.1:8546",
    ipc: process.env.HOME + "/.ethereum/geth.ipc"
});

```
The Augur API is a set of JavaScript bindings for the methods encoded in Augur's [smart contracts](https://github.com/AugurProject/augur-core).  The API method name, as well as its parameters, are generally identical to those of the underlying smart contract method.

Augur's [core contracts](https://github.com/AugurProject/augur-core) exist on Ethereum's decentralized network.  The various serialization, networking, and formatting tasks required to communicate with the Augur contracts from a web application are carried out by Augur's [middleware](http://docs.augur.net/#architecture).

[augur.js](https://github.com/AugurProject/augur.js) is the Augur JavaScript SDK, and is the user-facing component of the middleware.  If you want to interact with the Augur contracts from a custom application, augur.js is the recommended way to do so.  The easiest way to install augur.js is using [npm](https://www.npmjs.com/package/augur.js):

`$ npm install augur.js`

To use the Augur API, augur.js must connect to an Ethereum node, which can be either remote (hosted) or local.  To specify the connection endpoint, pass your RPC connection info to `augur.connect`.

<aside class="notice"><code>augur.connect</code> also accepts a second argument specifying the path to geth's IPC (inter-process communication) file.  IPC creates a persistent connection using a Unix domain socket (or a named pipe on Windows).  While it is significantly faster than HTTP RPC, it cannot be used from the browser.</aside>

Market creation
---------------
```javascript
var marketID = "0x34e104a15ab3eb2a0c26f1138c278416424ef7f8425799a76127e9b427fac74d";
var params = {
  market: marketID,
  liquidity: 9012,
  startingQuantity: 501,
  bestStartingQuantity: 500,
  priceWidth: "0.08697536855694898",
  initialFairPrices: [ "0.661837082683109", "0.5132635487227519" ]
};
augur.create.generateOrderBook(params, {
  onBuyCompleteSets: function (res) { /* ... */ },
  onSetupOutcome: function (outcome) { /* ... */ },
  onSetupOrder: function (order) { /* ... */ },
  onSuccess: function (orderBook) { /* ... */ },
  onFailed: function (err) { /* ... */ }
});

params.isSimulationOnly = true;
augur.create.generateOrderBook(params, {
  onSimulate: function (simulation) { /* ... */ }
})
// example:
simulation = {
  shares: "4007",
  numBuyOrders: [ 10, 7 ],
  numSellOrders: [ 4, 7 ],
  buyPrices:
   [ [ "0.61834939840463451",
       "0.55853753243153362626",
       "0.49872566645843274252",
       "0.43891380048533185878",
       "0.37910193451223097504",
       "0.3192900685391300913",
       "0.25947820256602920756",
       "0.19966633659292832382",
       "0.13985447061982744008",
       "0.08004260464672655634" ],
     [ "0.46977586444427741",
       "0.40996399847117652626",
       "0.35015213249807564252",
       "0.29034026652497475878",
       "0.23052840055187387504",
       "0.1707165345787729913",
       "0.11090466860567210756" ] ],
  sellPrices:
   [ [ "0.70532476696158349",
       "0.76513663293468437374",
       "0.82494849890778525748",
       "0.88476036488088614122" ],
     [ "0.55675123300122639",
       "0.61656309897432727374",
       "0.67637496494742815748",
       "0.73618683092052904122",
       "0.79599869689362992496",
       "0.8558105628667308087",
       "0.91562242883983169244" ] ],
  numTransactions: 34,
  priceDepth: "0.05981186597310088374"
}
```
`augur.create.generateOrderBook` is a convenience method for generating an initial order book for a newly created market. `generateOrderBook` calculates the number of orders to create, as well as the spacing between orders, using the `calculatePriceDepth` method.

#### generateOrderBook(params, callbacks)

`params` is an object containing the input parameters (see example code).  `liquidity` is the total amount of liquidity to add to the market.  `initialFairPrice` is the center of the bid-ask spread. `startingQuantity` is the number of shares available at each price point. `bestStartingQuantity` can optionally be specified separately, and is the number of shares available at the best price point (those closest to the spread). `priceWidth` is the price difference between the best bid and best ask orders.

`calculatePriceDepth` accepts arguments `liquidity`, `startingQuantity`, `bestStartingQuantity`, `halfPriceWidth`, `minValue`, and `maxValue`.  All arguments to `calculatePriceDepth` are expected to be in `BigNumber` format.  The order book generated by `augur.create.generateOrderBook` is "flat", in the sense that there is the same size order created at each price point (except for the best bid/offer).  A typical generated order book (for a single outcome) might look like:

<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="1172px" version="1.1" viewBox="0 0 1172 793" style="max-width:100%;max-height:793px;"><defs><linearGradient x1="0%" y1="0%" x2="100%" y2="0%" id="mx-gradient-dae8fc-1-7ea6e0-1-e-0"><stop offset="0%" style="stop-color:#DAE8FC"/><stop offset="100%" style="stop-color:#7EA6E0"/></linearGradient><linearGradient x1="0%" y1="0%" x2="100%" y2="0%" id="mx-gradient-f8cecc-1-ea6b66-1-e-0"><stop offset="0%" style="stop-color:#F8CECC"/><stop offset="100%" style="stop-color:#EA6B66"/></linearGradient></defs><g transform="translate(0.5,0.5)"><g transform="translate(533.5,508.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="95" height="19" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; font-weight: bold; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><span style="font-size: 18px ; line-height: 21.6px"><font color="#9673a6">price width</font></span></div></div></foreignObject><text x="48" y="16" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica" font-weight="bold">[Not supported by viewer]</text></switch></g><g transform="translate(1073.5,731.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="84" height="40" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; font-weight: bold; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><div><font style="font-size: 18px" color="#0066cc">maximum</font></div><div><font style="font-size: 18px" color="#0066cc">price</font></div></div></div></foreignObject><text x="42" y="26" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica" font-weight="bold">[Not supported by viewer]</text></switch></g><rect x="1053" y="154" width="83" height="570" fill="url(#mx-gradient-dae8fc-1-7ea6e0-1-e-0)" stroke="#6c8ebf" pointer-events="none"/><rect x="201" y="154" width="80" height="570" fill="url(#mx-gradient-f8cecc-1-ea6b66-1-e-0)" stroke="#b85450" pointer-events="none"/><rect x="121" y="154" width="80" height="570" fill="url(#mx-gradient-f8cecc-1-ea6b66-1-e-0)" stroke="#b85450" pointer-events="none"/><rect x="281" y="154" width="80" height="570" fill="url(#mx-gradient-f8cecc-1-ea6b66-1-e-0)" stroke="#b85450" pointer-events="none"/><rect x="361" y="154" width="80" height="570" fill="url(#mx-gradient-f8cecc-1-ea6b66-1-e-0)" stroke="#b85450" pointer-events="none"/><rect x="441" y="354" width="80" height="370" fill="url(#mx-gradient-f8cecc-1-ea6b66-1-e-0)" stroke="#b85450" pointer-events="none"/><rect x="970" y="154" width="83" height="570" fill="url(#mx-gradient-dae8fc-1-7ea6e0-1-e-0)" stroke="#6c8ebf" pointer-events="none"/><rect x="887" y="154" width="83" height="570" fill="url(#mx-gradient-dae8fc-1-7ea6e0-1-e-0)" stroke="#6c8ebf" pointer-events="none"/><rect x="804" y="154" width="83" height="570" fill="url(#mx-gradient-dae8fc-1-7ea6e0-1-e-0)" stroke="#6c8ebf" pointer-events="none"/><rect x="721" y="154" width="83" height="570" fill="url(#mx-gradient-dae8fc-1-7ea6e0-1-e-0)" stroke="#6c8ebf" pointer-events="none"/><path d="M 101 724 L 101 62.24" fill="none" stroke="#000000" stroke-width="2" stroke-miterlimit="10" pointer-events="none"/><path d="M 101 56.24 L 105 64.24 L 101 62.24 L 97 64.24 Z" fill="#000000" stroke="#000000" stroke-width="2" stroke-miterlimit="10" pointer-events="none"/><rect x="641" y="354" width="80" height="370" fill="url(#mx-gradient-dae8fc-1-7ea6e0-1-e-0)" stroke="#6c8ebf" pointer-events="none"/><path d="M 101 724 L 1152.76 724" fill="none" stroke="#000000" stroke-width="2" stroke-miterlimit="10" pointer-events="none"/><path d="M 1158.76 724 L 1150.76 728 L 1152.76 724 L 1150.76 720 Z" fill="#000000" stroke="#000000" stroke-width="2" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(861.5,741.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="57" height="26" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><font style="font-size: 24px"><b>price</b></font></div></div></foreignObject><text x="29" y="19" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><g transform="translate(22.5,2.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="113" height="47" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><div><font style="font-size: 24px"><b>order size</b></font></div><font style="font-size: 18px"><b>(# shares)</b></font></div></div></foreignObject><text x="57" y="30" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica">[Not supported by viewer]</text></switch></g><g transform="translate(20.5,330.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="70" height="61" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; font-weight: bold; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><font style="font-size: 18px" color="#009999">best</font><div><font style="font-size: 18px" color="#009999">starting</font><div><font style="font-size: 18px" color="#009999">quantity</font></div></div></div></div></foreignObject><text x="35" y="37" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica" font-weight="bold">[Not supported by viewer]</text></switch></g><g transform="translate(19.5,131.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="70" height="40" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 102); line-height: 1.2; vertical-align: top; white-space: nowrap; font-weight: bold; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><font style="font-size: 18px" color="#000099">starting</font><div><span style="font-size: 18px ; line-height: 1.2"><font color="#000099">quantity</font></span></div></div></div></foreignObject><text x="35" y="26" fill="#000066" text-anchor="middle" font-size="12px" font-family="Helvetica" font-weight="bold">[Not supported by viewer]</text></switch></g><g transform="translate(540.5,739.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="80" height="40" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; font-weight: bold; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><font color="#00cc00" style="font-size: 18px">initial fair</font><div><font color="#00cc00" style="font-size: 18px">price</font></div></div></div></foreignObject><text x="40" y="26" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica" font-weight="bold">[Not supported by viewer]</text></switch></g><path d="M 580 724 L 580 51" fill="none" stroke="#00cc00" stroke-width="5" stroke-miterlimit="10" stroke-dasharray="15 15" pointer-events="none"/><path d="M 537.92 538.67 L 624.74 538.67" fill="none" stroke="#9673a6" stroke-width="5" stroke-miterlimit="10" pointer-events="none"/><path d="M 526.92 538.67 L 537.92 533.17 L 537.92 544.17 Z" fill="#9673a6" stroke="#9673a6" stroke-width="5" stroke-miterlimit="10" pointer-events="none"/><path d="M 635.74 538.67 L 624.74 544.17 L 624.74 533.17 Z" fill="#9673a6" stroke="#9673a6" stroke-width="5" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(64.5,728.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="80" height="40" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; font-weight: bold; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><font style="font-size: 18px" color="#cc0000">minimum</font><div><font style="font-size: 18px" color="#cc0000">price</font></div></div></div></foreignObject><text x="40" y="26" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica" font-weight="bold">[Not supported by viewer]</text></switch></g><path d="M 337.92 751.26 L 384.41 751.07" fill="none" stroke="#994c00" stroke-width="5" stroke-miterlimit="10" pointer-events="none"/><path d="M 326.92 751.31 L 337.9 745.76 L 337.95 756.76 Z" fill="#994c00" stroke="#994c00" stroke-width="5" stroke-miterlimit="10" pointer-events="none"/><path d="M 395.41 751.02 L 384.43 756.57 L 384.39 745.57 Z" fill="#994c00" stroke="#994c00" stroke-width="5" stroke-miterlimit="10" pointer-events="none"/><g transform="translate(312.5,763.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="97" height="19" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; font-weight: bold; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><span style="font-size: 18px ; line-height: 21.6px"><font color="#994c00">price depth</font></span></div></div></foreignObject><text x="49" y="16" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica" font-weight="bold">[Not supported by viewer]</text></switch></g><path d="M 401 729 L 401 734 Q 401 739 401 749 L 401 759" fill="none" stroke="#994c00" stroke-width="2" stroke-miterlimit="10" pointer-events="none"/><path d="M 321 759 L 321 729" fill="none" stroke="#994c00" stroke-width="2" stroke-miterlimit="10" pointer-events="none"/><path d="M 1151 155 L 91 155" fill="none" stroke="#000099" stroke-width="3" stroke-miterlimit="10" stroke-dasharray="9 9" pointer-events="none"/><path d="M 1151 354 L 91 354" fill="none" stroke="#009999" stroke-width="3" stroke-miterlimit="10" stroke-dasharray="9 9" pointer-events="none"/><path d="M 346 -97 L 323.5 -97 L 323.5 104 L 301 104 L 323.5 104 L 323.5 305 L 346 305" fill="none" stroke="#b85450" stroke-width="3" stroke-miterlimit="10" transform="rotate(90,323.5,104)" pointer-events="none"/><g transform="translate(790.5,49.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="197" height="24" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; font-weight: bold; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><span style="line-height: 21.6px"><font style="font-size: 24px" color="#0066cc">asks (sell orders)</font></span></div></div></foreignObject><text x="99" y="18" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica" font-weight="bold">[Not supported by viewer]</text></switch></g><path d="M 911 -143 L 888.5 -143 L 888.5 104 L 866 104 L 888.5 104 L 888.5 351 L 911 351" fill="none" stroke="#0066cc" stroke-width="3" stroke-miterlimit="10" transform="rotate(90,888.5,104)" pointer-events="none"/><g transform="translate(222.5,49.5)"><switch><foreignObject style="overflow:visible;" pointer-events="all" width="196" height="24" requiredFeatures="http://www.w3.org/TR/SVG11/feature#Extensibility"><div xmlns="http://www.w3.org/1999/xhtml" style="display: inline-block; font-size: 12px; font-family: Helvetica; color: rgb(0, 0, 0); line-height: 1.2; vertical-align: top; white-space: nowrap; font-weight: bold; text-align: center;"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;text-align:inherit;text-decoration:inherit;"><span style="line-height: 21.6px"><font style="font-size: 24px" color="#b85450">bids (buy orders)</font></span></div></div></foreignObject><text x="98" y="18" fill="#000000" text-anchor="middle" font-size="12px" font-family="Helvetica" font-weight="bold">[Not supported by viewer]</text></switch></g></g></svg>

This calculation is done as follows.  Let \\(p_\mathrm{max}\\) and \\(p_\mathrm{min}\\) be the maximum and minimum allowed prices, \\(I\\) be the initial fair price for the outcome under consideration, \\(q\\) be the starting quantity (size of each order), \\(q_0\\) be the size of the best order, and \\(w\\) be the price width (the size of the bid-ask spread).  The liquidity available on the buy and sell order books is given by:

<p>
$$
L_{\mathrm{buy}} = q_0 + \frac{q}{\delta} \left( p_\mathrm{min} + I - \frac{w}{2} \right)
$$
</p>
<p>
$$
L_{\mathrm{sell}} = q_0 + \frac{q}{\delta} \left( p_\mathrm{max} - I - \frac{w}{2} \right)
$$
</p>

where \\(\delta\\) is the "price depth", the distance between orders on the same side of the book.  The total liquidity \\(L = L_{\mathrm{buy}} + L_{\mathrm{sell}}\\) is an input parameter, so the only unknown is the price depth \\(\delta\\), which can now be calculated:

<p>
$$
\delta = \frac{\left( p_\mathrm{min} + p_\mathrm{max} - w \right) q}{L - 2q_0}
$$
</p>

The initial order book is now fully specified.

`augur.create.generateOrderBook` also accepts an optional parameter, `isSimulationOnly`, which if set to `true`, returns information about what the method will do (see code example).  `shares` is the number of complete sets of shares to purchased, `numBuyOrders` is the number of buy orders that will be created, `numSellOrders` is the number of sell orders that will be created, and `numTransactions` is the total number of Ethereum transactions needed to set up the order book.

Trading
-------

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

Reporting
---------

<a href="images/reporting_cycle.svg"><img src="images/reporting_cycle.svg" onerror="this.src='images/reporting_cycle.png'"></a>

This diagram shows the Reporting cycle for an event (and its associated market) which was created during some previous cycle (off the diagram to the left) and which expires sometime during the cycle marked as "Reporting cycle 1".  Each cycle takes 60 days to complete.  Including the steps needed to fully complete all payouts and Reputation redistribution, resolving an event takes three full Reporting cycles.

<aside class="notice">In Augur, the terms "Reporting period" and "Reporting cycle" are used interchangeably throughout the codebase.</aside>

Initial market loading
----------------------
```python
# Each market's ID is a hash of the following information:
marketinfo = string(8*32 + len(description))
marketinfo[0] = tradingPeriod
marketinfo[1] = tradingFee
marketinfo[2] = block.timestamp # market creation timestamp
marketinfo[3] = tag1
marketinfo[4] = tag2
marketinfo[5] = tag3
marketinfo[6] = expirationDate
marketinfo[7] = len(description)
mcopy(marketinfo + 8*32, description, chars=len(description))
marketID = sha3(marketinfo, chars=len(marketinfo))

# Then for the next market, do sha3 of its marketID plus the previous:
# (marketID2 is the same calculation as above, for the second market)
compositeMarketsHash = sha3(marketID + marketID2)

# Then continue this process until you've included all markets in the
# composite hash; for example, if there were 4 total markets:
compositeMarketsHash = sha3(compositeMarketsHash + marketID3)
compositeMarketsHash = sha3(compositeMarketsHash + marketID4)
```

To load the basic market info for all markets, first call `augur.getMarketsInfo({branch, offset, numMarketsToLoad, callback})`.  You will likely need to chunk the results so that the request does not time out.  More detailed market info (including prices) for each market in each chunk (page) is then loaded using `augur.getMarketInfo(marketID)`.  `getMarketInfo` does not return the full order book; to get the order book for a market, call `augur.getOrderBook(marketID)`.

<aside class="notice">Cache nodes regularly call <code>augur.getMarketsInfo({branch, offset, numMarketsToLoad, callback})</code>.  The first time <code>getMarketsInfo</code> is called, all markets should be loaded.  Subsequent <code>getMarketsInfo</code> calls should only load markets created since the previous call.</aside>

Reporting outcomes
------------------

### Yes or no (binary)
- no: `2**64`
- yes: `2*2**64`
- indeterminate: `3*2**63`
- exactly in the middle but not indeterminate: `2**63 + 1`

### Multiple choice (categorical)

- min: `1`
- max: `2**64`
- indeterminate: `2**63`
- exactly in the middle but not indeterminate: `2**63 + 1`

### Numerical (scalar)
- min: `1`
- max: `2**64`
- indeterminate: `2**63`
- exactly in the middle but not indeterminate: `2**63 + 1`

Simplified API
--------------
```javascript
var branchId = 1010101;
var marketId = "0x45c545745a80121b14c879bf9542dd838559f7acc90f1e1774f4268c332a519";

augur.trading.orderBook.getOrderBook(marketId, function (orderBook) { /* ... */ });
// example output:
{ sell:
   [ { id: '-0xc764b37f2eebb389040025b33668470bf017ce00e3d7c3725d2c33da04cc0bc5',
       type: 'sell',
       market: '0x45c545745a80121b14c879bf9542dd838559f7acc90f1e1774f4268c332a519',
       amount: '0.25',
       price: '0.51999999999999999998',
       owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
       block: 8457,
       outcome: '1' },
     { id: '-0x66f53e64d4547120bd1962168f4acedc58d93efb63e62ad675ef55d19743e66c',
       type: 'sell',
       market: '0x45c545745a80121b14c879bf9542dd838559f7acc90f1e1774f4268c332a519',
       amount: '1',
       price: '0.5',
       owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
       block: 8452,
       outcome: '1' },
     { id: '-0x6d354a545d040ee85ef3b59f96cf20227f7f9385c510c87a5b43970043e36ed5',
       type: 'sell',
       market: '0x45c545745a80121b14c879bf9542dd838559f7acc90f1e1774f4268c332a519',
       amount: '0.25',
       price: '0.51999999999999999998',
       owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
       block: 8379,
       outcome: '1' },
     { id: '-0x7f79fc1e36e703f9a0f7b8776d9a1899d6e9ce121f68b3bce7161b9f394b942e',
       type: 'sell',
       market: '0x45c545745a80121b14c879bf9542dd838559f7acc90f1e1774f4268c332a519',
       amount: '1',
       price: '0.5',
       owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
       block: 8375,
       outcome: '1' } ],
  buy:
   [ { id: '-0x753a368b0cd164302041448c61c57a868dccf8fceb538fb2e0a60356f793d720',
       type: 'buy',
       market: '0x45c545745a80121b14c879bf9542dd838559f7acc90f1e1774f4268c332a519',
       amount: '0.25',
       price: '0.51999999999999999998',
       owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
       block: 8448,
       outcome: '1' },
     { id: '-0x9bae4959ded1d5426da64870b39a1465a39491d47e5071c6e4889d739c994c0d',
       type: 'buy',
       market: '0x45c545745a80121b14c879bf9542dd838559f7acc90f1e1774f4268c332a519',
       amount: '1',
       price: '0.5',
       owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
       block: 8446,
       outcome: '1' },
     { id: '-0xadd0854ba0ebba7ac915929988cb01e7b2ca8b6cca9cf9f88b235d654ce6666c',
       type: 'buy',
       market: '0x45c545745a80121b14c879bf9542dd838559f7acc90f1e1774f4268c332a519',
       amount: '0.25',
       price: '0.51999999999999999998',
       owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
       block: 8391,
       outcome: '1' },
     { id: '-0x1116dd2bd3aa1db9d14591c42f8eaac9a6a57ea2aad5654c5ada3245f7a52a7b',
       type: 'buy',
       market: '0x45c545745a80121b14c879bf9542dd838559f7acc90f1e1774f4268c332a519',
       amount: '1',
       price: '0.5',
       owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
       block: 8389,
       outcome: '1' } ] }

augur.markets.getMarketInfo(marketId, function (marketInfo) { /* ... */ })
// example output:
{ network: '2',
  traderCount: 2,
  traderIndex: 5e-20,
  numOutcomes: 2,
  tradingPeriod: 107304960000,
  makerFee: '0.01246155619866478799995356402249700352',
  takerFee: '0.07910419330000878000004643597750299648',
  tradingFee: '0.061043832999115712',
  branchId: '0xf69b5',
  numEvents: 1,
  cumulativeScale: '14999',
  creationTime: 1463784466,
  volume: '1.25',
  creationFee: '5.14285714285714266327',
  author: '0x7c0d52faab596c08f484e3478aebc6205f3f5d8c',
  tags: [ 'spaceflight', 'LEO', 'economics' ],
  type: 'scalar',
  endDate: 1609574400000,
  winningOutcomes: [ '0', '0', '0', '0', '0', '0', '0', '0' ],
  description: 'How much will it cost (in USD) to move a pound of inert cargo from Earth\'s surface to Low Earth Orbit by January 1, 2021?',
  outcomes:
   [ { shares: {},
       id: 1,
       outstandingShares: '0.5',
       price: '0.51999999999999999998' },
     { shares: {}, id: 2, outstandingShares: '0.5', price: '0' } ],
  events:
   [ { id: '-0x9d869b3088e7c963e21d295bec13b57342aa70510252fa97388ded77df03d483',
       endDate: 1609574400000,
       outcome: '0',
       minValue: '1',
       maxValue: '15000',
       numOutcomes: 2,
       type: 'scalar' } ],
  _id: '0x45c545745a80121b14c879bf9542dd838559f7acc90f1e1774f4268c332a519' }
```
All of the methods in the Simplified API are getter methods that use an `eth_call` RPC request; for transactional requests (`eth_sendTransaction`), see the Full API section below.  This API is simplified in the sense that single requests to this API can be used to fetch a large amount of data, without the need for complicated RPC batch queries.

### augur.trading.orderBook.getOrderBook(marketId[, callback])

Retrieves the full order book for `marketId`.  The order book's format is an object with fields `buy` and `sell` containing arrays of buy and sell orders.  The structure of the orders is shown in the example code.

### augur.markets.getMarketInfo(marketId[, callback])

Reads all information about a market that is stored on-contract.  It also determines the `type` of the market, which can be `binary` (two outcomes; i.e., Yes or No), `categorical` (more than two outcomes, i.e., Multiple Choice), `scalar` (answer can be any of a range of values; i.e., Numerical), or `combinatorial` (for combined wagers on multiple events).  If the market is a combinatorial market, `getMarketInfo` also makes separate RPC request for the individual event descriptions.

```javascript
var marketIDs = [
  '0xf41e2f1827142a95cc14b5333f3d3493588342ef8bc9214e96e0c894dff27fc5',
  '0x9b8e45cdf9d35ab66b939d5eb5b48bf10de3c39b7f6fa2d38fe518a869502e8'
];
augur.markets.batchGetMarketInfo(marketIDs, function (info) { /* ... */ });
// example output:
info = {
  "0xf41e2f1827142a95cc14b5333f3d3493588342ef8bc9214e96e0c894dff27fc5": {
    "network": "2",
    "traderCount": 0,
    "makerFee": "0.01246155619866478799995356402249700352",
    "takerFee": "0.07910419330000878000004643597750299648",
    "tradingFee": "0.061043832999115712",
    "traderIndex": 0,
    "numOutcomes": 3,
    "tradingPeriod": 206104,
    "branchId": "0xf69b5",
    "numEvents": 1,
    "cumulativeScale": "1",
    "creationTime": 1464642365,
    "volume": "0",
    "creationFee": "8.99999999999999967469",
    "author": "0x7c0d52faab596c08f484e3478aebc6205f3f5d8c",
    "tags": ["weather", "temperature", "climate change"],
    "type": "categorical",
    "endDate": 1483948800,
    "winningOutcomes": ["0", "0", "0", "0", "0", "0", "0", "0"],
    "description": "Will the average temperature on Earth in 2016 be Higher, Lower, or Unchanged from the average temperature on Earth in 2015? Choices: Higher, Lower, Unchanged",
    "outcomes": [
      {
        "shares": {},
        "id": 1,
        "outstandingShares": "0",
        "price": "0"
      },
      {
        "shares": {},
        "id": 2,
        "outstandingShares": "0",
        "price": "0"
      },
      {
        "shares": {},
        "id": 3,
        "outstandingShares": "0",
        "price": "0"
      }
    ],
    "events": [
      {
        "id": "0x808bd49d2a16214bed80a6249302b55a87282a7d6ecc74a0381b7453b1ed9101",
        "endDate": 1483948800,
        "outcome": "0",
        "minValue": "1",
        "maxValue": "2",
        "numOutcomes": 3,
        "type": "categorical"
      }
    ],
    "_id": "0xf41e2f1827142a95cc14b5333f3d3493588342ef8bc9214e96e0c894dff27fc5",
    "sortOrder": 0
  },
  "0x9b8e45cdf9d35ab66b939d5eb5b48bf10de3c39b7f6fa2d38fe518a869502e8": {
    "network": "2",
    "traderCount": 0,
    "makerFee": "0.01246155619866478799995356402249700352",
    "takerFee": "0.07910419330000878000004643597750299648",
    "tradingFee": "0.061043832999115712",
    "traderIndex": 0,
    "numOutcomes": 3,
    "tradingPeriod": 206104,
    "branchId": "0xf69b5",
    "numEvents": 1,
    "cumulativeScale": "1",
    "creationTime": 1464642450,
    "volume": "0",
    "creationFee": "8.99999999999999967469",
    "author": "0x7c0d52faab596c08f484e3478aebc6205f3f5d8c",
    "tags": ["quotes", "严肃", "蝙蝠侠"],
    "type": "categorical",
    "endDate": 1483948800,
    "winningOutcomes": ["0", "0", "0", "0", "0", "0", "0", "0"],
    "description": "为什么有这么严重吗？",
    "outcomes": [
      {
        "shares": {},
        "id": 1,
        "outstandingShares": "0",
        "price": "0"
      },
      {
        "shares": {},
        "id": 2,
        "outstandingShares": "0",
        "price": "0"
      },
      {
        "shares": {},
        "id": 3,
        "outstandingShares": "0",
        "price": "0"
      }
    ],
    "events": [
      {
        "id": "0xe2b0453641b305c4aa96b3bd473d93b0b5062a7c0fc62d6e158c133859fcdcb3",
        "endDate": 1483948800,
        "outcome": "0",
        "minValue": "1",
        "maxValue": "2",
        "numOutcomes": 3,
        "type": "categorical"
      }
    ],
    "_id": "0x9b8e45cdf9d35ab66b939d5eb5b48bf10de3c39b7f6fa2d38fe518a869502e8",
    "sortOrder": 1
  }
}

var options = {
  branch: 1010101,     // branch ID (default: 1010101)
  offset: 10,          // which markets to start  (default: 0)
  numMarketsToLoad: 2  // numMarkets
};
augur.markets.getMarketsInfo(options, function (marketsInfo) { /* ... */ })
// example output:
{ '0xf41e2f1827142a95cc14b5333f3d3493588342ef8bc9214e96e0c894dff27fc5':
   { _id: '0xf41e2f1827142a95cc14b5333f3d3493588342ef8bc9214e96e0c894dff27fc5',
     sortOrder: 0,
     tradingPeriod: 206104,
     tradingFee: '0.01999999999999999998',
     creationTime: 1464642365,
     volume: '0',
     tags: [ 'weather', 'temperature', 'climate change' ],
     endDate: 1483948800,
     description: 'Will the average temperature on Earth in 2016 be Higher, Lower, or Unchanged from the average temperature on Earth in 2015? Choices: Higher, Lower, Unchanged' },
  '0x9b8e45cdf9d35ab66b939d5eb5b48bf10de3c39b7f6fa2d38fe518a869502e8':
   { _id: '0x9b8e45cdf9d35ab66b939d5eb5b48bf10de3c39b7f6fa2d38fe518a869502e8',
     sortOrder: 1,
     tradingPeriod: 206104,
     tradingFee: '0.01999999999999999998',
     creationTime: 1464642450,
     volume: '0',
     tags: [ 'quotes', '严肃', '蝙蝠侠' ],
     endDate: 1483948800,
     description: '为什么有这么严重吗？' } }
```
### augur.markets.batchGetMarketInfo(marketIDs[, callback])

Retrieve a `marketInfo` object for the market IDs in array `marketIDs`.  The `marketInfo` objects (see above for example) are collected into a single object and indexed by market ID.

### augur.markets.getMarketsInfo(options[, callback])

Gets basic info about markets the specified branch, and returns an object with market info indexed by market ID.  The `options` parameter is an object which specifies the branch ID (`branch`).  There are also two fields (`offset` and `numMarketsToLoad`) used to split up the `getMarketsInfo` query into multiple requests.  This is useful if the number of markets on the branch is too large for a single RPC request (which is typical).

<aside class="notice">Each branch's market IDs are stored as an "array" on the <a href="https://github.com/AugurProject/augur-core/blob/master/src/data_api/branches.se">branches</a> contract, in the contract's <code>Branches[](markets[], numMarkets, ...)</code> data.  Markets are indexed in the order created; i.e., the first market created has index 0, the second 1, etc.  This ordering allows us to break up a large aggregate request like <code>getMarketsInfo</code> into manageable chunks.

For example, suppose you were displaying markets on separate pages.  You might want to retrieve information about all markets, but, to keep your loading time reasonable, only get 5 markets per request.  To get the first 5 markets, you would set <code>offset</code> to 0 and <code>numMarketsToLoad</code> to 5: <code>augur.markets.getMarketsInfo({offset: 0, numMarketsToLoad: 5}, cb)</code>.  To get the second 5, <code>offset</code> would be 5: <code>augur.markets.getMarketsInfo({offset: 5, numMarketsToLoad: 5}, cb)</code>.  The third 5, <code>offset</code> would be 10: <code>augur.markets.getMarketsInfo({offset: 10, numMarketsToLoad: 5}, cb)</code>, and so on.</aside>

Call API
--------
Augur's Call API is made up of "getter" methods that retrieve information from the blockchain (using Ethereum's `eth_call` RPC) but does not write information to the blockchain.

```javascript
// Branch contract
const branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5";
const parentPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";

augur.api().Branch.getChildBranch({ branch, parentPayoutDistributionHash }, function (childBranch) { /* ... */ })
// example output:
childBranch = "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c"

augur.api().Branch.getCurrentReportingWindow({ branch }, function (currReportingWindow) { /* ... */ })
// example output:
currReportingWindow = "578"

augur.api().Branch.getForkEndTime({ branch }, function (forkEndTime) { /* ... */ })
// example output:
forkEndTime = "1489855429";

augur.api().Branch.getForkingMarket({ branch }, function (forkingMarket) { /* ... */ })
// example output:
forkingMarket = "0x78f7b43150d27c464359e735781c16ac585f52a8";

augur.api().Branch.getNextReportingWindow({ branch }, function (nextReportingWindow) { /* ... */ })
// example output:
nextReportingWindow = "579"

augur.api().Branch.getParentBranch({ branch }, function (parentBranch) { /* ... */ })
// example output:
parentBranch = "0x63c59544b89cce1dd53b1b566862189b25adec41"

augur.api().Branch.getParentPayoutDistributionHash({ branch }, function (branchParentPayoutDistributionHash) { /* ... */ })
// example output:
branchParentPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api().Branch.getPreviousReportingWindow({ branch }, function (previousReportingWindow) { /* ... */ })
// example output:
previousReportingWindow = "577"

augur.api().Branch.getReportingPeriodDurationInSeconds({ branch }, function (reportingPeriodDuration) { /* ... */ })
// example output:
reportingPeriodDuration = "2592000";

const endTime = 2524608000;
augur.api().Branch.getReportingWindowByMarketEndTime({ branch, endTime, hasAutomatedReporter: 0 }, function (reportingWindowByEndTime) { /* ... */ })
// example output:
reportingWindowByEndTime = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api().Branch.getReportingWindowByTimestamp({ branch, timestamp: endTime }, function (reportingWindowByTimestamp) { /* ... */ })
// example output:
reportingWindowByTimestamp = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api().Branch.getReportingWindowId({ branch, timestamp: new Date().getTime() }, function (reportingWindowId) { /* ... */ })
// example output:
reportingWindowId = "578";

augur.api().Branch.getReputationToken({ branch }, function (reputationTokenAddress) { /* ... */ })
// example output:
reputationTokenAddress = "0x2fb561b2bdbcd1ae1995bdd6aff6776d6f4292f2";

augur.api().Branch.getTopics({ branch }, function (topicsAddress) { /* ... */ })
// example output:
topicsAddress = "0x14f094c79a676c681e7cc490e775f73072e535ae";

const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api().Branch.isContainerForMarket({ branch, shadyMarket: market }, function (isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1";

const registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
augur.api().Branch.isContainerForRegistrationToken({ branch, shadyRegistrationToken: registrationToken }, function (isContainerForRegistrationToken) { /* ... */ })
// example output:
isContainerForRegistrationToken = "1";

const reportingToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
augur.api().Branch.isContainerForReportingToken({ branch, shadyReportingToken: reportingToken }, function (isContainerForReportingToken) { /* ... */ })
// example output:
isContainerForReportingToken = "1";

const reportingWindow = "578";
augur.api().Branch.isContainerForReportingWindow({ branch, shadyReportingWindow: reportingWindow }, function (isContainerForReportingWindow) { /* ... */ })
// example output:
isContainerForReportingWindow = "1";

const childBranch = "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c";
augur.api().Branch.isParentOf({ branch, shadyChild: childBranch }, function (isParentOf) { /* ... */ })
// example output:
isParentOf = "1";
```
### [Branch Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/branch.se)

#### augur.api().Branch.getChildBranch({ branch, parentPayoutDistributionHash }[, callback])

Returns the child branch address of the specified `branch` address related to the `parentPayoutDistributionHash` provided.

#### augur.api().Branch.getCurrentReportingWindow({ branch }[, callback])

Returns the current reporting window ID for the specified `branch`.

#### augur.api().Branch.getForkEndTime({ branch }[, callback])

Returns the timestamp for when the fork ends given a specified `branch`.

#### augur.api().Branch.getForkingMarket({ branch }[, callback])

Returns the address of the market that the specified `branch` is forking over.

#### augur.api().Branch.getNextReportingWindow({ branch }[, callback])

Returns the next reporting window ID for a specific `branch`.

#### augur.api().Branch.getParentBranch({ branch }[, callback])

Returns the parent branch address of the specified `branch`.

#### augur.api().Branch.getParentPayoutDistributionHash({ branch }[, callback])

Returns the parent branch's payout distribution hash given a `branch` address.

#### augur.api().Branch.getPreviousReportingWindow({ branch }[, callback])

Returns the previous reporting window ID for the specified `branch`.

#### augur.api().Branch.getReportingPeriodDurationInSeconds({ branch }[, callback])

Returns the specified `branch`'s reporting period duration in seconds.

#### augur.api().Branch.getReportingWindowByMarketEndTime({ branch, endTime, hasAutomatedReporter }[, callback])

Returns the reporting window address on the specific `branch` given an `endTime` and if the market we are checking for has an automated reporter or not (`hasAutomatedReporter`). `hasAutomatedReporter` should be `0` for markets without an automated reporter, `1` for markets with an automated reporter.

#### augur.api().Branch.getReportingWindowByTimestamp({ branch, timestamp }[, callback])

Returns the reporting window address for a specific `branch` and provided `timestamp`.

#### augur.api().Branch.getReportingWindowId({ branch, timestamp }[, callback])

Returns the reporting window ID for a specific `branch` and provided `timestamp`. This is calculated by dividing the timestamp by the branch's reporting period duration in seconds.

#### augur.api().Branch.getReputationToken({ branch }[, callback])

Returns the reputation token address for a specific `branch`.

#### augur.api().Branch.getTopics({ branch }[, callback])

Returns the topics address for the specific `branch`.

#### augur.api().Branch.isContainerForMarket({ branch, shadyMarket }[, callback])

Returns wether the specific `branch` is a container for the `shadyMarket` address provided. Returns `1` if true, `0` if false.

#### augur.api().Branch.isContainerForRegistrationToken({ branch, shadyRegistrationToken }[, callback])

Returns wether the specific `branch` is a container for the `shadyRegistrationToken` address provided. Returns `1` if true, `0` if false.

#### augur.api().Branch.isContainerForReportingToken({ branch, shadyReportingToken }[, callback])

Returns wether the specific `branch` is a container for the `shadyReportingToken` address provided. Returns `1` if true, `0` if false.

#### augur.api().Branch.isContainerForReportingWindow({ branch, shadyReportingWindow }[, callback])

Returns wether the specific `branch` is a container for the `shadyReportingWindow` provided. Returns `1` if true, `0` if false.

#### augur.api().Branch.isParentOf({ branch, shadyChild }[, callback])

Returns wether the specific `branch` is a container for the `shadyChild` branch address provided. Returns `1` if true, `0` if false.


```javascript
// Market Contract
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api().Market.canBeReportedOn({ market }, function (canBeReportedOn) { /* ... */ })
// example output:
canBeReportedOn = "1"

augur.api().Market.getAutomatedReportDisputeDueTimestamp({ market }, function (automatedReporterDisputeDueTimestamp) { /* ... */ })
// example output:
automatedReporterDisputeDueTimestamp = "1500907130"

augur.api().Market.getAutomatedReportDueTimestamp({ market }, function (automatedReporterDueTimestamp) { /* ... */ })
// example output:
automatedReporterDueTimestamp = "1500647930"

augur.api().Market.getBranch({ market }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api().Market.getCompleteSetCostInAttotokens({ market }, function (completeSetCostInAttotokens) { /* ... */ })
// example output:
completeSetCostInAttotokens = "1000000000000000000"

augur.api().Market.getCreator({ market }, function (marketCreator) { /* ... */ })
// example output:
marketCreator = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa"

augur.api().Market.getDenominationToken({ market }, function (denominationToken) { /* ... */ })
// example output:
denominationToken = "0x30e3852448f4ab5d62bbf7544ca3c92daca5c957"

augur.api().Market.getEndTime({ market }, function (endTime) { /* ... */ })
// example output:
endTime = "1500388730";

augur.api().Market.getFinalizationTime({ market }, function (finalizationTime) { /* ... */ })
// example output:
finalizationTime = "1500647930";

augur.api().Market.getFinalPayoutDistributionHash({ market }, function (finalPayoutDistributionHash) { /* ... */ })
// example output:
finalPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api().Market.getFinalWinningReportingToken({ market }, function (winningReportingToken) { /* ... */ })
// example output:
winningReportingToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api().Market.getMarketCreatorSettlementFeeInAttoethPerEth({ market }, function (marketCreatorSettlementFee) { /* ... */ })
// example output:
marketCreatorSettlementFee = "20000000000000000"

augur.api().Market.getMaxDisplayPrice({ market }, function (maxDisplayPrice) { /* ... */ })
// example output:
maxDisplayPrice = "1"

augur.api().Market.getMinDisplayPrice({ market }, function (minDisplayPrice) { /* ... */ })
// example output:
minDisplayPrice = "0"

augur.api().Market.getNumberOfOutcomes({ market }, function (numOutcomes) { /* ... */ })
// example output:
numOutcomes = "2"

augur.api().Market.getPayoutDenominator({ market }, function (payoutDenominator) { /* ... */ })
// example output:
payoutDenominator = "1"

augur.api().Market.getRegistrationToken({ market }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api().Market.getReportingWindow({ market }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api().Market.getReputationToken({ market }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api().Market.getShareToken({ market, outcome: 1 }, function (shareToken) { /* ... */ })
// example output:
shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054"

augur.api().Market.getTentativeWinningPayoutDistributionHash({ market }, function (tentativeWinningPayoutDistributionHash) { /* ... */ })
// example output:
tentativeWinningPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api().Market.getTopic({ market }, function (topic) { /* ... */ })
// example output:
topic = "Augur"

const reportingToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
augur.api().Market.isContainerForReportingToken({ market, shadyToken: reportingToken }, function (isContainerForReportingToken) { /* ... */ })
// example output:
isContainerForReportingToken = "1"

const shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
augur.api().Market.isContainerForShareToken({ market, shadyShareToken: shareToken }, function (isContainerForShareToken) { /* ... */ })
// example output:
isContainerForShareToken = "1"

augur.api().Market.isDoneWithAllReporters({ market }, function (isDoneWithAllReporters) { /* ... */ })
// example output:
isDoneWithAllReporters = "1"

augur.api().Market.isDoneWithAutomatedReporters({ market }, function (isDoneWithAutomatedReporters) { /* ... */ })
// example output:
isDoneWithAutomatedReporters = "1"

augur.api().Market.isDoneWithLimitedReporters({ market }, function (isDoneWithLimitedReporters) { /* ... */ })
// example output:
isDoneWithLimitedReporters = "1"

augur.api().Market.isFinalized({ market }, function (isFinalized) { /* ... */ })
// example output:
isFinalized = "1"

augur.api().Market.isInAllDisputePhase({ market }, function (isInAllDisputePhase) { /* ... */ })
// example output:
isInAllDisputePhase = "1";

augur.api().Market.isInAllReportingPhase({ market }, function (isInAllReportingPhase) { /* ... */ })
// example output:
isInAllReportingPhase = "1";

augur.api().Market.isInAutomatedDisputePhase({ market }, function (isInAutomatedDisputePhase) { /* ... */ })
// example output:
isInAutomatedDisputePhase = "1";

augur.api().Market.isInAutomatedReportingPhase({ market }, function (isInAutomatedReportingPhase) { /* ... */ })
// example output:
isInAutomatedReportingPhase = "1";

augur.api().Market.isInLimitedDisutePhase({ market }, function (isInLimitedDisutePhase) { /* ... */ })
// example output:
isInLimitedDisutePhase = "1";

augur.api().Market.isInLimitedReportingPhase({ market }, function (isInLimitedReportingPhase) { /* ... */ })
// example output:
isInLimitedReportingPhase = "1";

augur.api().Market.needsMigration({ market }, function (needsMigration) { /* ... */ })
// example output:
needsMigration = "1";

augur.api().Market.shouldCollectReportingFees({ market }, function (shouldCollectReportingFees) { /* ... */ })
// example output:
shouldCollectReportingFees = "1";
```
### [Market Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/market.se)

#### augur.api().Market.canBeReportedOn({ market }[, callback])

Returns wether the specific `market` can be reported on. Returns `1` if true, `0` if false.

#### augur.api().Market.getAutomatedReportDisputeDueTimestamp({ market }[, callback])

Returns the timestamp for when a specific `market`'s  automated report process and dispute period should be completed.

#### augur.api().Market.getAutomatedReportDueTimestamp({ market }[, callback])

Returns the timestamp for when a specific `market`'s  automated report process should be completed but doesn't count the dispute period.

#### augur.api().Market.getBranch({ market }[, callback])

Returns the branch ID of the branch that the specified `market` is contained within.

#### augur.api().Market.getCompleteSetCostInAttotokens({ market }[, callback])

Returns the cost of a complete set for a specific `market` denominated in Attotokens.

#### augur.api().Market.getCreator({ market }[, callback])

Returns the address of the creator of the specified `market`.

#### augur.api().Market.getDenominationToken({ market }[, callback])

Returns the address of the token used to denominate the specified `market`.

#### augur.api().Market.getEndTime({ market }[, callback])

Returns the timestamp for when the specified `market`'s event has come to pass and the market is ready to be reported on.

#### augur.api().Market.getFinalizationTime({ market }[, callback])

Returns the timestamp for when the specified `market` was finalized.

#### augur.api().Market.getFinalPayoutDistributionHash({ market }[, callback])

Returns the final payout distribution hash for a specified `market`.

#### augur.api().Market.getFinalWinningReportingToken({ market }[, callback])

Returns the winning reporting token address for a specified `market`.

#### augur.api().Market.getMarketCreatorSettlementFeeInAttoethPerEth({ market }[, callback])

Returns the fee paid to the `market` creator denominated in Attotokens for 1 token settled on the market.

#### augur.api().Market.getMaxDisplayPrice({ market }[, callback])

Returns the maximum displayed price for the specified `market`.

#### augur.api().Market.getMinDisplayPrice({ market }[, callback])

Returns the minimum displayed price for the specified `market`.

#### augur.api().Market.getNumberOfOutcomes({ market }[, callback])

Returns the number of outcomes for a specified `market`.

#### augur.api().Market.getPayoutDenominator({ market }[, callback])

Returns the payout denominator for a specified `market`.

#### augur.api().Market.getRegistrationToken({ market }[, callback])

Returns the registration token's address for the specified `market`.

#### augur.api().Market.getReportingWindow({ market }[, callback])

Returns the reporting window address for the specified `market`.

#### augur.api().Market.getReputationToken({ market }[, callback])

Returns the reputation token's address for the specified `market`.

#### augur.api().Market.getShareToken({ market, outcome }[, callback])

Returns the share token's address for the specified `market` and `outcome`.

#### augur.api().Market.getTentativeWinningPayoutDistributionHash({ market }[, callback])

Returns the tentatively winning Payout Distribution Hash given a specified `market`.

#### augur.api().Market.getTopic({ market }[, callback])

Returns the topic of the specified `market`.

#### augur.api().Market.isContainerForReportingToken({ market, shadyToken }[, callback])

Returns wether the specific `market` is a container for the `shadyToken` address provided, which is the reporting token address we intend to check. Returns `1` if true, `0` if false.

#### augur.api().Market.isContainerForShareToken({ market, shadyShareToken }[, callback])

Returns wether the specific `market` is a container for the `shadyShareToken` address provided, which is the share token address we intend to check. Returns `1` if true, `0` if false.

#### augur.api().Market.isDoneWithAllReporters({ market }[, callback])

Returns wether the specific `market` is done with the all reporting phase or not. Returns `1` if true, `0` if false.

#### augur.api().Market.isDoneWithAutomatedReporters({ market }[, callback])

Returns wether the specific `market` is done with the automated reporting phase or not. Returns `1` if true, `0` if false.

#### augur.api().Market.isDoneWithLimitedReporters({ market }[, callback])

Returns wether the specific `market` is done with the limited reporting phase or not. Returns `1` if true, `0` if false.

#### augur.api().Market.isFinalized({ market }[, callback])

Returns wether the specific `market` has been finalized or not. Returns `1` if true, `0` if false.

#### augur.api().Market.isInAllDisputePhase({ market }[, callback])

Returns wether the specific `market` is current in the All Dispute Phase or not. Returns `1` if true, `0` if false.

#### augur.api().Market.isInAllReportingPhase({ market }[, callback])

Returns wether the specific `market` is current in the All Reporting Phase or not. Returns `1` if true, `0` if false.

#### augur.api().Market.isInAutomatedDisputePhase({ market }[, callback])

Returns wether the specific `market` is current in the Automated Dispute Phase or not. Returns `1` if true, `0` if false.

#### augur.api().Market.isInAutomatedReportingPhase({ market }[, callback])

Returns wether the specific `market` is current in the Automated Reporting Phase or not. Returns `1` if true, `0` if false.

#### augur.api().Market.isInLimitedDisutePhase({ market }[, callback])

Returns wether the specific `market` is current in the Limited Dispute Phase or not. Returns `1` if true, `0` if false.

#### augur.api().Market.isInLimitedReportingPhase({ market }[, callback])

Returns wether the specific `market` is current in the the Limited Reporting Phase or not. Returns `1` if true, `0` if false.

#### augur.api().Market.needsMigration({ market }[, callback])

Returns wether the specific `market` needs to be migrated to a new branch or not. Returns `1` if true, `0` if false.

#### augur.api().Market.shouldCollectReportingFees({ market }[, callback])

Returns wether the specific `market` should collect reporting fees or not. Returns `1` if true, `0` if false.

```javascript
// Registration Token Contract
const registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
const ownerAddress = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spenderAddress = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api().RegistrationToken.allowance({ registrationToken, ownerAddress, spenderAddress }, function (allowance) { /* ... */ })
// example output:
allowance = "1";

augur.api().RegistrationToken.balanceOf({ registrationToken, address: ownerAddress }, function (balance) { /* ... */ })
// example output:
balance = "1";

augur.api().RegistrationToken.getBranch({ registrationToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api().RegistrationToken.getPeakSupply({ registrationToken }, function (peakSuppy) { /* ... */ })
// example output:
peakSuppy = "234"

augur.api().RegistrationToken.getReportingWindow({ registrationToken }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api().RegistrationToken.getReputationToken({ registrationToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api().RegistrationToken.totalSupply({ registrationToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "215";

```
### [Registration Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/registrationToken.se)

#### augur.api().RegistrationToken.allowance({ registrationToken, ownerAddress, spenderAddress }[, callback])

Returns the allowance that a specified `spenderAddress` can spend of the `ownerAddress`'s `registrationToken`s.

#### augur.api().RegistrationToken.balanceOf({ registrationToken, address }[, callback])

Returns the token balance for the specified `registrationToken` owned by the `address` provided.

#### augur.api().RegistrationToken.getBranch({ registrationToken }[, callback])

Returns the Branch address for the specified `registrationToken`'s Reporting Window.

#### augur.api().RegistrationToken.getPeakSupply({ registrationToken }[, callback])

Returns the peak supply of tokens for the specified `registrationToken`.

#### augur.api().RegistrationToken.getReportingWindow({ registrationToken }[, callback])

Returns the Reporting Window address for the specified `registrationToken`.

#### augur.api().RegistrationToken.getReputationToken({ registrationToken }[, callback])

Returns the Reputation Tokens address for the specific `registrationToken`'s Reporting Window.

#### augur.api().RegistrationToken.totalSupply({ registrationToken }[, callback])

Returns the current total supply of the specified `registrationToken`.

```javascript
// Reporting Token Contract
const reportingToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
const ownerAddress = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spenderAddress = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api().ReportingToken.allowance({ reportingToken, ownerAddress, spenderAddress }, function (allowance) { /* ... */ })
// example output:
allowance = "1"

augur.api().ReportingToken.balanceOf({ reportingToken, address: ownerAddress }, function (balance) { /* ... */ })
// example output:
balance = "1"

augur.api().ReportingToken.getBranch({ reportingToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api().ReportingToken.getMarket({ reportingToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api().ReportingToken.getPayoutDistributionHash({ reportingToken }, function (payoutDistributionHash) { /* ... */ })
// example output:
payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api().ReportingToken.getPayoutNumerator({ reportingToken, index: 0 }, function (payoutNumerator) { /* ... */ })
// example output:
payoutNumerator = "1"

augur.api().ReportingToken.getRegistrationToken({ reportingToken }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api().ReportingToken.getReportingWindow({ reportingToken }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api().ReportingToken.getReputationToken({ reportingToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api().ReportingToken.totalSupply({ reportingToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "210"

```
### [Reporting Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reportingToken.se)

#### augur.api().ReportingToken.allowance({ reportingToken, ownerAddress, spenderAddress }[, callback])

Returns the allowance that a specified `spenderAddress` can spend of the `ownerAddress`'s `reportingToken`s.

#### augur.api().ReportingToken.balanceOf({ reportingToken, address }[, callback])

Returns the token balance for the specified `reportingToken` owned by the provided `address`.

#### augur.api().ReportingToken.getBranch({ reportingToken }[, callback])

Returns the Branch address for the specified `reportingToken`'s Reporting Window.

#### augur.api().ReportingToken.getMarket({ reportingToken }[, callback])

Returns the market address for the specified `reportingToken`.

#### augur.api().ReportingToken.getPayoutDistributionHash({ reportingToken }[, callback])

Returns the payoutDistributionHash for a specific `reportingToken`.

#### augur.api().ReportingToken.getPayoutNumerator({ reportingToken, index }[, callback])

Returns the payout Numerator for a specific `reportingToken` given an outcome `index`.

#### augur.api().ReportingToken.getRegistrationToken({ reportingToken }[, callback])

Returns the Registration Token address for this specific `reportingToken`.

#### augur.api().ReportingToken.getReportingWindow({ reportingToken }[, callback])

Returns the Reporting Window address for the specified `reportingToken`.

#### augur.api().ReportingToken.getReputationToken({ reportingToken }[, callback])

Returns the Reputation Tokens address for the specific `reportingToken`'s Reporting Window.

#### augur.api().ReportingToken.totalSupply({ reportingToken }[, callback])

Returns the current total supply of the specified `reportingToken`.

```javascript
// Reporting Window Contract
const reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api().ReportingWindow.getBranch({ reportingWindow }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api().ReportingWindow.getDisputeEndTime({ reportingWindow }, function (disputeEndTime) { /* ... */ })
// example output:
disputeEndTime = "1500907130"

augur.api().ReportingWindow.getDisputeStartTime({ reportingWindow }, function (disputeStartTime) { /* ... */ })
// example output:
disputeStartTime = "1500647930"

augur.api().ReportingWindow.getEndTime({ reportingWindow }, function (endTime) { /* ... */ })
// example output:
endTime = "1500388730"

augur.api().ReportingWindow.getMaxReportsPerLimitedReporterMarket({ reportingWindow }, function (maxReportsPerLimitedReporterMarket) { /* ... */ })
// example output:
maxReportsPerLimitedReporterMarket = "9"

augur.api().ReportingWindow.getRegistrationToken({ reportingWindow }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api().ReportingWindow.getReportingEndTime({ reportingWindow }, function (reportingEndTime) { /* ... */ })
// example output:
reportingEndTime = "1500647900"

augur.api().ReportingWindow.getReportingStartTime({ reportingWindow }, function (reportingStartTime) { /* ... */ })
// example output:
reportingStartTime = "14998315100"

const reporter = "0x2cd999e2f90dfc237ccbc52e2a469e1e11221f75";
augur.api().ReportingWindow.getReportsByReporter({ reportingWindow, reporter }, function (reportsByReporter) { /* ... */ })
// example output:
reportsByReporter = "0x03198001d1c223b2fdb1866703a7c2e4d5313f80"

augur.api().ReportingWindow.getReputationToken({ reportingWindow }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api().ReportingWindow.getRequiredReportsPerReporterForlimitedReporterMarkets({ reportingWindow }, function (requiredReportsPerReporterForLimitedReporterMarkets) { /* ... */ })
// example output:
requiredReportsPerReporterForLimitedReporterMarkets = "2"

augur.api().ReportingWindow.getStartTime({ reportingWindow }, function (startTime) { /* ... */ })
// example output:
startTime = "14995895900"

augur.api().ReportingWindow.getTargetReportsPerLimitedReporterMarket({ reportingWindow }, function (targetReportsPerLimitedReporterMarket) { /* ... */ })
// example output:
targetReportsPerLimitedReporterMarket = "3"

augur.api().ReportingWindow.getTargetReportsPerReporter({ reportingWindow }, function (targetReportsPerReporter) { /* ... */ })
// example output:
targetReportsPerReporter = "5"

augur.api().ReportingWindow.isActive({ reportingWindow }, function (isActive) { /* ... */ })
// example output:
isActive = "1"

const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api().ReportingWindow.isContainerForMarket({ reportingWindow, shadyMarket: market }, function (isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1"

const registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
augur.api().ReportingWindow.isContainerForRegistrationToken({ reportingWindow, shadyRegistrationToken: registrationToken }, function (isContainerForRegistrationToken) { /* ... */ })
// example output:
isContainerForRegistrationToken = "1"

augur.api().ReportingWindow.isDisputeActive({ reportingWindow }, function (isDisputeActive) { /* ... */ })
// example output:
isDisputeActive = "1"

augur.api().ReportingWindow.isDoneReporting({ reportingWindow, reporter }, function (isDoneReporting) { /* ... */ })
// example output:
isDoneReporting = "1"

augur.api().ReportingWindow.isReportingActive({ reportingWindow }, function (isReportingActive) { /* ... */ })
// example output:
isReportingActive = "1"
```
### [Reporting Window Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reportingWindow.se)

#### augur.api().ReportingWindow.getBranch({ reportingWindow }[, calllback])

Returns the branch address that contains the specified `reportingWindow`.

#### augur.api().ReportingWindow.getDisputeEndTime({ reportingWindow }[, calllback])

Returns the Dispute End Time for a specified `reportingWindow`.

#### augur.api().ReportingWindow.getDisputeStartTime({ reportingWindow }[, calllback])

Returns the Dispute Start Time for the specified `reportingWindow`.

#### augur.api().ReportingWindow.getEndTime({ reportingWindow }[, calllback])

Returns the End Time for the specified `reportingWindow`.

#### augur.api().ReportingWindow.getMaxReportsPerLimitedReporterMarket({ reportingWindow }[, calllback])

Returns the maximum number of reports a limited reporter market can have in the specified `reportingWindow`.

#### augur.api().ReportingWindow.getRegistrationToken({ reportingWindow }[, calllback])

Returns the Registration Token address for the specified `reportingWindow`.

#### augur.api().ReportingWindow.getReportingEndTime({ reportingWindow }[, calllback])

Returns the reporting phase end time for the specified `reportingWindow`.

#### augur.api().ReportingWindow.getReportingStartTime({ reportingWindow }[, calllback])

Returns the reporting phase start time for the specified `reportingWindow`.

#### augur.api().ReportingWindow.getReportsByReporter({ reportingWindow, reporter }[, calllback])

Returns the reports set address for the specified `reportingWindow` and `reporter`.

#### augur.api().ReportingWindow.getReputationToken({ reportingWindow }[, calllback])

Returns the Reputation Token address for the specified `reportingWindow`.

#### augur.api().ReportingWindow.getRequiredReportsPerReporterForlimitedReporterMarkets({ reportingWindow }[, callback])

Returns the required number of reports per reporter for the limited reporter markets contained within the specified `reportingWindow`.

#### augur.api().ReportingWindow.getStartTime({ reportingWindow }[, calllback])

Returns the specified `reportingWindow`'s start time.

#### augur.api().ReportingWindow.getTargetReportsPerLimitedReporterMarket({ reportingWindow }[, callback])

Returns the target number of reports per reporter for the limited reporter markets contained within the specified `reportingWindow`.

#### augur.api().ReportingWindow.getTargetReportsPerReporter({ reportingWindow }[, callback])

Returns the target number of reports per reporter for all markets contained within the specified `reportingWindow`.

#### augur.api().ReportingWindow.isActive({ reportingWindow }[, callback])

Returns wether the specified `reportingWindow` is currently active or not. Returns `1` if true, `0` if false.

#### augur.api().ReportingWindow.isContainerForMarket({ reportingWindow, shadyMarket }[, callback])

Returns wether the specified `reportingWindow` contains the `shadyMarket` or not. Returns `1` if true, `0` if false.

#### augur.api().ReportingWindow.isContainerForRegistrationToken({ reportingWindow, shadyRegistrationToken }[, callback])

Returns wether the specified `reportingWindow` contains the `shadyRegistrationToken` or not. Returns `1` if true, `0` if false.

#### augur.api().ReportingWindow.isDisputeActive({ reportingWindow }[, callback])

Returns wether the specified `reportingWindow`'s dispute phase is active or not. Returns `1` if true, `0` if false.

#### augur.api().ReportingWindow.isDoneReporting({ reportingWindow, reporter }[, callback])

Returns wether the specified `reporter` is finished reporting for the `reportingWindow` or not. Returns `1` if true, `0` if false.

#### augur.api().ReportingWindow.isReportingActive({ reportingWindow }[, callback])

Returns wether the specified `reportingWindow`'s reporting phase is active or not. Returns `1` if true, `0` if false.

```javascript
// Reputation Token Contract
const reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
const ownerAddress = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spenderAddress = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api().ReputationToken.allowance({ reputationToken, ownerAddress, spenderAddress }, function (allowance) { /* ... */ })
// example output:
allowance = "200"

augur.api().ReputationToken.balanceOf({ reputationToken, address: ownerAddress }, function (balance) { /* ... */ })
// example output:
balance = "1000"

augur.api().ReputationToken.getBranch({ reputationToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api().ReputationToken.getTopMigrationDestination({ reputationToken }, function (topMigrationDestination) { /* ... */ })
// example output:
topMigrationDestination = "0x1aa30942000ac72dee6580e1ac32d1d01ac1af00"

augur.api().ReputationToken.totalSupply({ reputationToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "11000000"
```
### [Reputation Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reputationToken.se)

#### augur.api().ReputationToken.allowance({ reputationToken, ownerAddress, spenderAddress }[, callback])

Returns the allowance that a specified `spenderAddress` can spend of the `ownerAddress`'s `reputationToken`s.

#### augur.api().ReputationToken.balanceOf({ reputationToken, address }[, callback])

Returns the token balance of the specified `reputationToken` owned by the `address` provided.

#### augur.api().ReputationToken.getBranch({ reputationToken }[, callback])

Returns the Branch address for the specified `reputationToken`.

#### augur.api().ReputationToken.getTopMigrationDestination({ reputationToken }[, callback])

Returns the top migration destination address for the specified `reputationToken`.

#### augur.api().ReputationToken.totalSupply({ reputationToken }[, callback])

Returns the current total supply of the specified `reputationToken`.

```javascript
// Cash Contract
const owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api().Cash.allowance({ owner, spender }, function (allowance) { /* ... */ })
// example output:
allowance = "100"

augur.api().Cash.balanceOf({ address: owner }, function (balance) { /* ... */ })
// example output:
balance = "10000"

augur.api().Cash.getDecimals({}, function (decimals) { /* ... */ })
// example output:
decimals = "18"

augur.api().Cash.getInitiated({}, function (initiated) { /* ... */ })
// example output:
initiated = "1"

augur.api().Cash.getName({}, function (name) { /* ... */ })
// example output:
name = "Cash"

augur.api().Cash.getSymbol({}, function (symbol) { /* ... */ })
// example output:
symbol = "CASH"

augur.api().Cash.totalSupply({}, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "11000000"
```
### [Cash Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/cash.se)

#### augur.api().Cash.allowance({ owner, spender }[, callback])

Returns the allowance that a specified `spender` address can spend of the `owner` address' Cash tokens.

#### augur.api().Cash.balanceOf({ address }[, callback])

Returns the balance of Cash tokens owned by the specified `address`.

#### augur.api().Cash.getDecimals({}[, callback])

Returns the amount of decimal places that the Cash contract is accurate to: `18`, like Ether.

#### augur.api().Cash.getInitiated({}[, callback])

Returns wether the sender of this call has initiated a withdraw of Ether from the Cash contract or not. Returns `1` if true, `0` if false.

#### augur.api().Cash.getName({}[, callback])

Returns the name string for Cash: `Cash`.

#### augur.api().Cash.getSymbol({}[, callback])

Returns the symbol string for Cash: `CASH`.

#### augur.api().Cash.totalSupply({}[, callback])

Returns the current total supply of Cash.

```javascript
// Orders Contract
const orderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
const type = "1";
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
const outcome = "1";
const fxpPrice = "450000000000000000"; // 0.45

augur.api().Orders.assertIsNotBetterPrice({ type, market, outcome, fxpPrice,
betterOrderID: orderID }, function (isNotBetterPrice) { /* ... */ })
// example output:
isNotBetterPrice = "1"

augur.api().Orders.assertIsNotWorsePrice({ type, market, outcome, fxpPrice, worstOrderID: orderID }, function (isNotWorstPrice) { /* ... */ })
// example output:
isNotWorstPrice = "0"

augur.api().Orders.getAmount({ orderID, type, market, outcome }, function (amount) { /* ... */ })
// example output:
amount = "15"

augur.api().Orders.getBestOrderID({ type, market, outcome }, function (bestOrderID) { /* ... */ })
// example output:
bestOrderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

const secondBestOrderID = "0x49cb49f610b5f6e31ee163a8ad65f964af1088e38c8a1b07f1218177b5e006b5";
augur.api().Orders.getBetterOrderID({ orderID: secondBestOrderID, type, market, outcome }, function (betterOrderID) { /* ... */ })
// example output:
betterOrderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

augur.api().Orders.getGasPrice({ orderID, type, market, outcome }, function (gasPrice) { /* ... */ })
// example output:
gasPrice = "42000000000000"

augur.api().Orders.getLastOutcomePrice({ market, outcome }, function (lastOutcomePrice) { /* ... */ })
// example output:
lastOutcomePrice = "490000000000000000"

augur.api().Orders.getOrderMoneyEscrowed({ orderID, type, market, outcome }, function (orderMoneyEscrowed) { /* ... */ })
// example output:
orderMoneyEscrowed = "5000000000000000000"

augur.api().Orders.getOrderOwner({ orderId, type, market, outcome }, function (owner) { /* ... */ })
// example output:
owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";

augur.api().Orders.getOrderSharesEscrowed({ orderId, type, market, outcome }, function (orderSharesEscrowed) { /* ... */ })
// example output:
orderSharesEscrowed = "0";

augur.api().Orders.getPrice({ orderId, type, market, outcome }, function (price) { /* ... */ })
// example output:
price = "500000000000000000";

augur.api().Orders.getVolume({ market }, function (volume) { /* ... */ })
// example output:
volume = "100000000000000000000000";

const secondWorstOrderID = "0x4b538f4de2517f7d7bbb227161981c51c40bf725da9941b3dc02e6c14cafd1f1";
augur.api().Orders.getWorseOrderID({ orderID: secondWorstOrderID, type, market, outcome }, function (worseOrderID) { /* ... */ })
// example output:
worseOrderID = "0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api().Orders.getWorstOrderID({ type, market, outcome }, function (worstOrderID) { /* ... */ })
// example output:
worstOrderID = "0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api().Orders.isBetterPrice({ type, market, outcome, fxpPrice, orderID }, function (isBetterPrice) { /* ... */ })
// example output:
isBetterPrice = "0"

augur.api().Orders.isWorsePrice({ type, market, outcome, fxpPrice, orderID }, function (isWorsePrice) { /* ... */ })
// example output:
isWorsePrice = "1"
```
### [Orders Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/orders.se)

#### augur.api().Orders.assertIsNotBetterPrice({ type, market, outcome, fxpPrice, betterOrderID }[, callback])

Returns wether the specified `fxpPrice` is not a better price than the `betterOrderID` for a given order `type` trading on the `outcome` of the provided `market`. Returns `1` if true, `0` if false.

#### augur.api().Orders.assertIsNotWorsePrice({ type, market, outcome, fxpPrice, worstOrderID }[, callback])

Returns wether the specified `fxpPrice` is not a worst price than the `worstOrderID` for a given order `type` trading on the `outcome` of the provided `market`. Returns `1` if true, `0` if false.

#### augur.api().Orders.getAmount({ orderID, type, market, outcome }[, callback])

Returns the amount of shares requested on a specified `orderID` of `type` trading on the `outcome` in the provided `market`.

#### augur.api().Orders.getBestOrderID({ type, market, outcome }[, callback])

Returns the order ID of the best order on the `market` of `type` trading on the provided `outcome`.

#### augur.api().Orders.getBetterOrderID({ orderID, type, market, outcome }[, callback])

Returns a better order ID than the `orderID` specified on the `market` of `type` trading on the provided `outcome`.

#### augur.api().Orders.getGasPrice({ orderID, type, market, outcome }[, callback])

Returns the gas price for the `orderID` specified on the `market` of `type` trading on the provided `outcome`.

#### augur.api().Orders.getLastOutcomePrice({ market, outcome }[, callback])

Returns the fixed point value of the last price traded for a specified `market` and `outcome`.

#### augur.api().Orders.getOrderMoneyEscrowed({ orderID, type, market, outcome }[, callback])

Returns the fixed point value of the amount of money escrowed by a specified `orderID` of `type` trading on the `market` and `outcome`.

#### augur.api().Orders.getOrderOwner({ orderID, type, market, outcome }[, callback])

Returns the owner address of the specified `orderID` of `type` trading on `market` and `outcome`.

#### augur.api().Orders.getOrderSharesEscrowed({ orderID, type, market, outcome }[, callback])

Returns the fixed point value of the amount of shares escrowed by a specified `orderID` of `type` trading on the `market` and `outcome`.

#### augur.api().Orders.getPrice({ orderID, type, market, outcome }[, callback])

Returns the fixed point value of the price of a specified `orderID` of `type` trading on the `market` and `outcome`.

#### augur.api().Orders.getVolume({ market }[, callback])

Returns the fixed point value of the volume of a specified `market`.

#### augur.api().Orders.getWorseOrderID({ orderID, type, market, outcome }[, callback])

Returns a worse order ID than the `orderID` specified on the `market` of `type` trading on the provided `outcome`.

#### augur.api().Orders.getWorstOrderID({ type, market, outcome }[, callback])

Returns the order ID of the worst order on the `market` of `type` trading on the provided `outcome`.

#### augur.api().Orders.isBetterPrice({ type, market, outcome, fxpPrice, OrderID }[, callback])

Returns wether the specified `fxpPrice` is a better price than the `orderID` for a given order `type` trading on the `outcome` of the provided `market`. Returns `1` if true, `0` if false.

#### augur.api().Orders.isWorsePrice({ type, market, outcome, fxpPrice, OrderID }[, callback])

Returns wether the specified `fxpPrice` is a worst price than the `orderID` for a given order `type` trading on the `outcome` of the provided `market`. Returns `1` if true, `0` if false.

```javascript
// Orders Fetcher Contract
const orderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
const type = "1";
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
const outcome = "1";
const fxpPrice = "450000000000000000"; // 0.45

augur.api().OrdersFetcher.ascendOrderList({ type, market, outcome, fxpPrice, lowestOrderID: orderID }, function (ascendingOrderList) { /* ... */ })
// example output:
ascendingOrderList = [
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870",
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0"]

augur.api().OrdersFetcher.descendOrderList({ type, market, outcome, fxpPrice, highestOrderID: orderID }, function (decendingOrderList) { /* ... */ })
// example output:
decendingOrderList = [
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e",
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"]

augur.api().OrdersFetcher.findBoundingOrders({ type, market, outcome, fxpPrice, bestOrderID: orderID, worstOrderID: 0, betterOrderID: 0, worseOrderID: 0 }, function (boundingOrders) { /* ... */ })
// example output:
boundingOrders = [
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0",
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e"]

augur.api().OrdersFetcher.getOrder({ orderID, type, market, outcome }, function (order) { /* ... */ })
// example output:
order = [ "10000000000000000000",
          "500000000000000000",
          "0x438f2aeb8a16745b1cd711e168581ebce744ffaa",
          "5000000000000000000",
          "0",
          "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0",
          "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e",
          "42000000000000"  ]

augur.api().OrdersFetcher.getOrderIDs({ type, market, outcome, startingOrderID: orderID, numOrdersToLoad: 2 }, function (orderIDs) { /* ... */ })
// example output:
orderIDs = [
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870",
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0"]
```
### [Orders Fetcher Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/ordersFetcher.se)

#### augur.api().OrdersFetcher.ascendOrderList({ type, market, outcome, fxpPrice, lowestOrderID }[, callback])

Returns an array containing the order IDs that should be set to `betterOrderID` and `worseOrderID` respectively for an order inserted at `fxpPrice`. `lowestOrderID` is an order ID expected to be a worse price than the `fxpPrice` specified for an order of `type` trading on `market` around `outcome`.

#### augur.api().OrdersFetcher.descendOrderList({ type, market, outcome, fxpPrice, highestOrderID }[, callback])

Returns an array containing the order IDs that should be set to `betterOrderID` and `worseOrderID` respectively for an order inserted at `fxpPrice`. `highestOrderID` is an order ID expected to be a better price than the `fxpPrice` specified for an order of `type` trading on `market` around `outcome`.

#### augur.api().OrdersFetcher.findBoundingOrders({ type, market, outcome, fxpPrice, bestOrderID, worstOrderID, betterOrderID, worseOrderID }[, callback])

Returns an array containing the order IDs that should be set to `betterOrderID` and `worseORderID` respectively for an order inserted at `fxpPrice`. `betterOrderID` and `worseOrderID` should be orders that are better or worse than the `fxpPrice` for an order of `type` trading on `market` around `outcome`. `bestOrderID` and `worstOrderID` should be the best and worst order IDs on the order book for the specified `market`.

the proper place to insert the fxpPrice order, IE returns a fxpPrice's better/worse order ids for insertion into the order book.

#### augur.api().OrdersFetcher.getOrder({ orderID, type, market, outcome }[, callback])

Returns a length 8 array containing information about a specified `orderID` of `type` trading on `market` around `outcome`. Information returned includes: amount of attoshares, fixed point display price, owner address, tokens escrowed, shares escrowed, better order id, worse order id, and gas price.

#### augur.api().OrdersFetcher.getOrderIDs({ type, market, outcome, startingOrderID, numOrdersToLoad }[, callback])

Returns an array of order IDs of `type` trading on `market` around `outcome` starting from the `startingOrderID` order ID specified. The array will be of length `numOrdersToLoad`.

```javascript
// Share Token Contract
const shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
const owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api().ShareToken.allowance({ shareToken, owner, spender }, function (allowance) { /* ... */ })
// example output:
allowance = "100"

augur.api().ShareToken.balanceOf({ shareToken, address: owner }, function (balance) { /* ... */ })
// example output:
balance = "1000"

augur.api().ShareToken.getDecimals({ shareToken }, function (decimals) { /* ... */ })
// example output:
decimals = "18"

augur.api().ShareToken.getMarket({ shareToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api().ShareToken.getName({ shareToken }, function (name) { /* ... */ })
// example output:
name = "Shares"

augur.api().ShareToken.getOutcome({ shareToken }, function (outcome) { /* ... */ })
// example output:
outcome = "1"

augur.api().ShareToken.getSymbol({ shareToken }, function (symbol) { /* ... */ })
// example output:
symbol = "SHARES"

augur.api().ShareToken.isShareToken({ shareToken }, function (isShareToken) { /* ... */ })
// example output:
isShareToken = "1"

augur.api().ShareToken.totalSupply({ shareToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "50000"
```
### [Share Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/shareToken.se)

#### augur.api().ShareToken.allowance({ shareToken, owner, spender }[, callback])

Returns the token allowance a `spender` can use of an `owner`'s specified `shareToken`s.

#### augur.api().ShareToken.balanceOf({ shareToken, address }[, callback])

Returns the token balance of a specified `shareToken` owned by the `address`.

#### augur.api().ShareToken.getDecimals({ shareToken }[, callback])

Returns the amount of decimal places the `shareToken` is accurate to: `18`.

#### augur.api().ShareToken.getMarket({ shareToken }[, callback])

Returns the market address for the specified `shareToken`.

#### augur.api().ShareToken.getName({ shareToken }[, callback])

Returns the name string of the `shareToken`: `Shares`.

#### augur.api().ShareToken.getOutcome({ shareToken }[, callback])

Returns the Outcome of the market that the specified `shareToken` is for.

#### augur.api().ShareToken.getSymbol({ shareToken }[, callback])

Returns the symbol string of the `shareToken`: `SHARES`.

#### augur.api().ShareToken.isShareToken({ shareToken }[, callback])

Returns wether the `shareToken` is a share token or not. Returns `1` if true, `0` if false. (should always be true.)

#### augur.api().ShareToken.totalSupply({ shareToken }[, callback])

Returns the total supply of `shareToken`s specified.

```javascript
// Topics Contract
const topics = "0x14f094c79a676c681e7cc490e775f73072e535ae";
const topic = "Augur";

augur.api().Topics.count({ topics }, function (count) { /* ... */ })
// example output:
count = "152"

augur.api().Topics.getPopularity({ topics, topic }, function (popularity) { /* ... */ })
// example output:
popularity = "1000"

augur.api().Topics.getPopularityByOffset({ topics, offset: 0 }, function () { /* ... */ })
// example output:
popularity = "1000"

augur.api().Topics.getTopicByOffset({ topics, offset: 0 }, function () { /* ... */ })
// example output:
topic = "Augur"
```
### [Topics Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/topics.se)

#### augur.api().Topics.count({ topics }[, callback])

Returns a count of all the Topics at the specified `topics` address.

#### augur.api().Topics.getPopularity({ topics, topic }[, callback])

Returns the popularity value of a specified `topic` at the `topics` address provided.

#### augur.api().Topics.getPopularityByOffset({ topics, offset }[, callback])

Returns the popularity of the Topic at the index `offset` at the `topics` address provided.

#### augur.api().Topics.getTopicByOffset({ topics, offset }[, callback])

Returns the Topic at the index `offset` at the `topics` address provided.


Transaction API
---------------

Augur's Transaction API is made up of "setter" methods that can both read from and write to the blockchain using Ethereum's `eth_sendTransaction` RPC.  Under the hood, the API uses augur.js's convenience wrapper for `eth_sendTransaction`, `augur.transact`.

### Callbacks

All Transaction API methods accept three callback functions:

#### onSent(sentResponse)

Fires when the initial `eth_sendTransaction` response is received.  If the transaction was broadcast to the network without problems, `sentResponse` will have two fields: `txHash` (the transaction hash as a hex string) and `callReturn` (the value returned by the invoked contract method).  The optional `returns` field in the `tx` object sent to `augur.transact` can be used to specify the format of the `callReturn` value.

#### onSuccess(successResponse)

Fires when the transaction is successfully incorporated into a block and added to the blockchain, as indicated by a nonzero `blockHash` value.  `successResponse` is structured the same way as `eth_getTransactionByHash` responses (see code example for details), with the addition of a `callReturn` field which contains the contract method's return value.

#### onFailed(failedResponse)

Fires if the transaction is unsuccessful.  `failedResponse` has `error` (error code) and `message` (error description) fields, describing the way in which the transaction failed.

No callbacks are required; if none are supplied, then the transaction hash (or error) will simply be returned.  In this case, the transaction has been broadcast to the Ethereum network, but further confirmation of the transaction and/or lookups of its return value will need to be done manually.  If an `onSent` but not an `onSuccess` callback is provided, the transaction will be broadcast, and `onSent` will be passed an `sentResponse` object containing `txHash` and `callReturn` fields, but `augur.rpc` will not poll the network repeatedly to check on the status of the transaction.

### Arguments

All Transaction API methods accept either positional or object arguments.  Examples are shown using object arguments (to make the field names clear).  The arguments are displayed in order from left-to-right (top-to-bottom if multiple lines are needed).  For positional arguments, `onSent`, `onSuccess`, and `onFailed` should always be the last three arguments, in that order.

```javascript
// transaction object (generated by the buyShares method)
var tx = {
  "to": "0x2e5a882aa53805f1a9da3cf18f73673bca98fa0f",
  "method": "buyShares",
  "signature": "iiiiii",
  "from": "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
  "params": [
    "0xf69b5",
    "-0x130158e01e01cc67d3d8803f88493b8aadc6654be759fbf2d40105506b41daa3",
    1,
    "0x199999999999999a",
    "0",
    0
  ],
  "returns": "unfix",
  "send": true
};

// onSent callback fires when the initial eth_sendTransaction response
// is received
var onSent = function (sentResponse) {
  console.log("transaction sent:", sentResponse);  
};

// onSuccess callback fires
var onSuccess = function (successResponse) {
  console.log("transaction successful:", successResponse);
};

// onFailed callback fires when the transaction is malformed, fails to confirm,
// or an object with an error field is received
var onFailed = function (failedResponse) {
  console.error("transaction failed:", failedResponse);
};

augur.transact(tx, onSent, onSuccess, onFailed);
// example outputs:
sentResponse = {
  txHash: '0xdf096e249638df143118f562868e90285579819e59b09f0784b95fa5fd920413',
  callReturn: '0.05959702938270431576'
}
successResponse = {
  nonce: '0x4e1',
  blockHash: '0xb8e4e78c9cf949729bbb7b94e942d8d63e67e9f48b394f3208cf0d2928e44bad',
  blockNumber: '0x6a5f',
  transactionIndex: '0x0',
  from: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  to: '0x2e5a882aa53805f1a9da3cf18f73673bca98fa0f',
  value: '0x0',
  gas: '0x2fd618',
  gasPrice: '0xba43b7400',
  input: '0x7d9e764100000000000000000000000000000000000000000000000000000000000f69b5ecfea71fe1fe33982c277fc077b6c47552399ab418a6040d2bfefaaf94be255d0000000000000000000000000000000000000000000000000000000000000001000000000000000000000000000000000000000000000000199999999999999a00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000',
  callReturn: '0.05959702938270431576',
  txHash: '0xdf096e249638df143118f562868e90285579819e59b09f0784b95fa5fd920413'
}
failedResponse = {
  error: 501,
  message: 'polled network but could not confirm transaction'
}
```
### transact(tx[, onSent, onSuccess, onFailed])

<aside class="warning">While it is possible to use <code>augur.transact</code> directly, it is generally easier and less error-prone to use one of the named API functions documented in the following sections.  Readers who want to use the Transaction API and aren't terribly curious about the augur.js/ethrpc plumbing under the hood should jump to the next section!</aside>

`augur.transact` carries out the following "send-call-confirm" sequence:

1. `augur.transact` sends an `eth_sendTransaction` RPC request (or `eth_sendRawTransaction` for transactions which are already signed), which broadcasts the transaction to the Ethereum network.  If an error is received, then the error is passed to `onFailed` and `augur.transact` terminates.  If a transaction hash is received, then this transaction is added to the `augur.rpc.txs` object (which is indexed by transaction hash, e.g. `augur.rpc.txs[txHash]`) and assigned a `status` of `"pending"`.

2. `augur.rpc.confirmTx` uses `eth_getTransactionByHash` to look up the transaction's exact ABI-encoded inputs.  It then sends an `eth_call` RPC using these inputs to get the invoked method's return value (if any).  If the return value is an error, then the error is passed to `onFailed` and `augur.transact` terminates.  Otherwise, `augur.rpc.encodeResult` encodes the return value (as specified in the `returns` field of the `tx` input), and an object with `txHash` and `callReturn` fields is passed to `onSent`.

3. Every `augur.rpc.TX_POLL_INTERVAL` milliseconds, `augur.rpc.txNotify` looks up the transaction using `eth_getTransactionByHash`.  If `augur.rpc.txNotify` receives a `null` response, `augur.rpc.txs[txHash].status` is set to `"failed"`.  A null response indicates that the transaction has been (silently) removed from geth's transaction pool.  This can happen if the transaction is a duplicate of another transaction that has not yet cleared the transaction pool (and therefore geth does not fire a duplicate transaction error), or if the transaction's nonce (but not its other fields) is a duplicate.  In the former case, the duplicate transaction is assumed to be an accidental submission, a `TRANSACTION_NOT_FOUND` error is passed to `onFailed`, and the `augur.transact` sequence terminates.  The latter case arises when the transaction was signed client-side -- and therefore its nonce was also generated client-side -- then submitted using `eth_sendRawTransaction` (instead of `eth_sendTransaction`).  Network latency, client-side errors, and other factors can result in duplicate raw transaction nonces.  In this case, `augur.rpc.txs[txHash].status` is set to `"resubmitted"`, the transaction's nonce is incremented, and `augur.transact` is executed with the new transaction.

4. If `augur.rpc.txNotify` receives a non-null transaction object, then `augur.rpc.checkBlockHash` checks to see if the transaction's `blockHash` field is a nonzero value, indicating that it has been successfully incorporated into a block and attached to the blockchain.  If `blockHash` is zero, then after `augur.rpc.TX_POLL_INTERVAL` elapses, `augur.rpc.txNotify` again looks up the transaction; this step repeats at most `augur.rpc.TX_POLL_MAX` times, after which `augur.rpc.txs[txHash].status` is set to `"unconfirmed"`, the `TRANSACTION_NOT_CONFIRMED` error is passed to `onFailed`, and the sequence terminates.  If `blockHash` is non-zero, then `augur.rpc.txs[txHash].status` is set to `"confirmed"`.  A `callReturn` field is added to the transaction object, which is then passed to `onSuccess`, completing the sequence.

<aside class="notice">The <code>augur.rpc</code> object is simply an instance of <a href="https://github.com/AugurProject/ethrpc">ethrpc</a> that has its state synchronized with the <code>augur</code> object.</aside>

The first argument to `augur.transact` is a "transaction object".


```javascript
// Market Contract
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
const payoutNumerators = [ 0, 2 ];

augur.api().Market.approveSpenders({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320485,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xe0dd7114a82fda1daba5adee379eb2fc4fce72e2b2d0005833ffd5ee1f54064c",
  input: "0x8d7e8a57",
  nonce: "0x1",
  timestamp: 1501003130,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.automatedReport({
	market,
  payoutNumerators,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320486,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x4c291db662925c48ed38d3c33e3f7e6599f956f3254d4a8464b71bdadb758316",
  input: "0x17c18af20000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002",
  nonce: "0x2",
  timestamp: 1501003132,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

const newCreator = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
augur.api().Market.changeCreator({
	market,
  newCreator,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320487,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x1053711293fe30fd7bfe3e0f138ef4f38357553ffa4c33a1a4b8233dc250b531",
  input: "0xc78c9a9e000000000000000000000000438f2aeb8a16745b1cd711e168581ebce744ffaa",
  nonce: "0x3",
  timestamp: 1501003133,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.decreaseMarketCreatorSettlementFeeInAttoethPerEth({
	market,
  newFeePerEthInWei: '1000000000',
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320488,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x6544da2190a54542e95473a6c9b18b7259480b8b48953eaa750d3e1379d8ccd6",
  input: "0x4c92c4b3000000000000000000000000000000000000000000000000000000003b9aca00",
  nonce: "0x4",
  timestamp: 1501003134,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.disputeAllReporters({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320489,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x80dbc117b8dc3868944cb8b7748ab53cec5a2d9f5041f882b04b0bf8a88e6172",
  input: "0x99ea0fd5",
  nonce: "0x5",
  timestamp: 1501003135,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.disputeAutomatedReport({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320490,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xd0d81785960bb128c35748e018ad52e599a80921c39ac02adabdaeb4a23d926c",
  input: "0x7a13d14c",
  nonce: "0x6",
  timestamp: 1501003136,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.disputeLimitedReporters({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320491,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xd3f644e8904966909a9d974c7ed63ae8ddbb0f23d824f576764afddd7023ef88",
  input: "0x3f4628c1",
  nonce: "0x7",
  timestamp: 1501003137,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.migrateThroughAllForks({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320492,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x32bf0849d589b6dd639791c7ea49579ea654a648c41b3a614878d77608ef1b07",
  input: "0x00987265",
  nonce: "0x8",
  timestamp: 1501003138,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.migrateThroughOneFork({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320493,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x32bf0849d589b6dd639791c7ea49579ea654a648c41b3a614878d77608ef1b07",
  input: "0x069b559c",
  nonce: "0x9",
  timestamp: 1501003139,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.tryFinalize({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320494,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x6b506f0f96381638635948aa0969f4fbb0e918b7a752643cbe7619f0c1f9fc67",
  input: "0x338e579e",
  nonce: "0xa",
  timestamp: 1501003140,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.tryFinalizeAllReporting({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320495,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xa1ffc62d3b695a834d17aae5be1dbe4ba23ffe9d8dd43935e6c9651445c80f68",
  input: "0xef202e5f",
  nonce: "0xb",
  timestamp: 1501003141,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.tryFinalizeAutomatedReport({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320496,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x515c59f42ef87795e97665c249447164248163aea50ef027d1b3cdda3cd851c2",
  input: "0x806ec359",
  nonce: "0xc",
  timestamp: 1501003142,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.tryFinalizeFork({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320497,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x6309a70af5a70eb6b4c2f23255334a19633f3d1d1c57fa79bb5c4ba1288421a5",
  input: "0x685186c2",
  nonce: "0xd",
  timestamp: 1501003143,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

augur.api().Market.tryFinalizeLimitedReporting({
	market,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320498,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x46d6ffc1189a8cd5dbdba9a253294c6fed16f7d654dc80af359f3dc65fc51032",
  input: "0x3afa4270",
  nonce: "0xe",
  timestamp: 1501003144,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}

const payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";
augur.api().Market.updateTentativeWinningPayoutDistributionHash({
	market,
  payoutDistributionHash,
	onSent: (result) => console.log(result),
	onSuccess: (result) => console.log(result),
	onFailed: (result) => console.log(result)
})
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320499,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x7c5aff31c3c88edfb03d10ced2c561a7464326a6941db1eee868c0a46e7fc77d",
  input: "0xe36cd6e04480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a",
  nonce: "0xf",
  timestamp: 1501003145,
  to: "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  value: "0x0"
}
```
### [Market Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/market.se)

#### augur.api().Market.approveSpenders({ market[, onSent, onSuccess, onFailed ]})

This transaction calls a number of `approve` transactions for the `market`'s `denominationToken` and `shareTokens` to allow other contracts the ability to transfer around the `market`'s tokens so the `market` can function.

#### augur.api().Market.automatedReport({ market, payoutNumerators[, onSent, onSuccess, onFailed ]})

This transaction is used by the `automatedReporter` for a specified `market` to report the winning outcome, by submitting `payoutNumerators`, of the `market`. This transaction will fail if the `msg.sender` isn't the `automatedReporterAddress` set for this `market`, or if this `market` isn't in the Automated Reporting Phase.

#### augur.api().Market.changeCreator({ market, newCreator[, onSent, onSuccess, onFailed ]})

Changes the `market` creator address to the `newCreator` address submitted. This transaction will fail if the `msg.sender` isn't set as the creator of the `market`.

#### augur.api().Market.decreaseMarketCreatorSettlementFeeInAttoethPerEth({ market, newFeePerEthInWei[, onSent, onSuccess, onFailed ]})

Lowers the `market` creator's settlement fee in attoeth per `Eth` settled to the `newFeePerEthInWei` value. This transaction will fail if the `msg.sender` is not the creator of the `market`, if `newFeePerEthInWei` is 0 or less, or if `newFeePerEthInWei` isn't a lower number than the current fee per `Eth`.

#### augur.api().Market.disputeAllReporters({ market[, onSent, onSuccess, onFailed ]})

This transaction will trigger a dispute of the all reporters phase of reporting for the specified `market`. This transaction will take the bond amount from the `msg.sender` and then move the `market` into the upcoming reporting window. This transaction will fail if the `market` isn't in the all reporters dispute phase of reporting.

#### augur.api().Market.disputeAutomatedReport({ market[, onSent, onSuccess, onFailed ]})

This transaction will trigger a dispute of the automated report phase of reporting for the specified `market`. This transaction will take the bond amount from the `msg.sender` and then update the `market` reporting phase to limited reporting. This transaction will fail if the `market` isn't in the automated dispute phase of reporting or if the `market` has been finalized.

#### augur.api().Market.disputeLimitedReporters({ market[, onSent, onSuccess, onFailed ]})

This transaction will trigger a dispute of the limited report phase of reporting for the specified `market`. This transaction will take the bond amount from the `msg.sender` and then move the `market` into the next reporting window for all reporting. This transaction will fail if the `market` isn't in the limited dispute phase of reporting.

#### augur.api().Market.migrateThroughAllForks({ market[, onSent, onSuccess, onFailed ]})

This transaction will call `migrateThroughOneFork` repeatedly until the `market` has moved through all forks or has reached an active fork which will throw.

#### augur.api().Market.migrateThroughOneForks({ market[, onSent, onSuccess, onFailed ]})

This transaction will move the `market` onto the active branch following a fork and refund bond holders for limited or all reporting disputes. This transaction will fail if no move is required or if the forked market isn't finalized. Returns `1` if a move occurred, `0` if no move occurred, and throws if the forking market isn't finalized.

#### augur.api().Market.tryFinalize({ market[, onSent, onSuccess, onFailed ]})

This transaction will attempt to finalize the `market` by calling 'tryFinalizeAutomatedReport', 'tryFinalizeLimitedReporting', 'tryFinalizeAllReporting', and 'tryFinalizeFork' in that order. If the `market` becomes finalized after any of those calls, this transaction will return `1`. If none of those calls finalized the market then this transaction returns `0`.

#### augur.api().Market.tryFinalizeAllReporting({ market[, onSent, onSuccess, onFailed ]})

This transaction will attempt to finalize the `market`'s all reporting phase by first moving the `market` through all forks, changing the `market` to a finalized state, and pays reward to bond holders. This transaction will fail if this `market` hasn't gone through a limited reporters phase, this `market` doesn't have a limited reporters dispute bond holder, this `market` does have an all reporters dispute bond holder, or if this `market`'s reporting window isn't complete. Returns `1` if successful, `0` if the `market` can't be finalized.

#### augur.api().Market.tryFinalizeAutomatedReport({ market[, onSent, onSuccess, onFailed ]})

This transaction will attempt to finalize the `market`'s Automated reporting phase by changing the `market` to a finalized state, and pays reward to automated report bond holders. This transaction will fail if this `market` doesn't have a reported winning outcome, if this `market` doesn't have a Automated Reporter Dispute Bond Holder, or if this `market`'s automated report dispute period hasn't ended. Returns `1` if successful, `0` if the `market` can't be finalized.

#### augur.api().Market.tryFinalizeFork({ market[, onSent, onSuccess, onFailed ]})

This transaction will attempt to finalize `market` after it caused a fork and pays out rewards to the bond holders. This transaction will fail if the market hasn't gone through the limited reporters phase, if the market doesn't have a limited reporter dispute bond holder, if the market hasn't gone through the all reporters phase, if the market doesn't have a all reporter dispute bond holder, if this market isn't the market that caused a fork, if this market doesn't have a winning branch, if the winning branch doesn't have at least half the total supply of REP (5.5 million), or if it's before the fork end time. Returns `1` if successful, `0` if the `market` can't be finalized.

#### augur.api().Market.tryFinalizeLimitedReporting({ market[, onSent, onSuccess, onFailed ]})

This transaction will attempt to finalize the `market`'s limited reporting phase by first moving the `market` through all forks, changing the `market` to a finalized state, and pays reward to bond holders. This transaction will fail if this `market` hasn't gone through a automated report phase, this `market` doesn't have a automated report dispute bond holder, or if this `market`'s reporting window isn't complete. Returns `1` if successful, `0` if the `market` can't be finalized.

#### augur.api().Market.updateTentativeWinningPayoutDistributionHash({ market, payoutDistributionHash[, onSent, onSuccess, onFailed ]})

This transaction will attempt to update the `tentativeWinningPayoutDistributionHash` for this `market` to the `payoutDistributionHash` provided. This transaction will not update the `tentativeWinningPayoutDistributionHash` if it already has a value and it's supply of reporting tokens is higher than the `payoutDistributionHash`'s supply of reporting tokens. This transaction will fail if the `payoutDistributionHash` provided isn't a hash contained within this `market`'s reporting tokens. Returns `1`.

```javascript
// Registration Token Contract
const registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
const spenderAddress = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";
const attotokens = "100000000000000000000";

augur.api().RegistrationToken.approve({
  registrationToken,
  spenderAddress,
  attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320494,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x2b51b527645d4844b5f604c39c7b5bca1e7de02b028f9e70d3b2c0aaf4471163",
  input: "0x83b58638000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0x7",
  timestamp: 1501003138,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

augur.api().RegistrationToken.redeem({
  registrationToken,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320495,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x35fceac9ab27d1e6922d10c90db97cb863876dbfb74503ef93e5df5161e04fe2",
  input: "0xbe040fb0",
  nonce: "0x8",
  timestamp: 1501003139,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

augur.api().RegistrationToken.register({
  registrationToken,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320496,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x2324f99375a30a212d9b47c2478516a79c98804379599fcf9030ddbd46fd87e4",
  input: "0x1aa3a008",
  nonce: "0x9",
  timestamp: 1501003140,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

augur.api().RegistrationToken.transfer({
  registrationToken,
  destinationAddress: spenderAddress,
  attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});

// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320497,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x6d3e368b97bb342b2f901efb94a5b306a33f9d623ababd1a258ab3c287762682",
  input: "0x86744558000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xa",
  timestamp: 1501003141,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

const sourceAddress = "0x39D3b15006e580077a2E8B51B93BE90cCF1EC0e0";
augur.api().RegistrationToken.transferFrom({
  registrationToken,
  sourceAddress,
  destinationAddress: spenderAddress,
  attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320498,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xd9417b50a49b50a50e133173dc3fb994477af9e30cecd2c3c845f7bc79ce8f78",
  input: "0x27f08b0000000000000000000000000039d3b15006e580077a2e8b51b93be90ccf1ec0e0000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xb",
  timestamp: 1501003142,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}
```
### [Registration Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/registrationToken.se)

#### augur.api().RegistrationToken.approve({ registrationToken, spenderAddress, attotokens[, onSent, onSuccess, onFailed ]})

Allows the `spenderAddress` the ability to spend up to `attotokens` worth of the specified `registrationToken` for the sender of this `approve` transaction (`msg.sender`). This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `spenderAddress`, and `attotokens` value approved.

#### augur.api().RegistrationToken.redeem({ registrationToken[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of this transaction has a `registrationToken`, has completed reporting, and the reporting window for the specified `registrationToken` has completed then transfer the bond amount of reputation tokens to the `msg.sender`. Currently the bond amount is set to 10<sup>18</sup> attotokens, or 1 `REP`.

#### augur.api().RegistrationToken.register({ registrationToken[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of this transaction has the bond amount of `REP`, the reporting window for this `registrationToken` hasn't started yet, and the `msg.sender` doesn't already have a `registrationToken` then this transaction will transfer the bond amount of `reputationTokens` out of the `msg.sender`'s wallet and transfer 1 `registrationToken` to `msg.sender` in return. Currently the bond amount is set to 10<sup>18</sup> attotokens, or 1 `REP`.

#### augur.api().RegistrationToken.transfer({ registrationToken, destinationAddress, attotokens[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of the `transfer` transaction has enough of `registrationToken` to be able to transfer `attotokens` worth to the `destinationAddress` and `attotokens` is a valid value between 1 and 2<sup>254</sup> then this transaction will send `attotokens` worth of `registrationToken` to the specified `destiniationAddress` from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address, to address, and `attotokens` amount transfered.

#### augur.api().RegistrationToken.transferFrom({ registrationToken, sourceAddress, destinationAddress, attotokens[, onSent, onSuccess, onFailed ]})

If the `sourceAddress` of the `transferFrom` transaction has enough of `registrationToken` to be able to transfer `attotokens` worth to the `destinationAddress`, `attotokens` is a valid value between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `attotokens` worth of `registrationToken` for `sourceAddress` then this transaction will send `attotokens` worth of `registrationToken` to the specified `destinationAddress` from the `sourceAddress`. This transaction will spawn a `Transfer` event which will record the from address, to address, and `attotokens` amount transfered.

```javascript
// Reporting Token Contract
const reportingToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
const spenderAddress = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";
const attotokens = "100000000000000000000";

augur.api().ReportingToken.approve({
  reportingToken,
  spenderAddress,
  attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320499,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xb3f94e1ad1890d0a14b21e3fd46530eb8c18887edc810e01fc789ccbfb39f067",
  input: "0x83b58638000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xc",
  timestamp: 1501003143,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

augur.api().ReportingToken.buy({
  reportingToken,
  attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320500,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x99620dd1428671eb095de26ad02137916237844456e243cdaa9d9821affa5120",
  input: "0xe94030130000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xd",
  timestamp: 1501003143,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

augur.api().ReportingToken.extractBondHolderPayments({
  reportingToken,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320501,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x1a2c55d91a8343b32ba3713d4ef1fa766cd4643ac2c5353fa2634248e3d15eec",
  input: "0xe9aa05a1",
  nonce: "0xd1",
  timestamp: 1501003145,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

augur.api().ReportingToken.migrateLosingTokens({
  reportingToken,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320502,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x75cb653f1ab81c95d0aad86c03e092a3fa9de7252603edfabee534b8e5183141",
  input: "0xe9aa05a1",
  nonce: "0xd2",
  timestamp: 1501003151,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

augur.api().ReportingToken.redeemDisavowedTokens({
  reportingToken,
  reporter: spenderAddress,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320503,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x3c7827f3cc51e4062c73bf9fdbd6ad51edec7d31842900cd88811e67d83eb514",
  input: "0x60b54d2e000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e",
  nonce: "0xd3",
  timestamp: 1501003152,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

augur.api().ReportingToken.redeemForkedTokens({
  reportingToken,
  reporter: spenderAddress,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320504,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x8cfc973a802f44e5c0a93a8a0d294e680fe4735ca4f49310038908d73bb4536c",
  input: "0x00633a30000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e",
  nonce: "0xd4",
  timestamp: 1501003153,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

augur.api().ReportingToken.redeemWinningTokens({
  reportingToken,
  reporter: spenderAddress,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320505,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xa94d27cf876602ff5cbbce344e5d03ff30c60d61c2e2adf4bf9c54c303d51b81",
  input: "0xc165c7cc000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e",
  nonce: "0xd5",
  timestamp: 1501003155,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

augur.api().ReportingToken.transfer({
  reportingToken,
  destinationAddress: spenderAddress,
  attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320500,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x8f92137eff5e7824423ff6e79e15188b61d9dd9244fd2c436b020de6d8e721fe",
  input: "0x86744558000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xe",
  timestamp: 1501003144,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}

const sourceAddress = "0x34c85afe56c392e240c64dc09d2a7962afe2920a";
augur.api().ReportingToken.transferFrom({
  reportingToken,
  sourceAddress,
  destinationAddress: spenderAddress,
  attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320501,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x2c678df877e01d343a4e7701b92dddcecafc095fd1e4d90423838cd73eadb7d7",
  input: "0xc19cca5200000000000000000000000034c85afe56c392e240c64dc09d2a7962afe2920a000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xf",
  timestamp: 1501003145,
  to: "0x8385755a52e85df2f571ce5e1550e5472c639352",
  value: "0x0"
}
```
### [Reporting Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reportingToken.se)

#### augur.api().ReportingToken.approve({ reportingToken, spenderAddress, attotokens[, onSent, onSuccess, onFailed ]})

Allows the `spenderAddress` the ability to spend up to `attotokens` worth of the specified `reportingToken` for the `msg.sender` of this `approve` transaction. This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `spenderAddress`, and `attotokens` value approved.

#### augur.api().ReportingToken.buy({ reportingToken, attotokens[, onSent, onSuccess, onFailed ]})

Purchases `attotokens` worth of `reportingToken` for `msg.sender` using the `msg.sender`'s `reputationToken`s. If the `msg.sender` doesn't have `attotokens` worth of `reputationToken`, if `attotokens` value isn't between 1 and 2<sup>254</sup>, the `market` for this `reportingToken` can't be reported on, or the `msg.sender` doesn't have a `registrationToken` for the `reportingWindow` containing the `reportingToken`'s `market` then this transaction will fail. This transaction will spawn a `Transfer` event which will record the from address, to address, and the amount of `attotokens` purchased.

#### augur.api().ReportingToken.extractBondHolderPayments({ reportingToken[, onSent, onSuccess, onFailed ]})

Extracts reputation from the `reportingToken` and transfers it to the `market` for this `reportingToken`. Does nothing if the `market` already has the desired amount of `REP` to reward a successful dispute. Returns 1 if reputation was moved from `reportingToken` to the market, returns 0 if the market already has enough `REP` to reward a successful dispute.

#### augur.api().ReportingToken.migrateLosingTokens({ reportingToken[, onSent, onSuccess, onFailed ]})

Sets the supply of this `reportingToken` to 0 and transfers all `REP` on this `reportingToken` to this `reportingToken`'s `market`'s winning reporting token contract. This will fail if the `market` isn't finalized, the `market` isn't a container for this `reportingToken`, the `market` for this `reportingToken` is the cause of a fork, and the `market`'s winning reporting token isn't this `reportingToken`.

#### augur.api().ReportingToken.redeemDisavowedTokens({ reportingToken, reporter[, onSent, onSuccess, onFailed ]})

Transfers `REP` to the `reporter` from the `reportingToken` for the value of `reportingToken` owned by `reporter`. This transaction will fail if the `reportingToken` is still contained within the `reportingToken`'s `market`. This transaction will spawn a `Transfer` event which will record the from address, to address, and the amount of `attotokens` of `REP` transferred to the `reporter`.

#### augur.api().ReportingToken.redeemForkedTokens({ reportingToken, reporter[, onSent, onSuccess, onFailed ]})

Transfers `REP` to the `reporter` on the new `branch` for the amount of `reportingToken`s owned by `reporter`. This transaction will fail if the `msg.sender` isn't the `reporter` address, if the `market` isn't a container for the `reportingToken`, or if the `market` isn't the cause of a fork. This transaction will spawn a `Transfer` event which will record the from address, to address, and the amount of `attotokens` of `REP` transferred to the `reporter` on the new branch.

#### augur.api().ReportingToken.redeemWinningTokens({ reportingToken, reporter[, onSent, onSuccess, onFailed ]})

Transfers `REP` to the `reporter` based on how many `reportingToken`s the `reporter` owned. This transaction will fail if the `msg.sender` isn't the `reporter`, if the `market` isn't finalized, if the `market` isn't a container for `reportingToken`, if the `market` is the cause of a fork, or if the `reportingToken` isn't the winning `reportingToken`.
This transaction will spawn a `Transfer` event which will record the from address, to address, and the amount of `attotokens` of `REP` transferred to the `reporter`.

#### augur.api().ReportingToken.transfer({ reportingToken, destinationAddress, attotokens[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of the `transfer` transaction has enough of `reportingToken` to be able to transfer `attotokens` worth to the `destinationAddress` and `attotokens` is a valid value between 1 and 2<sup>254</sup> then this transaction will send `attotokens` worth of `reportingToken` to the specified `destinationAddress` from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address, to address, and `attotokens` amount transferred.

#### augur.api().ReportingToken.transferFrom({ reportingToken, sourceAddress, destinationAddress, attotokens[, onSent, onSuccess, onFailed ]})

If the `sourceAddress` of the `transferFrom` transaction has enough of `reportingToken` to be able to transfer `attotokens` worth to the `destinationAddress`, `attotokens` is a valid value between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `attotokens` worth of `reportingToken` for `sourceAddress` then this transaction will send `attotokens` worth of `registrationToken` to the specified `destinationAddress` from the `sourceAddress`. This transaction will spawn a `Transfer` event which will record the from address, to address, and `attotokens` amount transferred.

```javascript
// Reporting Window Contract
const reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a";
const endTime = 1501015000;
const numOutcomes = '2';
const payoutDenominator = '2';
const feePerEthInWei = '10000000000000000';
const cashContract = "0xb85a75a008e15d134c8ba01679ce2ab82dd7f777";
const creator = "0xab11204cfeaccffa63c2d23aef2ea9accdb0a0d5";
const minDisplayPrice = "0";
const maxDisplayPrice = "1";
const automatedReporterAddress = "0x01dcd72e4bed9ecba84f1749b139ae4338b30ce0";
const topic = "examples";

augur.api().ReportingWindow.createNewMarket({
  reportingWindow,
  endTime,
  numOutcomes,
  payoutDenominator,
  feePerEthInWei,
  denominationToken: cashContract,
  creator,
  minDisplayPrice,
  maxDisplayPrice,
  automatedReporterAddress,
  topic,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320502,
  callReturn: "0x5e3b08cd8a3b909e4396eda0818d5b1e4f43d4da",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x8de51be2ae9140e6db01a97b8578419e8deb542c7de2a615ced10f282bce3cc8",
  input: "0x55d619ab000000000000000000000000000000000000000000000000000000005977abd800000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000000000000000000002000000000000000000000000000000000000000000000000002386f26fc10000000000000000000000000000b85a75a008e15d134c8ba01679ce2ab82dd7f777000000000000000000000000ab11204cfeaccffa63c2d23aef2ea9accdb0a0d50000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000100000000000000000000000001dcd72e4bed9ecba84f1749b139ae4338b30ce0000000000000000000000000000000000000000000000000000000000a62cd64",
  nonce: "0xf1",
  timestamp: 1501003145,
  to: "0x06cbcd92af2571f1419b622a794d65db524f682a",
  value: "0x0"
}
```
### [Reporting Window Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reportingWindow.se)

#### augur.api().ReportingWindow.createNewMarket({ reportingWindow, endTime, numOutcomes, payoutDenominator, feePerEthInWei, denominationToken, creator, minDisplayPrice, maxDisplayPrice, automatedReporterAddress, topic[, onSent, onSuccess, onFailed ]})

This function will create a new market for the given `reportingWindow` that will be constructed using the arguments passed and return the new market's address if successful. This transaction will fail if the `numOutcomes` value isn't within the range of 2 and 8, if the `payoutDenominator` isn't between 2 and 2<sup>254</sup>, if the current time is not before the start time of the `reportingWindow`, if the `payoutDenominator` isn't a multiple of `numOutcomes`, if `feesPerEthInWei` is lower than or equal to 0 or greater than or equal to 0.5 ETH (5 * 10<sup>18</sup>), if the `maxDisplayPrice` and `minDisplayPrice` isn't between -2<sup>254</sup> to 2<sup>254</sup>, if  `maxDisplayPrice` - `minDisplayPrice` must be between 1 and 2<sup>254</sup>, or if the `msg.value` amount sent isn't enough to cover the market's validity bond and the estimated gas cost for the target amount of reporters to report.

```javascript
// Reputation Token Contract
const reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
const spender = "0xea674fdde714fd979de3edf0f56aa9716b898ec8";
const source = "0x1a05071893b764109f0bbc5b75d78e3e38b69ab3";
const attotokens = "100000000000000000000";

augur.api().ReputationToken.approve({
  reputationToken,
  spender,
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320512,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x915f8f0b13244b0dd9b7377b252d9245ef0fc109c82931a87410d1bdad671fe6",
  input: "0x83b58638000000000000000000000000ea674fdde714fd979de3edf0f56aa9716b898ec80000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xf2",
  timestamp: 1501003152,
  to: "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e",
  value: "0x0"
}

augur.api().ReputationToken.migrateFromLegacyRepContract({
  reputationToken,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320513,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xf1eb499764e9422d51a37b3567bb2513c022d63524518d520a9d4eabad0f0238",
  input: "0x3a15e7ca",
  nonce: "0xf3",
  timestamp: 1501003153,
  to: "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e",
  value: "0x0"
}

const targetReputationToken = "0x73295d3c0ca46113ca226222c81c79adabf9f391";
augur.api().ReputationToken.migrateOut({
  reputationToken,
  destination: targetReputationToken,
  reporter: source,
  attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320514,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xeddcfa199671312d7dd90eba2895da3104801ab05220758c62bbc6ef059f6057",
  input: "0x5ca7a72700000000000000000000000073295d3c0ca46113ca226222c81c79adabf9f3910000000000000000000000001a05071893b764109f0bbc5b75d78e3e38b69ab30000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xf4",
  timestamp: 1501003154,
  to: "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e",
  value: "0x0"
}

augur.api().ReputationToken.transfer({
  reputationToken,
  to: spender,
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320515,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x8f92137eff5e7824423ff6e79e15188b61d9dd9244fd2c436b020de6d8e721fe",
  input: "0x86744558000000000000000000000000ea674fdde714fd979de3edf0f56aa9716b898ec80000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xf5",
  timestamp: 1501003155,
  to: "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e",
  value: "0x0"
}

augur.api().ReputationToken.transferFrom({
  reputationToken,
  from: source,
  to: spender,
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320516,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x2c678df877e01d343a4e7701b92dddcecafc095fd1e4d90423838cd73eadb7d7",
  input: "0x27f08b000000000000000000000000001a05071893b764109f0bbc5b75d78e3e38b69ab3000000000000000000000000ea674fdde714fd979de3edf0f56aa9716b898ec80000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xf6",
  timestamp: 1501003156,
  to: "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e",
  value: "0x0"
}
```
### [Reputation Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reputationToken.sol)

#### augur.api().ReputationToken.approve({ reputationToken, spender, value[, onSent, onSuccess, onFailed ]})

Allows the `spender` the ability to spend up to `value` (denoted in attotokens) worth of the specified `reputationToken` for the `msg.sender` of this `approve` transaction. This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `spender`, and `value` in attotokens approved.

#### augur.api().ReputationToken.migrateFromLegacyRepContract({ reputationToken[, onSent, onSuccess, onFailed ]})

This function will migrate REP tokens from the legacy rep contract owned by `msg.sender` to the `reputationToken` provided. `msg.sender` will add whatever `msg.sender`'s balance was for the legacy rep contract to the `reputationToken` contract.

#### augur.api().ReputationToken.migrateOut({ reputationToken, destination, reporter, attotokens[, onSent, onSuccess, onFailed ]})

This function migrates a `reporter`'s REP (amount of REP denoted in `attotokens`) from one `reputationToken` address to another (`destination`). The `msg.sender` of this transaction must be the `reporter` provided or the `msg.sender` must be approved to spend `attotokens` amount of REP for the `reporter` provided.

#### augur.api().ReputationToken.transfer({ reputationToken, to, value[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of the `transfer` transaction has enough of `reputationToken` to be able to transfer `value` (denoted in attotokens) worth to the `to` address and `value` is a number between 1 and 2<sup>254</sup> then this transaction will send `value` (in attotokens) worth of `reputationToken` to the specified `to` address from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address (`msg.sender`), `to` address, and `value` amount transferred.

#### augur.api().ReputationToken.transferFrom({ reputationToken, from, to, value[, onSent, onSuccess, onFailed ]})

If the `from` address of the `transferFrom` transaction has enough of `reputationToken` to be able to transfer `value` (denoted in attotokens) worth to the `to` address, `value` is a number between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `value` worth of `reputationTokens` for the `from` address then this transaction will send `value` worth of `reputationToken` to the specified `to` address from the `from` address. This transaction will spawn a `Transfer` event which will record the `from` address, `to` address, and `value` (in attotokens) amount transferred.

```javascript
// Cash Token Contract
const owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";
const attotokens = "100000000000000000000";

augur.api().Cash.approve({
  spender,
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320517,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x13c698a48dcee8a3680e8ff1b767cf3327b2dec516355de7112b23a1031dcd70",
  input: "0x83b58638000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xf7",
  timestamp: 1501003157,
  to: "0xa8f769b88d6d74fb2bd3912f6793f75625228baf",
  value: "0x0"
}

augur.api().Cash.publicDepositEther({
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320518,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xe5e6737ec3fd36c7147cc9af910d96a6e41977e1c2a491205b7a006dbf33c4ff",
  input: "0xf81de66a",
  nonce: "0xf8",
  timestamp: 1501003158,
  to: "0xa8f769b88d6d74fb2bd3912f6793f75625228baf",
  value: "0x0"
}

augur.api().Cash.publicWithdrawEther({
  to: owner,
  amount: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320519,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xe340a2b59c774d3e297e4728be09d96e1cd5579551cdde6966c0bd23ce764fb2",
  input: "0x25d48493000000000000000000000000438f2aeb8a16745b1cd711e168581ebce744ffaa0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xf9",
  timestamp: 1501003159,
  to: "0xa8f769b88d6d74fb2bd3912f6793f75625228baf",
  value: "0x0"
}

augur.api().Cash.transfer({
  to: spender,
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320520,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xa29b95168d8ee80bc940fdd9ddc68b9c3f2f94cb688d613719da58819c65673a",
  input: "0x86744558000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xfa",
  timestamp: 1501003120,
  to: "0xa8f769b88d6d74fb2bd3912f6793f75625228baf",
  value: "0x0"
}

augur.api().Cash.transferFrom({
  from: owner,
  to: spender,
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320521,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xb6973356d29c63f559093ec2d732a70fbfe4085736d04c0496e2b1abd45f53d5",
  input: "0x27f08b00000000000000000000000000438f2aeb8a16745b1cd711e168581ebce744ffaa000000000000000000000000fe9d0408be14d1d1ec28671b03bda1b80748977e0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xfb",
  timestamp: 1501003121,
  to: "0xa8f769b88d6d74fb2bd3912f6793f75625228baf",
  value: "0x0"
}
```
### [Cash Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/Cash.sol)

#### augur.api().Cash.approve({ spender, value[, onSent, onSuccess, onFailed ]})

Allows the `spender` the ability to spend up to `value` (denoted in attotokens) worth of Cash Tokens for the `msg.sender` of this `approve` transaction. This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `spender`, and `value` in attotokens approved.

#### augur.api().Cash.publicDepositEther({ value[, onSent, onSuccess, onFailed ]})

This transaction is used to convert Ether (ETH) into a Cash token that is used on the augur markets. `value` is the amount of Ether (ETH) denoted in attotokens to deposit into the Cash Token Contract for the `msg.sender`. This will spawn a `DepositEther` event which will record the owner address (`msg.sender`), `value` deposited, and the total balance for this `msg.sender`.

#### augur.api().Cash.publicWithdrawEther({ to, amount[, onSent, onSuccess, onFailed ]})

This transaction is used to convert Cash Tokens back into Ether (ETH) by sending the `amount` of `msg.sender`'s CASH Tokens `to` the address specified denoted in attotokens. This transaction requires a 3-day wait period from the initial call to withdraw. Once three days have passed, calling `publicWithdrawEther` again will withdraw the `amount` specified. This transaction will fail if we have initiated a withdraw but it hasn't been 3 days since the initiated withdraw, if the `msg.sender` doesn't have at least `amount` of Cash Tokens denoted in attotokens, or if the `amount` specified is less than 1. This transaction can spawn two different events depending on when it was called. If a withdraw hasn't been initiated then calling `publicWithdrawEther` will spawn a `InitiateWithdrawEther` event which records the `msg.sender`, the `amount` specified for withdraw, and the current balance of Cash Tokens for the `msg.sender`. If it has been at least 3 days since we have initiated a withdraw then when the withdraw takes place, this transaction will spawn a `WithdrawEther` event. The `WithdrawEther` event records `msg.sender`, the `amount` withdrawn, and the balance of Cash Tokens after withdrawal has been completed.

#### augur.api().Cash.transfer({ to, value[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of the `transfer` transaction has enough of Cash Tokens to be able to transfer `value` (denoted in attotokens) worth to the `to` address and `value` is a number between 1 and 2<sup>254</sup> then this transaction will send `value` (in attotokens) worth of Cash Tokens to the specified `to` address from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address (`msg.sender`), `to` address, and `value` amount transferred.

#### augur.api().Cash.transferFrom({ from, to, value[, onSent, onSuccess, onFailed ]})

If the `from` address of the `transferFrom` transaction has enough Cash Tokens to be able to transfer `value` (denoted in attotokens) worth to the `to` address, `value` is a number between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `value` worth of Cash Tokens for the `from` address then this transaction will send `value` worth of Cash Tokens to the specified `to` address from the `from` address. This transaction will spawn a `Transfer` event which will record the `from` address, `to` address, and `value` (in attotokens) amount transferred.


Legacy Transaction API
----------------------
```javascript
// faucets contract
var branch = augur.branches.dev;
augur.reputationFaucet({
  branch: branch,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: '0x7aeeacf586d3afa89dc7ca41a4df52307d1a91c33b8726552d3f84b041b5850e',
  callReturn: '1'
}
successResponse = {
  nonce: '0x4e4',
  blockHash: '0x1fb89b98f59e8a1803556e99c6e2772992ad9bd199e638ccf5e9bff9aa595ffe',
  blockNumber: '0x6ab8',
  transactionIndex: '0x0',
  from: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  to: '0x509592c96eee7e19f6a34772fd8783cb072ca3c6',
  value: '0x0',
  gas: '0x2fd618',
  gasPrice: '0xba43b7400',
  input: '0x988445fe00000000000000000000000000000000000000000000000000000000000f69b5',
  callReturn: '1',
  txHash: '0x7aeeacf586d3afa89dc7ca41a4df52307d1a91c33b8726552d3f84b041b5850e'
}
```
### [faucets contract](https://github.com/AugurProject/augur-core/blob/master/src/functions/faucets.se)
#### reputationFaucet(branch[, onSent, onSuccess, onFailed])

Sets the Reputation balance of the sending account to 47.  (Only available during testing, of course!)

```javascript
// cash contract
augur.depositEther({
  value: 2,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: '0x2cb4c0f6796b102fb6b1dead8cb85d91f2af330057eb69a6f6f9814617d04a56',
  callReturn: '2000000000000000000'
}
successResponse = {
  nonce: '0x4e8',
  blockHash: '0x81cfb01f897dd7df9ca898f4fd232f5c0957f59596a35b1ef552e37516cdfc38',
  blockNumber: '0x6af0',
  transactionIndex: '0x0',
  from: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  to: '0xe4714fcbdcdba49629bc408183ef40d120700b8d',
  value: '0x1bc16d674ec80000',
  gas: '0x2fd618',
  gasPrice: '0xba43b7400',
  input: '0x98ea5fca',
  callReturn: '2000000000000000000',
  txHash: '0x2cb4c0f6796b102fb6b1dead8cb85d91f2af330057eb69a6f6f9814617d04a56'
}

augur.withdrawEther({
  to: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  value: 2,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: '0x3f44c75513667dee7dcf0a5f08e7be32751fbc2c5c52a8a29018682941ee4895',
  callReturn: '1'
}
successResponse = {
  nonce: '0x4e9',
  blockHash: '0xde4deb916d67631e63e8aed4fefec7839a09be9b3cd8b3914fe48d9751a159a4',
  blockNumber: '0x6af3',
  transactionIndex: '0x0',
  from: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  to: '0xe4714fcbdcdba49629bc408183ef40d120700b8d',
  value: '0x0',
  gas: '0x2fd618',
  gasPrice: '0xba43b7400',
  input: '0x698f6e9d00000000000000000000000005ae1d0ca6206c6168b42efcd1fbe0ed144e821b0000000000000000000000000000000000000000000000001bc16d674ec80000',
  callReturn: '1',
  txHash: '0x3f44c75513667dee7dcf0a5f08e7be32751fbc2c5c52a8a29018682941ee4895'
}
```
### [cash contract](https://github.com/AugurProject/augur-core/blob/master/src/data_api/cash.se)
#### depositEther(value[, onSent, onSuccess, onFailed])

Exchange `value` units of Ether for an equivalent number of CASH tokens.  Returns the amount sent (in Wei).

#### withdrawEther(to, value[, onSent, onSuccess, onFailed])

Exchange `value` CASH tokens for an equivalent number of Ether tokens.  The withdrawn CASH is sent to address `to`.  Returns 1 if successful, 0 otherwise.

```javascript
// claimMarketProceeds contract
var marketId = "0x6eb799feec7669d757ed7bcad178bab45d08bfbd1730f9bd6eb0cb507dfe07d4"
augur.claimProceeds({
  market: marketId,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
})
// example output:
onSent = {
  txHash: "0xf56ae53dddc1eddfa5ba8fb0931701033e2a39036bbb941001f000556496d110",
  hash: "0xf56ae53dddc1eddfa5ba8fb0931701033e2a39036bbb941001f000556496d110",
  callReturn: "1"
}
onSuccess = {
  blockHash: "0x0cde35b78f6102a59c07c0c4279166864e34518fd6672af988cd9224acdfc9c6",
  blockNumber: "0xe0ddb",
  from: "0x15f6400a88fb320822b689607d425272bea2175f",
  gas: "0x2fd618",
  gasPrice: "0x4a817c800",
  input: "0x0dd40dd86eb799feec7669d757ed7bcad178bab45d08bfbd1730f9bd6eb0cb507dfe07d4"
  nonce: "0x539",
  to: "0xa16ced61576483990d0d821a5fc344a3429ba755",
  transactionIndex: "0x0",
  value: "0x0",
  callReturn: "1",
  txHash: "0xf56ae53dddc1eddfa5ba8fb0931701033e2a39036bbb941001f000556496d110"
}
```
### [claimMarketProceeds contract](https://github.com/AugurProject/augur-core/blob/master/src/functions/claimMarketProceeds.se)
#### claimProceeds(market[, onSent, onSuccess, onFailed])

Claims the trading profits after a specified `market` is completed. If the market hasn't been resolved yet, then you will get a call return of `0`.

```javascript
// bidAndAsk contract
var marketId = "0xb196c4ce182399271e6ed434eb3f2210ae5e427c8ac0604c2cb2261694951d9";
var outcome = 1;
var type = 2; // ask, bid is 1
var amount = "10";
var price = "0.5";

augur.placeOrder({
  type: type,
  amount: amount,
  price: price,
  market: marketId,
  outcome: outcome,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: "0x24fa2a803f42253bbaa900af9badd32d75a728299e66ce9a0dce6a2a264307ca",
  hash: "0x24fa2a803f42253bbaa900af9badd32d75a728299e66ce9a0dce6a2a264307ca",
  callReturn: "0x5f80029d47ca806002b6b6bbbb0077124f8da9b69a885f7714907bd773bcf8a7"
}
successResponse = {
  blockHash: "0x0cde35b78f6102a59c07c0c4279166864e34518fd6672af988cd9224acdfc9c6",
  blockNumber: "0xe0ddb",
  from: "0x15f6400a88fb320822b689607d425272bea2175f",
  gas: "0x2fd618",
  gasPrice: "0x4a817c800",
  input: "0x62be772100000000000000000000000000000000000000000000000000000000000000020000000000000000000000000000000000000000000000008ac7230489e8000000000000000000000000000000000000000000000000000006f05b59d3b200000b196c4ce182399271e6ed434eb3f2210ae5e427c8ac0604c2cb2261694951d90000000000000000000000000000000000000000000000000000000000000001",
  nonce: "0x1006ff",
  to: "0xa16ced61576483990d0d821a5fc344a3429ba755",
  transactionIndex: "0x0",
  value: "0x0",
  callReturn: "0x5f80029d47ca806002b6b6bbbb0077124f8da9b69a885f7714907bd773bcf8a7",
  txHash: "0x24fa2a803f42253bbaa900af9badd32d75a728299e66ce9a0dce6a2a264307ca"
}

var orderID = "0x5f80029d47ca806002b6b6bbbb0077124f8da9b69a885f7714907bd773bcf8a7";

augur.cancel({
  orderID: orderID
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: "0x134f4c425e132f1eed2be6aa21f55b258e22c589d88a93f198e73e9d6d938261",
  hash: "0x134f4c425e132f1eed2be6aa21f55b258e22c589d88a93f198e73e9d6d938261",
  callReturn: "1"
}
successResponse = {
  blockHash: "0x0cde35b78f6102a59c07c0c4279166864e34518fd6672af988cd9224acdfc9c6",
  blockNumber: "0xe0ddb",
  from: "0x15f6400a88fb320822b689607d425272bea2175f",
  gas: "0x2fd618",
  gasPrice: "0x4a817c800",
  input: "0x327a22f15f80029d47ca806002b6b6bbbb0077124f8da9b69a885f7714907bd773bcf8a7",
  nonce: "0x1006ff",
  to: "0xa16ced61576483990d0d821a5fc344a3429ba755",
  transactionIndex: "0x0",
  value: "0x0",
  callReturn: "1",
  txHash: "0x134f4c425e132f1eed2be6aa21f55b258e22c589d88a93f198e73e9d6d938261"
}
```
### [bidAndAsk contract](https://github.com/AugurProject/augur-core/blob/master/src/functions/bidAndAsk.se)
#### placeOrder(type, fxpAmount, fxpPrice, market, outcome[, onSent, onSuccess, onFailed])

Place a bid or ask order onto the order book. These orders can be filled using the `trade` method described in the `trade` contract section. `placeOrder` will fail if the market doesn't exist, the `fxpAmount` or `fxpPrice` are invalid values, or if this is an oracle only branch. The minimum order allowed is 0.00000001.

#### cancel(orderID[, onSent, onSuccess, onFailed])

Cancel an order you have placed on the order book. Returns shares if the order was an `ask`, refunds money if the order was a `bid`. Returns `1` on success, `0` on failure.

```javascript
// trade contract
var orderID = "0x5f80029d47ca806002b6b6bbbb0077124f8da9b69a885f7714907bd773bcf8a7";
var amountTakerWants = "10";

augur.trade({
  orderID: orderID,
  amountTakerWants: amountTakerWants,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: "0x1437c2e44379169076db42d12fb4f0fab1d330f84ab152900a703ee13f814bf7",
  hash: "0x1437c2e44379169076db42d12fb4f0fab1d330f84ab152900a703ee13f814bf7",
  callReturn: "0x8ac7230489e80000"
}
successResponse = {
  blockHash: "0x3f19ec7209de63043cf5031bb4a38df4bcf6e610f6874dfac87e7ad42e830708",
  blockNumber: "0xe10e0",
  from: "0x15f6400a88fb320822b689607d425272bea2175f",
  gas: "0x2fd618",
  gasPrice: "0x4a817c800",
  input: "0x09d600695f80029d47ca806002b6b6bbbb0077124f8da9b69a885f7714907bd773bcf8a7000000000000000000000000000000000000000000000000000000000000000a",
  nonce: "0x10074f",
  to: "0xb44cd937904ba0e309204ffc1413e094f2a84ee5",
  transactionIndex: "0x0",
  value: "0x0",
  callReturn: "0x8ac7230489e80000",
  txHash: "0x1437c2e44379169076db42d12fb4f0fab1d330f84ab152900a703ee13f814bf7"
}
```
### [trade contract](https://github.com/AugurProject/augur-core/blob/master/src/functions/trade.se)
#### trade(orderID, amountTakerWants[, onSent, onSuccess, onFailed])

Used to fill orders off of the order book. `OrderID` is the order we plan to fill, `amountTakerWants` is how much of the order to fill. This can fail if the `orderID` provided doesn't exist, if the order hash is bad, the order is attempting to be filled in the same block it was placed on the book, or if you are attempting to fill your own order.

```javascript
// createBranch contract
augur.createSubbranch({
  description: "bestBranch:Best branch ever!",
  periodLength: 172800,
  parent: augur.branches.dev,
  tradingFee: "0.02",
  oracleOnly: 0,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: "0x42502d8c16816d6488d0b02ad102af88dbf7c21cf096e37678a4a5ff03a9d9b9",
  hash: "0x42502d8c16816d6488d0b02ad102af88dbf7c21cf096e37678a4a5ff03a9d9b9",
  callReturn: "0x42502d8c16816d6488d0b02ad102af88dbf7c21cf096e37678a4a5ff03a9d9b9"
}
successResponse = {
  blockHash: "0x37884ebda00a4fea5f8aaac05d6b72279b29ec8a7970f3cfbf86a888c37de9d7",
  blockNumber: 37735,
  callReturn: "0x42502d8c16816d6488d0b02ad102af88dbf7c21cf096e37678a4a5ff03a9d9b9",
  from: "0x61badf138090eb57cac69a595374090ef7b76f86",
  gas: "0x2fd618",
  gasFees: "0.00749788",
  gasPrice: "0x4a817c800",
  hash: "0x27651b1e12ff3042cafc295da8ca0a7fb82ec9801432e2870107b27c7c97aa03",
  input: "0x177cff2900000000000000000000000000000000000000000000000000000000000000a0000000000000000000000000000000000000000000000000000000000002a30000000000000000000000000000000000000000000000000000000000000f69b500000000000000000000000000000000000000000000000002c68af0bb1400000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000001c626573744272616e63683a42657374206272616e636820657665722100000000",
  nonce: "0x8",
  r: "0x85fea59738f2b52f7a7f2e5525fc05c28760da67bea41aee11e836dddbc5ad64",
  s: "0x7ce88e84f1e89dc887535bc3549303a2d69c21a7d044db59f7261d7a1ea3583e",
  timestamp: 1489855429,
  to: "0x9fe69262bbaa47f013b7dbd6ca5f01e17446c645",
  transactionIndex: "0x0",
  v: "0x1c",
  value: "0x0"
}
```
### [createBranch contract](https://github.com/AugurProject/augur-core/blob/master/src/functions/createBranch.se)
#### createSubbranch(description, periodLength, parent, minTradingFee, oracleOnly[, onSent, onSuccess, onFailed])

Creates a new sub-branch of branch `parent`.  The description format is branchName:description.  `periodLength` is given in seconds. `oracleOnly` is given as 0 or 1 to set the new branch as an oracle only branch. Returns the new branch's ID if successful as `blockHash`.  Otherwise, returns -1 if there is a bad input or the parent doesn't exist, -2 if there is insufficient funds to create the branch or if the branch already exists.

```javascript
// sendReputation contract
augur.sendReputation({
  branchId: augur.branches.dev,
  to: "0x0da70d5a92d6cfcd4c12e2a83950676fdf4c95f9",
  value: 10,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: '0x1460b261654f24bf0bbb436f1766b68a94f476c57d4998e26da75114f56cc5a8',
  callReturn: '10'
}
successResponse = {
  nonce: '0x4f1',
  blockHash: '0x2ad3f1071758d9efcb4d2458236128fdaa76585c3c4d551f50c26d2a50a2e38f',
  blockNumber: '0x6b97',
  transactionIndex: '0x0',
  from: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  to: '0x35152caa07026203a1add680771afb690d872d7d',
  value: '0x0',
  gas: '0x2fd618',
  gasPrice: '0xba43b7400',
  input: '0xa677135c00000000000000000000000000000000000000000000000000000000000f69b50000000000000000000000000da70d5a92d6cfcd4c12e2a83950676fdf4c95f900000000000000000000000000000000000000000000000a0000000000000000',
  callReturn: '10',
  txHash: '0x1460b261654f24bf0bbb436f1766b68a94f476c57d4998e26da75114f56cc5a8'
}
```
### [sendReputation contract](https://github.com/AugurProject/augur-core/blob/master/src/functions/sendReputation.se)
#### sendReputation(branchId, to, value[, onSent, onSuccess, onFailed])

Sends `value` Reputation tokens on branch `branchId` to address `to`.  Returns the value sent if successful.

```javascript
// makeReports contract
var event = "0x7cbcc157062d19bf53daac10c98516c587925f0b4848240f690cc4e43ef5dcac";
var fxpReport = "0x1bc16d674ec80000"; // 2
var salt = "0xb3017088d3de23f9611dbf5d23773b5ad38621bab84aa79a0621c8800aeb4c33";
var sender = "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b";
var fxpEthics = "0xde0b6b3a7640000"; // 1
augur.makeHash({
  salt: salt,
  fxpReport: fxpReport,
  event: event,
  sender: sender,
  ethics: fxpEthics
})
// example output:
reportHash = "0x543ea8a255d3e8e92a08cf7d61acd7d79d71f4256d0be5f2d450a2b3a5442cd0"

augur.submitReport({
  event: event,
  salt: salt,
  fxpReport: fxpReport,
  fxpEthics: fxpEthics,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: "0x81635384e4fd7c0b36ed49901cd2c3f03577a287c030de55bcd6f0ddb739919c",
  hash: "0x81635384e4fd7c0b36ed49901cd2c3f03577a287c030de55bcd6f0ddb739919c",
  callReturn: "1"
}
successResponse = {
  nonce: "0x4f1",
  blockHash: "0x2ad3f1071758d9efcb4d2458236128fdaa76585c3c4d551f50c26d2a50a2e38f",
  blockNumber: "0x6b97",
  transactionIndex: "0x0",
  from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
  to: "0x35152caa07026203a1add680771afb690d872d7d",
  value: "0x0",
  gas: "0x2fd618",
  gasPrice: "0xba43b7400",
  input: "0x5b5156367cbcc157062d19bf53daac10c98516c587925f0b4848240f690cc4e43ef5dcacb3017088d3de23f9611dbf5d23773b5ad38621bab84aa79a0621c8800aeb4c330000000000000000000000000000000000000000000000001bc16d674ec800000000000000000000000000000000000000000000000000000de0b6b3a7640000",
  callReturn: "1",
  txHash: "0x81635384e4fd7c0b36ed49901cd2c3f03577a287c030de55bcd6f0ddb739919c"
}
var key = "this is a secret"; // generally this would be the logged in accounts secret key, for this example we are using a string.
var saltyEncryptedHash = augur.encryptReport(fxpReport, key, salt);
augur.submitReportHash({
  event: event,
  reportHash: reportHash,
  saltyEncryptedHash: saltyEncryptedHash,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: "0x3625055027664b92890677a3cd109edecc9d80f6451082b94cb641f688baa426",
  hash: "0x3625055027664b92890677a3cd109edecc9d80f6451082b94cb641f688baa426",
  callReturn: "1"
}
successResponse = {
  nonce: "0x4f1",
  blockHash: "0x2ad3f1071758d9efcb4d2458236128fdaa76585c3c4d551f50c26d2a50a2e38f",
  blockNumber: "0x6b97",
  transactionIndex: "0x0",
  from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
  to: "0x35152caa07026203a1add680771afb690d872d7d",
  value: "0x0",
  gas: "0x2fd618",
  gasPrice: "0xba43b7400",
  input: "0xd96945b9b3017088d3de23f9611dbf5d23773b5ad38621bab84aa79a0621c8800aeb4c33543ea8a255d3e8e92a08cf7d61acd7d79d71f4256d0be5f2d450a2b3a5442cd03de5f80e01c94760e70db1d4aa455096a76cfb85f7e9b55584079a76308ee37b",
  callReturn: "1",
  txHash: "0x3625055027664b92890677a3cd109edecc9d80f6451082b94cb641f688baa426"
}
```
### [makeReports contract](https://github.com/AugurProject/augur-core/blob/master/src/functions/makeReports.se)
#### makeHash(salt, fxpReport, event, sender, ethics)

Creates the `reportHash` which is a sha3 hash of `salt`, `fxpReport`, `event`, `sender`, and `ethics` and returns the `reportHash`.

#### submitReport(event, salt, fxpReport, fxpEthics[, onSent, onSuccess, onFailed])

Used to reveal/submit a report in the second half of the reporting cycle for a specific `event` that you have already committed a `reportHash` for in the first half of a reporting cycle.

#### submitReportHash(event, reportHash, saltyEncryptedHash[, onSent, onSuccess, onFailed])

Used to commit/submit a `reportHash` for an `event` during the first half of the reporting cycle.

```javascript
// slashRep contract
var branch = augur.branches.dev;
var salt = "0xb3017088d3de23f9611dbf5d23773b5ad38621bab84aa79a0621c8800aeb4c33";
var report = "1";
var ethics = "1";
var reporter = "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b";
var event = "0x7cbcc157062d19bf53daac10c98516c587925f0b4848240f690cc4e43ef5dcac";

augur.slashRep({
  branch: branch,
  salt: salt,
  report: report,
  ethics: ethics,
  reporter: reporter,
  event: event,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: "0x8b3c5d4276d370648e55563bcaab3219c8d961abf513ecd835df89ee63e85524",
  hash: "0x8b3c5d4276d370648e55563bcaab3219c8d961abf513ecd835df89ee63e85524",
  callReturn: "1"
}
successResponse = {
  nonce: "0x4f1",
  blockHash: "0x2ad3f1071758d9efcb4d2458236128fdaa76585c3c4d551f50c26d2a50a2e38f",
  blockNumber: "0x6b97",
  transactionIndex: "0x0",
  from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
  to: "0x35152caa07026203a1add680771afb690d872d7d",
  value: "0x0",
  gas: "0x2fd618",
  gasPrice: "0xba43b7400",
  input: "0x8a698a9b00000000000000000000000000000000000000000000000000000000000f69b5b3017088d3de23f9611dbf5d23773b5ad38621bab84aa79a0621c8800aeb4c33f57a578a48826896505e591fb940f52bf627e2159a399a033067d963bc124b0b000000000000000000000000000000000000000000000000000000000000000100000000000000000000000005ae1d0ca6206c6168b42efcd1fbe0ed144e821b7cbcc157062d19bf53daac10c98516c587925f0b4848240f690cc4e43ef5dcac",
  callReturn: "1",
  txHash: "0x8b3c5d4276d370648e55563bcaab3219c8d961abf513ecd835df89ee63e85524"
}
```
### [slashRep contract](https://github.com/AugurProject/augur-core/blob/master/src/functions/slashRep.se)
#### slashRep(branch, salt, report, ethics, reporter, event[, onSent, onSuccess, onFailed])

Slashes the Reputation of address `reporter` for `report` on branch `branchId`.

```javascript
// createMarket contract
var description = "What will the high temperature (in degrees Fahrenheit) be in San Francisco, California, on July 1, 2016?";
augur.createEvent({
  branch: augur.branches.dev,
  description: description,
  expDate: new Date("7/2/2016").getTime(),
  minValue: 0,
  maxValue: 120,
  numOutcomes: 2,
  resolution: "www.weather.com",
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: "0xc9b92f0224fee5f28c35f49c8b3015e5a09a4876ebb819c1abb5c08ba47c01c6",
  callReturn: "0xce75eb6f37b92fa80df3240ea8528f7c4b70c8553a536275e26f36a87f02518"
}
successResponse = {
  blockHash: "0x30782f7d4dda3be6e9823deb64a926556b9a508bbef76f4d7bb0e69a4940e59f",
  blockNumber: "0xe287e",
  from: "0x15f6400a88fb320822b689607d425272bea2175f",
  gas: "0x2fd618",
  gasPrice: "0x4a817c800",
  input: "0x3c1f6f6300000000000000000000000000000000000000000000000000000000000f69b500000000000000000000000000000000000000000000000000000000000000e000000000000000000000000000000000000000000000000000000155aa68258000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000780000000000000000000000000000000000000000000000000000000000000000000000000000000200000000000000000000000000000000000000000000000000000000000001800000000000000000000000000000000000000000000000000000000000000068576861742077696c6c2074686520686967682074656d70657261747572652028696e20646567726565732046616872656e686569742920626520696e2053616e204672616e636973636f2c2043616c69666f726e69612c206f6e204a756c7920312c20323031363f000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000f7777772e776561746865722e636f6d0000000000000000000000000000000000",
  nonce: "0x100751",
  to: "0x24b051c19bd981a59f6c459c128a285bcd8d66bc",
  transactionIndex: "0x1",
  value: "0x0",
  callReturn: "0xce75eb6f37b92fa80df3240ea8528f7c4b70c8553a536275e26f36a87f02518",
  txHash: "0xc9b92f0224fee5f28c35f49c8b3015e5a09a4876ebb819c1abb5c08ba47c01c6"
}

augur.createMarket({
  branch: augur.branches.dev,
  description: description,
  tradingFee: "0.05",
  event: "0xce75eb6f37b92fa80df3240ea8528f7c4b70c8553a536275e26f36a87f02518",
  tags: ["example tag", "other example tag", "nonsense"],
  extraInfo: "An even longer description / link to more info!",
  currency: "0x48c80F1f4D53D5951e5D5438B54Cba84f29F32a5",
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sendResponse = {
  txHash: "0x643462835b9899318ead8f47a1c43232b04a1dfeda8fba213e8c3d8a0c4651e0",
  hash: "0x643462835b9899318ead8f47a1c43232b04a1dfeda8fba213e8c3d8a0c4651e0",
  callReturn: "-0x3bb8d91f2481d886fe94acd4d1ffe3339ec60524aeb55ceb5a6c6c8631a796c2"
}
successResponse = {
  nonce: "0x535",
  blockHash: "0x60a299bc290af601300f2a17686947159073537884aa71fc18fe3628735c5f24",
  blockNumber: "0x72e7",
  transactionIndex: "0x0",
  from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
  to: "0x448c01a2e1fd6c2ef133402c403d2f48c99993e7",
  value: "0x0",
  gas: "0x2fd618",
  gasPrice: "0xba43b7400",
  input: "0xd705436d00000000000000000000000000000000000000000000000000000000000f69b5000000000000000000000000000000000000000000000000000000000000012000000000000000000000000000000000000000000000000000b1a2bc2ec500000ce75eb6f37b92fa80df3240ea8528f7c4b70c8553a536275e26f36a87f02518000000000000000000000000000000000000000000000000000000040e983be800000000000000000000000000000000000000000000000000099f896fc227e8000000000000000000000000000000000000000000000000000000000f4b5a7200000000000000000000000000000000000000000000000000000000000001c000000000000000000000000048c80f1f4d53d5951e5d5438b54cba84f29f32a50000000000000000000000000000000000000000000000000000000000000068576861742077696c6c2074686520686967682074656d70657261747572652028696e20646567726565732046616872656e686569742920626520696e2053616e204672616e636973636f2c2043616c69666f726e69612c206f6e204a756c7920312c20323031363f000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000002f416e206576656e206c6f6e676572206465736372697074696f6e202f206c696e6b20746f206d6f726520696e666f210000000000000000000000000000000000",
  callReturn: "-0x3bb8d91f2481d886fe94acd4d1ffe3339ec60524aeb55ceb5a6c6c8631a796c2",
  txHash: "0x643462835b9899318ead8f47a1c43232b04a1dfeda8fba213e8c3d8a0c4651e0"
}

var marketId = "-0x3bb8d91f2481d886fe94acd4d1ffe3339ec60524aeb55ceb5a6c6c8631a796c2";
augur.pushMarketForward({
  branch: augur.branches.dev,
  market: marketId,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
})
// example output:
onSent = {
  txHash: "0x96fcfee8dbbd0ceda0899c84bb44e7304924f48beb808d0eb2a759225efd4b20",
  hash: "0x96fcfee8dbbd0ceda0899c84bb44e7304924f48beb808d0eb2a759225efd4b20",
  callReturn: "1"
}
onSuccess = {
  nonce: "0x536",
  blockHash: "0x60a299bc290af601300f2a17686947159073537884aa71fc18fe3628735c5f24",
  blockNumber: "0x72e7",
  transactionIndex: "0x0",
  from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
  to: "0x448c01a2e1fd6c2ef133402c403d2f48c99993e7",
  value: "0x0",
  gas: "0x2fd618",
  gasPrice: "0xba43b7400",
  input: "0x2cec448100000000000000000000000000000000000000000000000000000000000f69b5fffffffffcd38ec2ec299f575e0d04c085fea28310707fcbd1034bf6ed524a96",
  callReturn: "1",
  txHash: "0x96fcfee8dbbd0ceda0899c84bb44e7304924f48beb808d0eb2a759225efd4b20"
}

augur.updateTradingFee({
  market: marketId,
  tradingFee: "0.03",
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
})
// example output:
onSent = {
  txHash:"0x3e743824454e182e34e41b6cae724e9531946043891026e8f77acedc89de9a86",
  hash: "0x3e743824454e182e34e41b6cae724e9531946043891026e8f77acedc89de9a86",
  callReturn: "1"
}
onSuccess = {
  nonce: "0x537",
  blockHash: "0x60a299bc290af601300f2a17686947159073537884aa71fc18fe3628735c5f24",
  blockNumber: "0x72e7",
  transactionIndex: "0x0",
  from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
  to: "0x448c01a2e1fd6c2ef133402c403d2f48c99993e7",
  value: "0x0",
  gas: "0x2fd618",
  gasPrice: "0xba43b7400",
  input: "0xba27370cfffffffffcd38ec2ec299f575e0d04c085fea28310707fcbd1034bf6ed524a96000000000000000000000000000000000000000000000000006a94d74f430000",
  callReturn: "1",
  txHash: "0x3e743824454e182e34e41b6cae724e9531946043891026e8f77acedc89de9a86"
}
```
### [createMarket contract](https://github.com/AugurProject/augur-core/blob/master/src/functions/createMarket.se)
#### createEvent(branch, description, expDate, minValue, maxValue, numOutcomes, resolution[, onSent, onSuccess, onFailed])

Creates an event on branch ID `branch` with `description`, expiration date (in epoch time) of `expDate`, minimum value `minValue`, maximum value `maxValue`, `numOutcomes` possible outcomes, and `resolution` source for resolution data (e.g., `resolution` might be set to `https://www.wunderground.com/history` for a weather-related event).

#### createMarket(branch, description, tradingFee, event, tags, extraInfo, currency[, onSent, onSuccess, onFailed])

Creates a market on branch ID `branch` with `description`, trading fee % paid on winnings or closing short positions is the `tradingFee`, topics/categories `tags`, and more detailed description and/or link to more info `extraInfo`, and containing event ID supplied in `event`, the `currency` is the address of the token we plan to use for trading shares on this market.

#### pushMarketForward(branch, market[, onSent, onSuccess, onFailed])

Pushes up a specified `market` on a `branch` so that it is reported on in the next reporting period instead of when it was scheduled to be reported on. The caller of this method will post a bond to pay out reporters in the case that this market isn't reportable after it's been moved up. The bond is equal to `0.5 * marketFee * marketValue`. This will only succeed if the `branch` is valid, the `market` isn't already closed or resolved, and the event for the `market` isn't already pushed forward, being reported on currently, or already has an outcome.

#### updateTradingFee(market, tradingFee[, onSent, onSuccess, onFailed])

Updates the specified `market`'s trading fee `tradingFee` to be *lower* then the current trading fee. This update will fail if the `tradingFee` is greater than the currently set trading fee on the market or the new trading fee is less than the minimum trading fee for the branch that market lives on.

```javascript
// closeMarket contract
var marketId = "0x51d1e6ed9e072dad3d6daf1dc92ff2993884dc35db5b8a3fda8f28ceef4abd6";
var sender = "0x265a416766e1525867f72756ed8fd16e18044d97";
augur.closeMarket({
  marketId: marketId,
  sender: sender,
  onSent: function (sentResponse) { /* ... */ },
  onSuccess: function (successResponse) { /* ... */ },
  onFailed: function (failedResponse) { /* ... */ }
});
// example outputs:
sentResponse = {
  txHash: "0x2b48ff35e52c9963503d573c15b559c53e6b34e2ba8f1be3d4d63709239bd8f2",
  hash: "0x2b48ff35e52c9963503d573c15b559c53e6b34e2ba8f1be3d4d63709239bd8f2",
  callReturn: "1"
}
successResponse = {
  nonce: "0x536",
  blockHash: "0x107c03ea39f081c462db612b7acbb8c6e3abfbdeb16fb7f2e27a680590092f9e",
  blockNumber: "0x7301",
  transactionIndex: "0x0",
  from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
  to: "0xcece47d6c0a6a1c90521f38ec5bf7550df983804",
  value: "0x0",
  gas: "0x2fd618",
  gasPrice: "0xba43b7400",
  input: "0x60aea93e051d1e6ed9e072dad3d6daf1dc92ff2993884dc35db5b8a3fda8f28ceef4abd6000000000000000000000000265a416766e1525867f72756ed8fd16e18044d97",
  callReturn: "1",
  txHash: "0x2b48ff35e52c9963503d573c15b559c53e6b34e2ba8f1be3d4d63709239bd8f2"
}
```
### [closeMarket contract](https://github.com/AugurProject/augur-core/blob/master/src/functions/closeMarket.se)
#### closeMarket(marketId, sender[, onSent, onSuccess, onFailed])

Closes market with ID `marketId` and refunds the closing cost to the specified address `sender`. Returns `1` if successful. Will fail if the market hasn't been reported on and resolved or if the market is already closed.

Invoke
------

In some cases, you may need more flexibility beyond simply mix-and-matching the Augur API methods.  To do this, use [ethrpc](https://github.com/AugurProject/ethrpc)'s lower-level `invoke` method (`augur.rpc.invoke`).  First, build a transaction object manually, then execute it using `invoke`.  The `invoke` method executes a method in a contract already on the network.  It can broadcast transactions to the network and/or capture return values by calling the contract method(s) locally.

```javascript
// Invoke:
// The method called here doubles its input argument.
augur.rpc.invoke({
   to: "0x5204f18c652d1c31c6a5968cb65e011915285a50",
   method: "double",
   signature: "i",
   params: 22121, // parameter value(s)
   send: false,
   returns: "number"
});
// this returns:
44242
```
Transaction fields are as follows:

Required:

- to: `<contract address> (hexstring)`
- method: `<function name> (string)`
- signature: `<function signature, e.g. "iia"> (string)`
- params: `<parameters passed to the function>`

Optional:

- send: `<true to sendTransaction, false to call (default)>`
- from: `<sender's address> (hexstring; defaults to the coinbase account)`
- returns: `<"array", "int", "BigNumber", or "string" (default)>`

The `params` and `signature` fields are required if your function accepts parameters; otherwise, these fields can be excluded.  The `returns` field is used only to format the output, and does not affect the actual RPC request.

<aside class="notice"><b><code>invoke</code> currently only works for <a href="https://github.com/ethereum/serpent">Serpent</a> contracts.</b>  The extra datatypes included by <a href="https://github.com/ethereum/solidity">Solidity</a> are not (yet) supported by the <a href="https://github.com/AugurProject/augur-abi">augur-abi</a> encoder.  The augur-abi encoder requires all parameters to be type <code>string</code>, <code>int256</code>, or <code>int256[]</code>.</aside>
