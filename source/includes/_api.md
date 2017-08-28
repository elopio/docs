API
===
```javascript
// After installing, just require augur.js to use it.
import Augur from 'augur.js';
const augur = new Augur();

const options = {
  httpAddresss: [
    'http://127.0.0.1:8545', // local http address for Geth Node
    'https://eth9000.augur.net' // hosted http address for Augur Node
  ],
  wsAddresses: [
    'ws://127.0.0.1:8545', // local websocket address for Geth Node
    'wss://ws9000.augur.net', // hosted websocket address for Augur Node
  ],
};

// Attempt to connect to a local Ethereum node
// if that fails, fall back to our hosted node
augur.connect(options, function (vitals) { /* ... */ });
// example vitals object:
vitals = {
  networkID: '9000',
  blockNumber: '0xf69b5',
  coinbase: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  gasPrice: 18000000000,
  api: {
    events: { /*...*/},
    functions: { /* ... */ },
  },
  contracts: { /* ... */ },
  rpc: { /* ... */ },
};
```
The Augur API is a set of JavaScript bindings for the methods encoded in Augur's [smart contracts](https://github.com/AugurProject/augur-core). The API method name, as well as its parameters as keys in the `params` object, are generally identical to those of the underlying smart contract method.

Augur's [core contracts](https://github.com/AugurProject/augur-core) exist on Ethereum's decentralized network. The various serialization, networking, and formatting tasks required to communicate with the Augur contracts from a web application are carried out by Augur's [middleware](http://docs.augur.net/#architecture).

[augur.js](https://github.com/AugurProject/augur.js) is the Augur JavaScript SDK, and is the user-facing component of the middleware. If you want to interact with the Augur contracts from a custom application, augur.js is the recommended way to do so. The easiest way to install augur.js is using [npm](https://www.npmjs.com/package/augur.js):

`$ npm install augur.js`

or if you prefer [yarn](https://yarnpkg.com/en/):

`$ yarn add augur.js`

To use the Augur API, augur.js must connect to an Ethereum node, which can be either remote (hosted) or local. To specify the connection endpoint, pass your RPC connection info to `augur.connect`. Augur will go through the list of potential connections provided by the `options` argument and attempt to connect to each in turn until one of the connections is successful or all attempts fail.

In the example we have set our first connection to try as `http://127.0.0.1:8545` which is our local geth node. If Augur is unable to connect to the local geth node, then Augur will go to the next provided address. In this case we have provided a single hosted node (`https://eth9000.augur.net`) as the only other attempt to make outside of the local geth node. If a connection is successfully established then a `vitals` object will be returned, otherwise an error message will be returned.

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

Initial market loading
----------------------
To load the basic market info for all markets, first call `augur.markets.getMarketsInfo({ [ branch, offset, numMarketsToLoad, volumeMin, volumeMax] }, callback)`. You will likely need to chunk the results so that the request does not time out. More detailed market info (including prices) for each market in each chunk (page) is then loaded using `augur.markets.getMarketInfo({ marketID[, account ] }, callback)`. `getMarketInfo` does not return the full order book; to get the order book for a market, call `augur.trading.orderBook.getOrderBook({ market[, offset, numTradesToLoad, scalarMinMax: { minValue, maxValue } ] }, callback)`.

<aside class="notice">Cache nodes regularly call <code>augur.markets.getMarketsInfo({ branch, offset, numMarketsToLoad } callback)</code>. The first time <code>getMarketsInfo</code> is called, all markets should be loaded. Subsequent <code>getMarketsInfo</code> calls should only load markets created since the previous call.</aside>

Simplified API
--------------
```javascript
const market = '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42';

augur.trading.orderBook.getOrderBook({ market }, function (orderBook) { /* ... */ })
// example output:
{
  buy: {
    '0x1f4f112a9aa99282e306cb58abc95b5b46199f802bd36d68f1619ba98866963a': {
      amount: '10',
      block: 217,
      fullPrecisionAmount: '10',
      fullPercisionPrice: '0.1208333333333333',
      id: '0x1f4f112a9aa99282e306cb58abc95b5b46199f802bd36d68f1619ba98866963a',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '2',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.1208',
      type: 'buy',
    },
    '0x2b054b0c9ca2fcf22ee73ba14ae41da70c7039c1a5b8125a40b9f2b68a20080b': {
      amount: '10',
      block: 219,
      fullPrecisionAmount: '10',
      fullPercisionPrice: '0.0083333333333333',
      id: '0x2b054b0c9ca2fcf22ee73ba14ae41da70c7039c1a5b8125a40b9f2b68a20080b',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '2',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.0083',
      type: 'buy',
    },
    '0x3c3958b3cad3fb693a6fdd013a615485ef42d824aaa3bd57734f5ec21567ebdc': {
      amount: '10',
      block: 220,
      fullPrecisionAmount: '10',
      fullPercisionPrice: '0.1770833333333333',
      id: '0x3c3958b3cad3fb693a6fdd013a615485ef42d824aaa3bd57734f5ec21567ebdc',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '3',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.177',
      type: 'buy',
    }
  },
  sell: {
    '0x0a056c290d73ca11b22531ef0c4ea970bdc3e7ccd64a60f8127fedaabd231f15': {
      amount: '10',
      block: 224,
      fullPrecisionAmount: '10',
      fullPercisionPrice: '0.9958333333333333',
      id: '0x0a056c290d73ca11b22531ef0c4ea970bdc3e7ccd64a60f8127fedaabd231f15',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '1',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.9959',
      type: 'sell',
    },
    '0x0bd9bf9c18ea08f98c70312ccc8deac7b58d88d3c5f2d0cc9a5bed201a90191e': {
      amount: '10',
      block: 220,
      fullPrecisionAmount: '10',
      fullPercisionPrice: '0.6583333333333333',
      id: '0x0bd9bf9c18ea08f98c70312ccc8deac7b58d88d3c5f2d0cc9a5bed201a90191e',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '3',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.6584',
      type: 'sell',
    },
    '0x1afbdc152df5d674a26459b0267a22cb13a3903f7922affcf526485662293269': {
      amount: '10',
      block: 222,
      fullPrecisionAmount: '10',
      fullPercisionPrice: '0.7145833333333333',
      id: '0x1afbdc152df5d674a26459b0267a22cb13a3903f7922affcf526485662293269',
      market: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
      outcome: '1',
      owner: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
      price: '0.7146',
      type: 'sell',
    }
  }
}

augur.markets.getMarketInfo({ marketID: market }, function (market) { /* ... */ });
// example output:
{
  author: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  branchID: '0xf69b5',
  consensus: null,
  creationBlock: 213,
  creationFee: '9',
  creationTime: 1502248462,
  cumulativeScale: '1',
  description: 'What will be the status of the U.S. electoral college on January 1, 2020?~|>Unchanged from 2016|Undermined but still in existence (e.g., National Popular Vote bill)|Formally abolished',
  endDate: 1577952000,
  eventBond: '4.5',
  extraInfo: 'http://www.nationalpopularvote.com',
  id: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
  makerFee: '0.01',
  maxValue: '3',
  minValue: '1',
  network: '9000',
  numOutcomes: 3,
  outcomes: [{
    id: 1,
    outstandingShares: '310',
    price: '0.6020833333333333',
    sharesPurchased: '0',
  }, {
    id: 2,
    outstandingShares: '310',
    price: '0.4333333333333333',
    sharesPurchased: '0',
  }, {
    id: 3,
    outstandingShares: '310',
    price: '0.4333333333333333',
    sharesPurchased: '0',
  }],
  tags: ['politics', 'elections', 'US politics'],
  takerFee: '0.02',
  topic: 'politics',
  tradingFee: '0.02',
  tradingPeriod: 9131,
  type: 'categorical',
  volume: '1710.399999999999999985'
}

const marketIDs = [ market, '0xe095e00863aecd814003a739da97b54c2b6737bd'];
const account = '0xb3f636cae9e8ad9795d14d3bdda3e382dba47c73';

augur.markets.batchGetMarketInfo({ marketIDs, account }, function (marketsInfo) { /* ... */ })
// example output:
{
  '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42': {
    author: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
    branchID: '0xf69b5',
    consensus: null,
    creationBlock: 213,
    creationFee: '9',
    creationTime: 1502248462,
    cumulativeScale: '1',
    description: 'What will be the status of the U.S. electoral college on January 1, 2020?~|>Unchanged from 2016|Undermined but still in existence (e.g., National Popular Vote bill)|Formally abolished',
    endDate: 1577952000,
    eventBond: '4.5',
    extraInfo: 'http://www.nationalpopularvote.com',
    id: '0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42',
    makerFee: '0.01',
    maxValue: '3',
    minValue: '1',
    network: '9000',
    numOutcomes: 3,
    outcomes: [{
      id: 1,
      outstandingShares: '310',
      price: '0.6020833333333333',
      sharesPurchased: '0',
    }, {
      id: 2,
      outstandingShares: '310',
      price: '0.4333333333333333',
      sharesPurchased: '0',
    }, {
      id: 3,
      outstandingShares: '310',
      price: '0.4333333333333333',
      sharesPurchased: '0',
    }],
    tags: ['politics', 'elections', 'US politics'],
    takerFee: '0.02',
    topic: 'politics',
    tradingFee: '0.02',
    tradingPeriod: 9131,
    type: 'categorical',
    volume: '1710.399999999999999985'
  },
  '0xe095e00863aecd814003a739da97b54c2b6737bd': {
    author: '0x9e12c5b6067c61c811add8fb55f5d6fcb88a9e5f',
    branchID: '0xf69b5',
    consensus: null,
    creationBlock: 5156,
    creationFee: '9',
    creationTime: 1502291695,
    cumulativeScale: '1',
    description: 'Will the Bitcoin (BTC) price be equal to or higher than USD 3,400 on 31 August 2017 as shown on www.coinmarketcap.com?',
    endDate: 1504130400,
    eventBond: '4.5',
    extraInfo: 'http://www.nationalpopularvote.com',
    id: '0xe095e00863aecd814003a739da97b54c2b6737bd',
    makerFee: '0.01',
    maxValue: '2',
    minValue: '1',
    network: '9000',
    numOutcomes: 2,
    outcomes: [{
      id: 1,
      outstandingShares: '2101.2',
      price: '0',
      sharesPurchased: '0',
    }, {
      id: 2,
      outstandingShares: '2101.2',
      price: '0.99',
      sharesPurchased: '5',
    }],
    resolutionSource: 'www.coinmarketcap.com',
    tags: ['Bitcoin', 'Cryptocurrency', null],
    takerFee: '0.02',
    topic: 'Bitcoin',
    tradingFee: '0.02',
    tradingPeriod: 8704,
    type: 'binary',
    volume: '8346'
  }
}
// all keys in the options object are optional, if branch is not provided then
// the default of augur.constants.DEFAULT_BRANCH_ID will be used.
const options = {
  branch: '0xf69b5',
  offset: '3',
  numMarketsToLoad: '2',
  volumeMin: '1000',
  volumeMax: '3000'
}

augur.markets.getMarketsInfo(options, function (marketsInfo) { /* ... */ })
// example output:
{
  '0xfb9165a0f492a910082c02bc174d3b3b7f7f979e': {
    author: '0x9e12c5b6067c61c811add8fb55f5d6fcb88a9e5f',
    branchID: '0xf69b5',
    consensus: null,
    creationTime: 1502248542,
    description: 'Who will win the University of Georgia vs. University of Florida football game in 2017?~|>Georgia|Florida|Vanderbilt',
    endDate: 1509346800,
    id: '0xfb9165a0f492a910082c02bc174d3b3b7f7f979e',
    makerFee: '0.01',
    maxValue: '3',
    minValue: '1',
    numOutcomes: 3,
    tags: ['sports', 'college football', 'football'],
    takerFee: '0.02',
    topic: 'sports',
    tradingFee: '0.02',
    tradingPeriod: 8734,
    type: 'categorical',
    volume: '1195.999999999999999997'
  },
  '0x563b377a956c80d77a7c613a9343699ad6123911': {
    author: '0x9e12c5b6067c61c811add8fb55f5d6fcb88a9e5f',
    branchID: '0xf69b5',
    consensus: null,
    creationTime: 1502248522,
    description: "Will Augur's live release happen by the end of August, 2017?",
    endDate: 1504249200,
    id: '0x563b377a956c80d77a7c613a9343699ad6123911',
    makerFee: '0.010000000000000000125',
    maxValue: '2',
    minValue: '1',
    numOutcomes: 2,
    tags: ['Augur', 'release date', 'Ethereum'],
    takerFee: '0.030000000000000000375',
    topic: 'Augur',
    tradingFee: '0.026666666666666667',
    tradingPeriod: 8705,
    type: 'binary',
    volume: '29623.019599999999999969'
  }
}
```
All of the methods in the Simplified API are getter methods that use an `eth_call` RPC request; for transactional requests (`eth_sendTransaction`), see the Transaction API section below. This API is simplified in the sense that single requests to this API can be used to fetch a large amount of data, without the need for complicated RPC batch queries.

### augur.trading.orderBook.getOrderBook({ market[, offset, numTradesToLoad, scalarMinMax: { minValue, maxValue } ] }[, callback])

Retrieves the full order book for `market`. The order book's format is an object with fields `buy` and `sell` containing arrays of buy and sell orders. If your `market` is a scalar `market` then you would want to include the optional object `scalarMinMax`. `scalarMinMax` should have a `minValue` and `maxValue` for the `market` you are querying. There are two optional fields, `offset` and `numTradesToLoad`, which can be used to split up the query into multiple chunks, which can be helpful for larger order books.

### augur.markets.getMarketInfo({ marketID[, account] }[, callback])

Reads all information about a market (`marketID`) that is stored on-contract. It also determines the `type` of the market, which can be `binary` (two outcomes; i.e., Yes or No), `categorical` (more than two outcomes, i.e., Multiple Choice), or `scalar` (answer can be any of a range of values; i.e., Numerical). You can optionally pass an `account` address to show the amount of shares purchased by that `account` for each outcome.

### augur.markets.batchGetMarketInfo({ marketIDs[, account] }[, callback])

Retrieve a `marketsInfo` object for the market IDs in array `marketIDs`. The `marketsInfo` object will contain `marketInfo` objects (see above for example) which are indexed by their respective market IDs. `account` is an optional parameter used to determine shares purchased by that `account` address for each outcome in the markets returned in the `marketsInfo` object.

### augur.markets.getMarketsInfo({ [branch, offset, numMarketsToLoad, volumeMin, volumeMax] }[, callback])

Returns basic information about markets on a specific `branch` or the `augur.constants.DEFAULT_BRANCH_ID` if no `branch` is provided. This function returns a `marketsInfo` object containing `marketInfo` objects indexed by their respective market IDs similar to `batchGetMarketInfo`. The `offset` and `numMarketsToLoad` optional params are also available to this function to break up the market information as the number of markets on a single branch is typically too large for a single RPC request. The final two optional parameters are `volumeMin` and `volumeMax` which are used to filter markets by a minimum and/or maximum trade volume.

<aside class="notice">Markets are indexed in the order created; i.e., the first market created has index 0, the second 1, etc. This ordering allows us to break up a large aggregate request like <code>getMarketsInfo</code> into manageable chunks.

For example, suppose you were displaying markets on separate pages. You might want to retrieve information about all markets, but, to keep your loading time reasonable, only get 5 markets per request. To get the first 5 markets, you would set <code>offset</code> to 0 and <code>numMarketsToLoad</code> to 5: <code>augur.markets.getMarketsInfo({offset: 0, numMarketsToLoad: 5}, cb)</code>.  To get the second 5, <code>offset</code> would be 5: <code>augur.markets.getMarketsInfo({offset: 5, numMarketsToLoad: 5}, cb)</code>.  The third 5, <code>offset</code> would be 10: <code>augur.markets.getMarketsInfo({offset: 10, numMarketsToLoad: 5}, cb)</code>, and so on.</aside>

Call API
--------
Augur's Call API is made up of "getter" methods that retrieve information from the blockchain (using Ethereum's `eth_call` RPC) but does not write information to the blockchain.

```javascript
// Branch contract
const branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5";
const parentPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";

augur.api.Branch.getChildBranch({ branch, parentPayoutDistributionHash }, function (childBranch) { /* ... */ })
// example output:
childBranch = "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c"

augur.api.Branch.getCurrentReportingWindow({ branch }, function (currReportingWindow) { /* ... */ })
// example output:
currReportingWindow = "578"

augur.api.Branch.getForkEndTime({ branch }, function (forkEndTime) { /* ... */ })
// example output:
forkEndTime = "1489855429";

augur.api.Branch.getForkingMarket({ branch }, function (forkingMarket) { /* ... */ })
// example output:
forkingMarket = "0x78f7b43150d27c464359e735781c16ac585f52a8";

augur.api.Branch.getNextReportingWindow({ branch }, function (nextReportingWindow) { /* ... */ })
// example output:
nextReportingWindow = "579"

augur.api.Branch.getParentBranch({ branch }, function (parentBranch) { /* ... */ })
// example output:
parentBranch = "0x63c59544b89cce1dd53b1b566862189b25adec41"

augur.api.Branch.getParentPayoutDistributionHash({ branch }, function (branchParentPayoutDistributionHash) { /* ... */ })
// example output:
branchParentPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Branch.getPreviousReportingWindow({ branch }, function (previousReportingWindow) { /* ... */ })
// example output:
previousReportingWindow = "577"

augur.api.Branch.getReportingPeriodDurationInSeconds({ branch }, function (reportingPeriodDuration) { /* ... */ })
// example output:
reportingPeriodDuration = "2592000";

const endTime = 2524608000;
augur.api.Branch.getReportingWindowByMarketEndTime({ branch, endTime, hasAutomatedReporter: 0 }, function (reportingWindowByEndTime) { /* ... */ })
// example output:
reportingWindowByEndTime = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.Branch.getReportingWindowByTimestamp({ branch, timestamp: endTime }, function (reportingWindowByTimestamp) { /* ... */ })
// example output:
reportingWindowByTimestamp = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.Branch.getReportingWindowId({ branch, timestamp: new Date().getTime() }, function (reportingWindowId) { /* ... */ })
// example output:
reportingWindowId = "578";

augur.api.Branch.getReputationToken({ branch }, function (reputationTokenAddress) { /* ... */ })
// example output:
reputationTokenAddress = "0x2fb561b2bdbcd1ae1995bdd6aff6776d6f4292f2";

augur.api.Branch.getTopics({ branch }, function (topicsAddress) { /* ... */ })
// example output:
topicsAddress = "0x14f094c79a676c681e7cc490e775f73072e535ae";

const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.Branch.isContainerForMarket({ branch, shadyMarket: market }, function (isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1";

const registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
augur.api.Branch.isContainerForRegistrationToken({ branch, shadyRegistrationToken: registrationToken }, function (isContainerForRegistrationToken) { /* ... */ })
// example output:
isContainerForRegistrationToken = "1";

const reportingToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
augur.api.Branch.isContainerForReportingToken({ branch, shadyReportingToken: reportingToken }, function (isContainerForReportingToken) { /* ... */ })
// example output:
isContainerForReportingToken = "1";

const reportingWindow = "578";
augur.api.Branch.isContainerForReportingWindow({ branch, shadyReportingWindow: reportingWindow }, function (isContainerForReportingWindow) { /* ... */ })
// example output:
isContainerForReportingWindow = "1";

const childBranch = "0xb4e8c1f85c4382d64954aca187f9f386c8bb1a6c";
augur.api.Branch.isParentOf({ branch, shadyChild: childBranch }, function (isParentOf) { /* ... */ })
// example output:
isParentOf = "1";
```
### [Branch Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/branch.se)

#### augur.api.Branch.getChildBranch({ branch, parentPayoutDistributionHash }[, callback])

Returns the child branch address of the specified `branch` address related to the `parentPayoutDistributionHash` provided.

#### augur.api.Branch.getCurrentReportingWindow({ branch }[, callback])

Returns the current reporting window ID for the specified `branch`.

#### augur.api.Branch.getForkEndTime({ branch }[, callback])

Returns the timestamp for when the fork ends given a specified `branch`.

#### augur.api.Branch.getForkingMarket({ branch }[, callback])

Returns the address of the market that the specified `branch` is forking over.

#### augur.api.Branch.getNextReportingWindow({ branch }[, callback])

Returns the next reporting window ID for a specific `branch`.

#### augur.api.Branch.getParentBranch({ branch }[, callback])

Returns the parent branch address of the specified `branch`.

#### augur.api.Branch.getParentPayoutDistributionHash({ branch }[, callback])

Returns the parent branch's payout distribution hash given a `branch` address.

#### augur.api.Branch.getPreviousReportingWindow({ branch }[, callback])

Returns the previous reporting window ID for the specified `branch`.

#### augur.api.Branch.getReportingPeriodDurationInSeconds({ branch }[, callback])

Returns the specified `branch`'s reporting period duration in seconds.

#### augur.api.Branch.getReportingWindowByMarketEndTime({ branch, endTime, hasAutomatedReporter }[, callback])

Returns the reporting window address on the specific `branch` given an `endTime` and if the market we are checking for has an automated reporter or not (`hasAutomatedReporter`). `hasAutomatedReporter` should be `0` for markets without an automated reporter, `1` for markets with an automated reporter.

#### augur.api.Branch.getReportingWindowByTimestamp({ branch, timestamp }[, callback])

Returns the reporting window address for a specific `branch` and provided `timestamp`.

#### augur.api.Branch.getReportingWindowId({ branch, timestamp }[, callback])

Returns the reporting window ID for a specific `branch` and provided `timestamp`. This is calculated by dividing the timestamp by the branch's reporting period duration in seconds.

#### augur.api.Branch.getReputationToken({ branch }[, callback])

Returns the reputation token address for a specific `branch`.

#### augur.api.Branch.getTopics({ branch }[, callback])

Returns the topics address for the specific `branch`.

#### augur.api.Branch.isContainerForMarket({ branch, shadyMarket }[, callback])

Returns wether the specific `branch` is a container for the `shadyMarket` address provided. Returns `1` if true, `0` if false.

#### augur.api.Branch.isContainerForRegistrationToken({ branch, shadyRegistrationToken }[, callback])

Returns wether the specific `branch` is a container for the `shadyRegistrationToken` address provided. Returns `1` if true, `0` if false.

#### augur.api.Branch.isContainerForReportingToken({ branch, shadyReportingToken }[, callback])

Returns wether the specific `branch` is a container for the `shadyReportingToken` address provided. Returns `1` if true, `0` if false.

#### augur.api.Branch.isContainerForReportingWindow({ branch, shadyReportingWindow }[, callback])

Returns wether the specific `branch` is a container for the `shadyReportingWindow` provided. Returns `1` if true, `0` if false.

#### augur.api.Branch.isParentOf({ branch, shadyChild }[, callback])

Returns wether the specific `branch` is a container for the `shadyChild` branch address provided. Returns `1` if true, `0` if false.

```javascript
// Dispute Bond Token Contract
const disputeBondToken = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
const address = "0x3d62bafc1791752393384b902f813da861ddedd9";

augur.api.DisputeBondToken.balanceOf({ disputeBondToken, address }, function (balance) { /* ... */ })
// example output:
balance = "1"

augur.api.DisputeBondToken.getBondHolder({ disputeBondToken }, function (bondHolder) { /* ... */ })
// example output:
bondHolder = "0x3d62bafc1791752393384b902f813da861ddedd9"

augur.api.DisputeBondToken.getBondRemainingToBePaidOut({ disputeBondToken }, function (bondRemainingToBePaidOut) { /* ... */ })
// example output:
bondRemainingToBePaidOut = "1100000000000000000000"

augur.api.DisputeBondToken.getBranch({ disputeBondToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.DisputeBondToken.getDisputedPayoutDistributionHash({ disputeBondToken }, function (disputedPayoutDistributionHash) { /* ... */ })
// example output:
disputedPayoutDistributionHash = "0xff89be2020af3cb2ca244eb862df2d350300904a96039eb53cbacf380f13f21b"

augur.api.DisputeBondToken.getMarket({ disputeBondToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.DisputeBondToken.getReputationToken({ disputeBondToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.DisputeBondToken.totalSupply({ disputeBondToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "1"
```
### [Dispute Bond Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/disputeBondToken.se)

#### augur.api.DisputeBondToken.balanceOf({ disputeBondToken, address }[, callback])

This transaction will return the balance for a specified `disputeBondToken` owned by the provided `address`. Will always result in `1` or `0`.

#### augur.api.DisputeBondToken.getBondHolder({ disputeBondToken }[, callback])

This transaction will return the address of the bond holder for a specified `disputeBondToken`.

#### augur.api.DisputeBondToken.getBondRemainingToBePaidOut({ disputeBondToken }[, callback])

This transaction will return the amount, denoted in attotokens, of bond remaining to be paid out for a specified `disputeBondToken`.

#### augur.api.DisputeBondToken.getBranch({ disputeBondToken }[, callback])

This transaction will return the branch that a specified `disputeBondToken`'s market lives on.

#### augur.api.DisputeBondToken.getDisputedPayoutDistributionHash({ disputeBondToken }[, callback])

This transaction will return the distribution hash for the dispute payout given a specified `disputeBondToken`.

#### augur.api.DisputeBondToken.getMarket({ disputeBondToken }[, callback])

This transaction will return the market that a specified `disputeBondToken` is disputing the results of.

#### augur.api.DisputeBondToken.getReputationToken({ disputeBondToken }[, callback])

This transaction will return the reputation token address that a specified `disputeBondToken`'s market uses.

#### augur.api.DisputeBondToken.totalSupply({ disputeBondToken }[, callback])

This transaction will return the total supply of a specified `disputeBondToken`, which will always return `1`.

```javascript
// Market Contract
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Market.canBeReportedOn({ market }, function (canBeReportedOn) { /* ... */ })
// example output:
canBeReportedOn = "1"

augur.api.Market.getAutomatedReportDisputeDueTimestamp({ market }, function (automatedReporterDisputeDueTimestamp) { /* ... */ })
// example output:
automatedReporterDisputeDueTimestamp = "1500907130"

augur.api.Market.getAutomatedReportDueTimestamp({ market }, function (automatedReporterDueTimestamp) { /* ... */ })
// example output:
automatedReporterDueTimestamp = "1500647930"

augur.api.Market.getBranch({ market }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.Market.getCompleteSetCostInAttotokens({ market }, function (completeSetCostInAttotokens) { /* ... */ })
// example output:
completeSetCostInAttotokens = "1000000000000000000"

augur.api.Market.getCreator({ market }, function (marketCreator) { /* ... */ })
// example output:
marketCreator = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa"

augur.api.Market.getDenominationToken({ market }, function (denominationToken) { /* ... */ })
// example output:
denominationToken = "0x30e3852448f4ab5d62bbf7544ca3c92daca5c957"

augur.api.Market.getEndTime({ market }, function (endTime) { /* ... */ })
// example output:
endTime = "1500388730";

augur.api.Market.getFinalizationTime({ market }, function (finalizationTime) { /* ... */ })
// example output:
finalizationTime = "1500647930";

augur.api.Market.getFinalPayoutDistributionHash({ market }, function (finalPayoutDistributionHash) { /* ... */ })
// example output:
finalPayoutDistributionHash = "0xa310ca2018af3cb2ca244eb862df2d350300904a96039eb53cbaff012c92d10c"

augur.api.Market.getFinalWinningReportingToken({ market }, function (winningReportingToken) { /* ... */ })
// example output:
winningReportingToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.Market.getMarketCreatorSettlementFeeInAttoethPerEth({ market }, function (marketCreatorSettlementFee) { /* ... */ })
// example output:
marketCreatorSettlementFee = "20000000000000000"

augur.api.Market.getMaxDisplayPrice({ market }, function (maxDisplayPrice) { /* ... */ })
// example output:
maxDisplayPrice = "1"

augur.api.Market.getMinDisplayPrice({ market }, function (minDisplayPrice) { /* ... */ })
// example output:
minDisplayPrice = "0"

augur.api.Market.getNumberOfOutcomes({ market }, function (numOutcomes) { /* ... */ })
// example output:
numOutcomes = "2"

augur.api.Market.getPayoutDenominator({ market }, function (payoutDenominator) { /* ... */ })
// example output:
payoutDenominator = "1"

augur.api.Market.getRegistrationToken({ market }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api.Market.getReportingWindow({ market }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.Market.getReputationToken({ market }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.Market.getShareToken({ market, outcome: 1 }, function (shareToken) { /* ... */ })
// example output:
shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054"

augur.api.Market.getTentativeWinningPayoutDistributionHash({ market }, function (tentativeWinningPayoutDistributionHash) { /* ... */ })
// example output:
tentativeWinningPayoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.Market.getTopic({ market }, function (topic) { /* ... */ })
// example output:
topic = "Augur"

const reportingToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
augur.api.Market.isContainerForReportingToken({ market, shadyToken: reportingToken }, function (isContainerForReportingToken) { /* ... */ })
// example output:
isContainerForReportingToken = "1"

const shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
augur.api.Market.isContainerForShareToken({ market, shadyShareToken: shareToken }, function (isContainerForShareToken) { /* ... */ })
// example output:
isContainerForShareToken = "1"

const disputeBondToken = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
augur.api.Market.isContainerForDisputeBondToken({ market, shadyBondToken: disputeBondToken }, function (isContainerForShareToken) { /* ... */ })
// example output:
isContainerForDisputeBondToken = "1"

augur.api.Market.isDoneWithAllReporters({ market }, function (isDoneWithAllReporters) { /* ... */ })
// example output:
isDoneWithAllReporters = "1"

augur.api.Market.isDoneWithAutomatedReporters({ market }, function (isDoneWithAutomatedReporters) { /* ... */ })
// example output:
isDoneWithAutomatedReporters = "1"

augur.api.Market.isDoneWithLimitedReporters({ market }, function (isDoneWithLimitedReporters) { /* ... */ })
// example output:
isDoneWithLimitedReporters = "1"

augur.api.Market.isFinalized({ market }, function (isFinalized) { /* ... */ })
// example output:
isFinalized = "1"

augur.api.Market.isInAllDisputePhase({ market }, function (isInAllDisputePhase) { /* ... */ })
// example output:
isInAllDisputePhase = "1";

augur.api.Market.isInAllReportingPhase({ market }, function (isInAllReportingPhase) { /* ... */ })
// example output:
isInAllReportingPhase = "1";

augur.api.Market.isInAutomatedDisputePhase({ market }, function (isInAutomatedDisputePhase) { /* ... */ })
// example output:
isInAutomatedDisputePhase = "1";

augur.api.Market.isInAutomatedReportingPhase({ market }, function (isInAutomatedReportingPhase) { /* ... */ })
// example output:
isInAutomatedReportingPhase = "1";

augur.api.Market.isInLimitedDisputePhase({ market }, function (isInLimitedDisputePhase) { /* ... */ })
// example output:
isInLimitedDisputePhase = "1";

augur.api.Market.isInLimitedReportingPhase({ market }, function (isInLimitedReportingPhase) { /* ... */ })
// example output:
isInLimitedReportingPhase = "1";

augur.api.Market.needsMigration({ market }, function (needsMigration) { /* ... */ })
// example output:
needsMigration = "1";

augur.api.Market.shouldCollectReportingFees({ market }, function (shouldCollectReportingFees) { /* ... */ })
// example output:
shouldCollectReportingFees = "1";
```
### [Market Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/market.se)

#### augur.api.Market.canBeReportedOn({ market }[, callback])

Returns wether the specific `market` can be reported on. Returns `1` if true, `0` if false.

#### augur.api.Market.getAutomatedReportDisputeDueTimestamp({ market }[, callback])

Returns the timestamp for when a specific `market`'s  automated report process and dispute period should be completed.

#### augur.api.Market.getAutomatedReportDueTimestamp({ market }[, callback])

Returns the timestamp for when a specific `market`'s  automated report process should be completed but doesn't count the dispute period.

#### augur.api.Market.getBranch({ market }[, callback])

Returns the branch ID of the branch that the specified `market` is contained within.

#### augur.api.Market.getCompleteSetCostInAttotokens({ market }[, callback])

Returns the cost of a complete set for a specific `market` denominated in attotokens.

#### augur.api.Market.getCreator({ market }[, callback])

Returns the address of the creator of the specified `market`.

#### augur.api.Market.getDenominationToken({ market }[, callback])

Returns the address of the token used to denominate the specified `market`.

#### augur.api.Market.getEndTime({ market }[, callback])

Returns the timestamp for when the specified `market`'s event has come to pass and the `market` is ready to be reported on.

#### augur.api.Market.getFinalizationTime({ market }[, callback])

Returns the timestamp for when the specified `market` was finalized.

#### augur.api.Market.getFinalPayoutDistributionHash({ market }[, callback])

Returns the final payout distribution hash for a specified `market`.

#### augur.api.Market.getFinalWinningReportingToken({ market }[, callback])

Returns the winning reporting token address for a specified `market`.

#### augur.api.Market.getMarketCreatorSettlementFeeInAttoethPerEth({ market }[, callback])

Returns the fee paid to the `market` creator denominated in attotokens for 1 token settled on the market.

#### augur.api.Market.getMaxDisplayPrice({ market }[, callback])

Returns the maximum displayed price for the specified `market`.

#### augur.api.Market.getMinDisplayPrice({ market }[, callback])

Returns the minimum displayed price for the specified `market`.

#### augur.api.Market.getNumberOfOutcomes({ market }[, callback])

Returns the number of outcomes for a specified `market`.

#### augur.api.Market.getPayoutDenominator({ market }[, callback])

Returns the payout denominator for a specified `market`.

#### augur.api.Market.getRegistrationToken({ market }[, callback])

Returns the registration token's address for the specified `market`.

#### augur.api.Market.getReportingWindow({ market }[, callback])

Returns the reporting window address for the specified `market`.

#### augur.api.Market.getReputationToken({ market }[, callback])

Returns the reputation token's address for the specified `market`.

#### augur.api.Market.getShareToken({ market, outcome }[, callback])

Returns the share token's address for the specified `market` and `outcome`.

#### augur.api.Market.getTentativeWinningPayoutDistributionHash({ market }[, callback])

Returns the tentatively winning Payout Distribution Hash given a specified `market`.

#### augur.api.Market.getTopic({ market }[, callback])

Returns the topic of the specified `market`.

#### augur.api.Market.isContainerForReportingToken({ market, shadyToken }[, callback])

Returns wether the specific `market` is a container for the `shadyToken` address provided, which is the reporting token address we intend to check. Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForShareToken({ market, shadyShareToken }[, callback])

Returns wether the specific `market` is a container for the `shadyShareToken` address provided, which is the share token address we intend to check. Returns `1` if true, `0` if false.

#### augur.api.Market.isContainerForDisputeBondToken({ market, shadyBondToken }[, callback])

Returns wether the specific `market` is a container for the `shadyBondToken` address provided. A `shadyBondToken` will return true if it is a automated report dispute bond token, a limited reporter dispute bond token, or an all reporters dispute bond token for this `market`. Returns `1` if true, `0` if false.

#### augur.api.Market.isDoneWithAllReporters({ market }[, callback])

Returns wether the specific `market` is done with the all reporting phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isDoneWithAutomatedReporters({ market }[, callback])

Returns wether the specific `market` is done with the automated reporting phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isDoneWithLimitedReporters({ market }[, callback])

Returns wether the specific `market` is done with the limited reporting phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isFinalized({ market }[, callback])

Returns wether the specific `market` has been finalized or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInAllDisputePhase({ market }[, callback])

Returns wether the specific `market` is current in the All Dispute Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInAllReportingPhase({ market }[, callback])

Returns wether the specific `market` is current in the All Reporting Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInAutomatedDisputePhase({ market }[, callback])

Returns wether the specific `market` is current in the Automated Dispute Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInAutomatedReportingPhase({ market }[, callback])

Returns wether the specific `market` is current in the Automated Reporting Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInLimitedDisputePhase({ market }[, callback])

Returns wether the specific `market` is current in the Limited Dispute Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.isInLimitedReportingPhase({ market }[, callback])

Returns wether the specific `market` is current in the the Limited Reporting Phase or not. Returns `1` if true, `0` if false.

#### augur.api.Market.needsMigration({ market }[, callback])

Returns wether the specific `market` needs to be migrated to a new branch or not. Returns `1` if true, `0` if false.

#### augur.api.Market.shouldCollectReportingFees({ market }[, callback])

Returns wether the specific `market` should collect reporting fees or not. Returns `1` if true, `0` if false.

```javascript
// Registration Token Contract
const registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
const ownerAddress = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spenderAddress = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.RegistrationToken.allowance({ registrationToken, ownerAddress, spenderAddress }, function (allowance) { /* ... */ })
// example output:
allowance = "1";

augur.api.RegistrationToken.balanceOf({ registrationToken, address: ownerAddress }, function (balance) { /* ... */ })
// example output:
balance = "1";

augur.api.RegistrationToken.getBranch({ registrationToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.RegistrationToken.getPeakSupply({ registrationToken }, function (peakSuppy) { /* ... */ })
// example output:
peakSuppy = "234"

augur.api.RegistrationToken.getReportingWindow({ registrationToken }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.RegistrationToken.getReputationToken({ registrationToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.RegistrationToken.totalSupply({ registrationToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "215";

```
### [Registration Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/registrationToken.se)

#### augur.api.RegistrationToken.allowance({ registrationToken, ownerAddress, spenderAddress }[, callback])

Returns the allowance that a specified `spenderAddress` can spend of the `ownerAddress`'s `registrationToken`s.

#### augur.api.RegistrationToken.balanceOf({ registrationToken, address }[, callback])

Returns the token balance for the specified `registrationToken` owned by the `address` provided.

#### augur.api.RegistrationToken.getBranch({ registrationToken }[, callback])

Returns the Branch address for the specified `registrationToken`'s Reporting Window.

#### augur.api.RegistrationToken.getPeakSupply({ registrationToken }[, callback])

Returns the peak supply of tokens for the specified `registrationToken`.

#### augur.api.RegistrationToken.getReportingWindow({ registrationToken }[, callback])

Returns the Reporting Window address for the specified `registrationToken`.

#### augur.api.RegistrationToken.getReputationToken({ registrationToken }[, callback])

Returns the Reputation Tokens address for the specific `registrationToken`'s Reporting Window.

#### augur.api.RegistrationToken.totalSupply({ registrationToken }[, callback])

Returns the current total supply of the specified `registrationToken`.

```javascript
// Reporting Token Contract
const reportingToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
const ownerAddress = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spenderAddress = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.ReportingToken.allowance({ reportingToken, ownerAddress, spenderAddress }, function (allowance) { /* ... */ })
// example output:
allowance = "1"

augur.api.ReportingToken.balanceOf({ reportingToken, address: ownerAddress }, function (balance) { /* ... */ })
// example output:
balance = "1"

augur.api.ReportingToken.getBranch({ reportingToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReportingToken.getMarket({ reportingToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.ReportingToken.getPayoutDistributionHash({ reportingToken }, function (payoutDistributionHash) { /* ... */ })
// example output:
payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a"

augur.api.ReportingToken.getPayoutNumerator({ reportingToken, index: 0 }, function (payoutNumerator) { /* ... */ })
// example output:
payoutNumerator = "1"

augur.api.ReportingToken.getRegistrationToken({ reportingToken }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api.ReportingToken.getReportingWindow({ reportingToken }, function (reportingWindow) { /* ... */ })
// example output:
reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a"

augur.api.ReportingToken.getReputationToken({ reportingToken }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.ReportingToken.totalSupply({ reportingToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "210"

```
### [Reporting Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reportingToken.se)

#### augur.api.ReportingToken.allowance({ reportingToken, ownerAddress, spenderAddress }[, callback])

Returns the allowance that a specified `spenderAddress` can spend of the `ownerAddress`'s `reportingToken`s.

#### augur.api.ReportingToken.balanceOf({ reportingToken, address }[, callback])

Returns the token balance for the specified `reportingToken` owned by the provided `address`.

#### augur.api.ReportingToken.getBranch({ reportingToken }[, callback])

Returns the Branch address for the specified `reportingToken`'s Reporting Window.

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

#### augur.api.ReportingToken.totalSupply({ reportingToken }[, callback])

Returns the current total supply of the specified `reportingToken`.

```javascript
// Reporting Window Contract
const reportingWindow = "0x06cbcd92af2571f1419b622a794d65db524f682a";

augur.api.ReportingWindow.getBranch({ reportingWindow }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReportingWindow.getDisputeEndTime({ reportingWindow }, function (disputeEndTime) { /* ... */ })
// example output:
disputeEndTime = "1500907130"

augur.api.ReportingWindow.getDisputeStartTime({ reportingWindow }, function (disputeStartTime) { /* ... */ })
// example output:
disputeStartTime = "1500647930"

augur.api.ReportingWindow.getEndTime({ reportingWindow }, function (endTime) { /* ... */ })
// example output:
endTime = "1500388730"

augur.api.ReportingWindow.getMaxReportsPerLimitedReporterMarket({ reportingWindow }, function (maxReportsPerLimitedReporterMarket) { /* ... */ })
// example output:
maxReportsPerLimitedReporterMarket = "9"

augur.api.ReportingWindow.getRegistrationToken({ reportingWindow }, function (registrationToken) { /* ... */ })
// example output:
registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352"

augur.api.ReportingWindow.getReportingEndTime({ reportingWindow }, function (reportingEndTime) { /* ... */ })
// example output:
reportingEndTime = "1500647900"

augur.api.ReportingWindow.getReportingStartTime({ reportingWindow }, function (reportingStartTime) { /* ... */ })
// example output:
reportingStartTime = "14998315100"

const reporter = "0x2cd999e2f90dfc237ccbc52e2a469e1e11221f75";
augur.api.ReportingWindow.getReportsByReporter({ reportingWindow, reporter }, function (reportsByReporter) { /* ... */ })
// example output:
reportsByReporter = "0x03198001d1c223b2fdb1866703a7c2e4d5313f80"

augur.api.ReportingWindow.getReputationToken({ reportingWindow }, function (reputationToken) { /* ... */ })
// example output:
reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e"

augur.api.ReportingWindow.getRequiredReportsPerReporterForlimitedReporterMarkets({ reportingWindow }, function (requiredReportsPerReporterForLimitedReporterMarkets) { /* ... */ })
// example output:
requiredReportsPerReporterForLimitedReporterMarkets = "2"

augur.api.ReportingWindow.getStartTime({ reportingWindow }, function (startTime) { /* ... */ })
// example output:
startTime = "14995895900"

augur.api.ReportingWindow.getTargetReportsPerLimitedReporterMarket({ reportingWindow }, function (targetReportsPerLimitedReporterMarket) { /* ... */ })
// example output:
targetReportsPerLimitedReporterMarket = "3"

augur.api.ReportingWindow.getTargetReportsPerReporter({ reportingWindow }, function (targetReportsPerReporter) { /* ... */ })
// example output:
targetReportsPerReporter = "5"

augur.api.ReportingWindow.isActive({ reportingWindow }, function (isActive) { /* ... */ })
// example output:
isActive = "1"

const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.ReportingWindow.isContainerForMarket({ reportingWindow, shadyMarket: market }, function (isContainerForMarket) { /* ... */ })
// example output:
isContainerForMarket = "1"

const registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
augur.api.ReportingWindow.isContainerForRegistrationToken({ reportingWindow, shadyRegistrationToken: registrationToken }, function (isContainerForRegistrationToken) { /* ... */ })
// example output:
isContainerForRegistrationToken = "1"

augur.api.ReportingWindow.isDisputeActive({ reportingWindow }, function (isDisputeActive) { /* ... */ })
// example output:
isDisputeActive = "1"

augur.api.ReportingWindow.isDoneReporting({ reportingWindow, reporter }, function (isDoneReporting) { /* ... */ })
// example output:
isDoneReporting = "1"

augur.api.ReportingWindow.isReportingActive({ reportingWindow }, function (isReportingActive) { /* ... */ })
// example output:
isReportingActive = "1"
```
### [Reporting Window Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reportingWindow.se)

#### augur.api.ReportingWindow.getBranch({ reportingWindow }[, callback])

Returns the branch address that contains the specified `reportingWindow`.

#### augur.api.ReportingWindow.getDisputeEndTime({ reportingWindow }[, callback])

Returns the Dispute End Time for a specified `reportingWindow`.

#### augur.api.ReportingWindow.getDisputeStartTime({ reportingWindow }[, callback])

Returns the Dispute Start Time for the specified `reportingWindow`.

#### augur.api.ReportingWindow.getEndTime({ reportingWindow }[, callback])

Returns the End Time for the specified `reportingWindow`.

#### augur.api.ReportingWindow.getMaxReportsPerLimitedReporterMarket({ reportingWindow }[, callback])

Returns the maximum number of reports a limited reporter market can have in the specified `reportingWindow`.

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

```javascript
// Reputation Token Contract
const reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
const ownerAddress = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spenderAddress = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.ReputationToken.allowance({ reputationToken, ownerAddress, spenderAddress }, function (allowance) { /* ... */ })
// example output:
allowance = "200"

augur.api.ReputationToken.balanceOf({ reputationToken, address: ownerAddress }, function (balance) { /* ... */ })
// example output:
balance = "1000"

augur.api.ReputationToken.getBranch({ reputationToken }, function (branch) { /* ... */ })
// example output:
branch = "0x0920d1513057572be46580b7ef75d1d01a99a3e5"

augur.api.ReputationToken.getTopMigrationDestination({ reputationToken }, function (topMigrationDestination) { /* ... */ })
// example output:
topMigrationDestination = "0x1aa30942000ac72dee6580e1ac32d1d01ac1af00"

augur.api.ReputationToken.totalSupply({ reputationToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "11000000"
```
### [Reputation Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/reputationToken.se)

#### augur.api.ReputationToken.allowance({ reputationToken, ownerAddress, spenderAddress }[, callback])

Returns the allowance that a specified `spenderAddress` can spend of the `ownerAddress`'s `reputationToken`s.

#### augur.api.ReputationToken.balanceOf({ reputationToken, address }[, callback])

Returns the token balance of the specified `reputationToken` owned by the `address` provided.

#### augur.api.ReputationToken.getBranch({ reputationToken }[, callback])

Returns the Branch address for the specified `reputationToken`.

#### augur.api.ReputationToken.getTopMigrationDestination({ reputationToken }[, callback])

Returns the top migration destination address for the specified `reputationToken`.

#### augur.api.ReputationToken.totalSupply({ reputationToken }[, callback])

Returns the current total supply of the specified `reputationToken`.

```javascript
// Cash Contract
const owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.Cash.allowance({ owner, spender }, function (allowance) { /* ... */ })
// example output:
allowance = "100"

augur.api.Cash.balanceOf({ address: owner }, function (balance) { /* ... */ })
// example output:
balance = "10000"

augur.api.Cash.getDecimals({}, function (decimals) { /* ... */ })
// example output:
decimals = "18"

augur.api.Cash.getInitiated({}, function (initiated) { /* ... */ })
// example output:
initiated = "1"

augur.api.Cash.getName({}, function (name) { /* ... */ })
// example output:
name = "Cash"

augur.api.Cash.getSymbol({}, function (symbol) { /* ... */ })
// example output:
symbol = "CASH"

augur.api.Cash.totalSupply({}, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "11000000"
```
### [Cash Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/cash.se)

#### augur.api.Cash.allowance({ owner, spender }[, callback])

Returns the allowance that a specified `spender` address can spend of the `owner` address' Cash tokens.

#### augur.api.Cash.balanceOf({ address }[, callback])

Returns the balance of Cash tokens owned by the specified `address`.

#### augur.api.Cash.getDecimals({}[, callback])

Returns the amount of decimal places that the Cash contract is accurate to: `18`, like Ether.

#### augur.api.Cash.getInitiated({}[, callback])

Returns wether the sender of this call has initiated a withdraw of Ether from the Cash contract or not. Returns `1` if true, `0` if false.

#### augur.api.Cash.getName({}[, callback])

Returns the name string for Cash: `Cash`.

#### augur.api.Cash.getSymbol({}[, callback])

Returns the symbol string for Cash: `CASH`.

#### augur.api.Cash.totalSupply({}[, callback])

Returns the current total supply of Cash.

```javascript
// Orders Contract
const orderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
const type = "1";
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
const outcome = "1";
const fxpPrice = "450000000000000000"; // 0.45

augur.api.Orders.assertIsNotBetterPrice({ type, market, outcome, fxpPrice,
betterOrderID: orderID }, function (isNotBetterPrice) { /* ... */ })
// example output:
isNotBetterPrice = "1"

augur.api.Orders.assertIsNotWorsePrice({ type, market, outcome, fxpPrice, worstOrderID: orderID }, function (isNotWorstPrice) { /* ... */ })
// example output:
isNotWorstPrice = "0"

augur.api.Orders.getAmount({ orderID, type, market, outcome }, function (amount) { /* ... */ })
// example output:
amount = "15"

augur.api.Orders.getBestOrderID({ type, market, outcome }, function (bestOrderID) { /* ... */ })
// example output:
bestOrderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

const secondBestOrderID = "0x49cb49f610b5f6e31ee163a8ad65f964af1088e38c8a1b07f1218177b5e006b5";
augur.api.Orders.getBetterOrderID({ orderID: secondBestOrderID, type, market, outcome }, function (betterOrderID) { /* ... */ })
// example output:
betterOrderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"

augur.api.Orders.getGasPrice({ orderID, type, market, outcome }, function (gasPrice) { /* ... */ })
// example output:
gasPrice = "42000000000000"

augur.api.Orders.getLastOutcomePrice({ market, outcome }, function (lastOutcomePrice) { /* ... */ })
// example output:
lastOutcomePrice = "490000000000000000"

augur.api.Orders.getOrderMoneyEscrowed({ orderID, type, market, outcome }, function (orderMoneyEscrowed) { /* ... */ })
// example output:
orderMoneyEscrowed = "5000000000000000000"

augur.api.Orders.getOrderOwner({ orderId, type, market, outcome }, function (owner) { /* ... */ })
// example output:
owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";

augur.api.Orders.getOrderSharesEscrowed({ orderId, type, market, outcome }, function (orderSharesEscrowed) { /* ... */ })
// example output:
orderSharesEscrowed = "0";

augur.api.Orders.getPrice({ orderId, type, market, outcome }, function (price) { /* ... */ })
// example output:
price = "500000000000000000";

augur.api.Orders.getVolume({ market }, function (volume) { /* ... */ })
// example output:
volume = "100000000000000000000000";

const secondWorstOrderID = "0x4b538f4de2517f7d7bbb227161981c51c40bf725da9941b3dc02e6c14cafd1f1";
augur.api.Orders.getWorseOrderID({ orderID: secondWorstOrderID, type, market, outcome }, function (worseOrderID) { /* ... */ })
// example output:
worseOrderID = "0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api.Orders.getWorstOrderID({ type, market, outcome }, function (worstOrderID) { /* ... */ })
// example output:
worstOrderID = "0x9a8d5523ed521813533d1f8469f5040fa1404fcf470b9da43bccfe38c80ad035"

augur.api.Orders.isBetterPrice({ type, market, outcome, fxpPrice, orderID }, function (isBetterPrice) { /* ... */ })
// example output:
isBetterPrice = "0"

augur.api.Orders.isWorsePrice({ type, market, outcome, fxpPrice, orderID }, function (isWorsePrice) { /* ... */ })
// example output:
isWorsePrice = "1"
```
### [Orders Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/orders.se)

#### augur.api.Orders.assertIsNotBetterPrice({ type, market, outcome, fxpPrice, betterOrderID }[, callback])

Returns wether the specified `fxpPrice` is not a better price than the `betterOrderID` for a given order `type` trading on the `outcome` of the provided `market`. Returns `1` if true, `0` if false.

#### augur.api.Orders.assertIsNotWorsePrice({ type, market, outcome, fxpPrice, worstOrderID }[, callback])

Returns wether the specified `fxpPrice` is not a worst price than the `worstOrderID` for a given order `type` trading on the `outcome` of the provided `market`. Returns `1` if true, `0` if false.

#### augur.api.Orders.getAmount({ orderID, type, market, outcome }[, callback])

Returns the amount of shares requested on a specified `orderID` of `type` trading on the `outcome` in the provided `market`.

#### augur.api.Orders.getBestOrderID({ type, market, outcome }[, callback])

Returns the order ID of the best order on the `market` of `type` trading on the provided `outcome`.

#### augur.api.Orders.getBetterOrderID({ orderID, type, market, outcome }[, callback])

Returns a better order ID than the `orderID` specified on the `market` of `type` trading on the provided `outcome`.

#### augur.api.Orders.getGasPrice({ orderID, type, market, outcome }[, callback])

Returns the gas price for the `orderID` specified on the `market` of `type` trading on the provided `outcome`.

#### augur.api.Orders.getLastOutcomePrice({ market, outcome }[, callback])

Returns the fixed point value of the last price traded for a specified `market` and `outcome`.

#### augur.api.Orders.getOrderMoneyEscrowed({ orderID, type, market, outcome }[, callback])

Returns the fixed point value of the amount of money escrowed by a specified `orderID` of `type` trading on the `market` and `outcome`.

#### augur.api.Orders.getOrderOwner({ orderID, type, market, outcome }[, callback])

Returns the owner address of the specified `orderID` of `type` trading on `market` and `outcome`.

#### augur.api.Orders.getOrderSharesEscrowed({ orderID, type, market, outcome }[, callback])

Returns the fixed point value of the amount of shares escrowed by a specified `orderID` of `type` trading on the `market` and `outcome`.

#### augur.api.Orders.getPrice({ orderID, type, market, outcome }[, callback])

Returns the fixed point value of the price of a specified `orderID` of `type` trading on the `market` and `outcome`.

#### augur.api.Orders.getVolume({ market }[, callback])

Returns the fixed point value of the volume of a specified `market`.

#### augur.api.Orders.getWorseOrderID({ orderID, type, market, outcome }[, callback])

Returns a worse order ID than the `orderID` specified on the `market` of `type` trading on the provided `outcome`.

#### augur.api.Orders.getWorstOrderID({ type, market, outcome }[, callback])

Returns the order ID of the worst order on the `market` of `type` trading on the provided `outcome`.

#### augur.api.Orders.isBetterPrice({ type, market, outcome, fxpPrice, OrderID }[, callback])

Returns wether the specified `fxpPrice` is a better price than the `orderID` for a given order `type` trading on the `outcome` of the provided `market`. Returns `1` if true, `0` if false.

#### augur.api.Orders.isWorsePrice({ type, market, outcome, fxpPrice, OrderID }[, callback])

Returns wether the specified `fxpPrice` is a worst price than the `orderID` for a given order `type` trading on the `outcome` of the provided `market`. Returns `1` if true, `0` if false.

```javascript
// Orders Fetcher Contract
const orderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
const type = "1";
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
const outcome = "1";
const fxpPrice = "450000000000000000"; // 0.45

augur.api.OrdersFetcher.ascendOrderList({ type, market, outcome, fxpPrice, lowestOrderID: orderID }, function (ascendingOrderList) { /* ... */ })
// example output:
ascendingOrderList = [
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870",
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0"]

augur.api.OrdersFetcher.descendOrderList({ type, market, outcome, fxpPrice, highestOrderID: orderID }, function (decendingOrderList) { /* ... */ })
// example output:
decendingOrderList = [
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e",
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870"]

augur.api.OrdersFetcher.findBoundingOrders({ type, market, outcome, fxpPrice, bestOrderID: orderID, worstOrderID: 0, betterOrderID: 0, worseOrderID: 0 }, function (boundingOrders) { /* ... */ })
// example output:
boundingOrders = [
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0",
  "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e"]

augur.api.OrdersFetcher.getOrder({ orderID, type, market, outcome }, function (order) { /* ... */ })
// example output:
order = [ "10000000000000000000",
          "500000000000000000",
          "0x438f2aeb8a16745b1cd711e168581ebce744ffaa",
          "5000000000000000000",
          "0",
          "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0",
          "0x09502d4c2765d61a8e47fd4ada696966f3bc3bce6b780ecedded035e616c272e",
          "42000000000000"  ]

augur.api.OrdersFetcher.getOrderIDs({ type, market, outcome, startingOrderID: orderID, numOrdersToLoad: 2 }, function (orderIDs) { /* ... */ })
// example output:
orderIDs = [
  "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870",
  "0x4a8d07c2c9cd996484c04b7077d1fc4aeaeb8aa4750d7f26f2a896c4393fb6b0"]
```
### [Orders Fetcher Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/ordersFetcher.se)

#### augur.api.OrdersFetcher.ascendOrderList({ type, market, outcome, fxpPrice, lowestOrderID }[, callback])

Returns an array containing the order IDs that should be set to `betterOrderID` and `worseOrderID` respectively for an order inserted at `fxpPrice`. `lowestOrderID` is an order ID expected to be a worse price than the `fxpPrice` specified for an order of `type` trading on `market` around `outcome`.

#### augur.api.OrdersFetcher.descendOrderList({ type, market, outcome, fxpPrice, highestOrderID }[, callback])

Returns an array containing the order IDs that should be set to `betterOrderID` and `worseOrderID` respectively for an order inserted at `fxpPrice`. `highestOrderID` is an order ID expected to be a better price than the `fxpPrice` specified for an order of `type` trading on `market` around `outcome`.

#### augur.api.OrdersFetcher.findBoundingOrders({ type, market, outcome, fxpPrice, bestOrderID, worstOrderID, betterOrderID, worseOrderID }[, callback])

Returns an array containing the order IDs that should be set to `betterOrderID` and `worseOrderID` respectively for an order inserted at `fxpPrice`. `betterOrderID` and `worseOrderID` should be orders that are better or worse than the `fxpPrice` for an order of `type` trading on `market` around `outcome`. `bestOrderID` and `worstOrderID` should be the best and worst order IDs on the order book for the specified `market`, `type`, and `outcome`.

#### augur.api.OrdersFetcher.getOrder({ orderID, type, market, outcome }[, callback])

Returns a length 8 array containing information about a specified `orderID` of `type` trading on `market` around `outcome`. Information returned includes: amount of attoshares, fixed point display price, owner address, tokens escrowed, shares escrowed, better order id, worse order id, and gas price.

#### augur.api.OrdersFetcher.getOrderIDs({ type, market, outcome, startingOrderID, numOrdersToLoad }[, callback])

Returns an array of order IDs of `type` trading on `market` around `outcome` starting from the `startingOrderID` order ID specified. The array will be of length `numOrdersToLoad`.

```javascript
// Share Token Contract
const shareToken = "0x18b17188ce3c491f6ab4427258d92452be5c8054";
const owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";

augur.api.ShareToken.allowance({ shareToken, owner, spender }, function (allowance) { /* ... */ })
// example output:
allowance = "100"

augur.api.ShareToken.balanceOf({ shareToken, address: owner }, function (balance) { /* ... */ })
// example output:
balance = "1000"

augur.api.ShareToken.getDecimals({ shareToken }, function (decimals) { /* ... */ })
// example output:
decimals = "18"

augur.api.ShareToken.getMarket({ shareToken }, function (market) { /* ... */ })
// example output:
market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42"

augur.api.ShareToken.getName({ shareToken }, function (name) { /* ... */ })
// example output:
name = "Shares"

augur.api.ShareToken.getOutcome({ shareToken }, function (outcome) { /* ... */ })
// example output:
outcome = "1"

augur.api.ShareToken.getSymbol({ shareToken }, function (symbol) { /* ... */ })
// example output:
symbol = "SHARES"

augur.api.ShareToken.isShareToken({ shareToken }, function (isShareToken) { /* ... */ })
// example output:
isShareToken = "1"

augur.api.ShareToken.totalSupply({ shareToken }, function (totalSupply) { /* ... */ })
// example output:
totalSupply = "50000"
```
### [Share Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/shareToken.se)

#### augur.api.ShareToken.allowance({ shareToken, owner, spender }[, callback])

Returns the token allowance a `spender` can use of an `owner`'s specified `shareToken`s.

#### augur.api.ShareToken.balanceOf({ shareToken, address }[, callback])

Returns the token balance of a specified `shareToken` owned by the `address`.

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

#### augur.api.ShareToken.totalSupply({ shareToken }[, callback])

Returns the total supply of `shareToken`s specified.

```javascript
// Topics Contract
const topics = "0x14f094c79a676c681e7cc490e775f73072e535ae";
const topic = "Augur";

augur.api.Topics.count({ topics }, function (count) { /* ... */ })
// example output:
count = "152"

augur.api.Topics.getPopularity({ topics, topic }, function (popularity) { /* ... */ })
// example output:
popularity = "1000"

augur.api.Topics.getPopularityByOffset({ topics, offset: 0 }, function () { /* ... */ })
// example output:
popularity = "1000"

augur.api.Topics.getTopicByOffset({ topics, offset: 0 }, function () { /* ... */ })
// example output:
topic = "Augur"
```
### [Topics Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/topics.se)

#### augur.api.Topics.count({ topics }[, callback])

Returns a count of all the Topics at the specified `topics` address.

#### augur.api.Topics.getPopularity({ topics, topic }[, callback])

Returns the popularity value of a specified `topic` at the `topics` address provided.

#### augur.api.Topics.getPopularityByOffset({ topics, offset }[, callback])

Returns the popularity of the Topic at the index `offset` at the `topics` address provided.

#### augur.api.Topics.getTopicByOffset({ topics, offset }[, callback])

Returns the Topic at the index `offset` at the `topics` address provided.


Transaction API
---------------

```javascript
// Transaction API example:
const privateKey = <Buffer ...>;
const reputationTokenAddress = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
const transferTarget = "0xea674fdde714fd979de3edf0f56aa9716b898ec8";
const attotokens = "100000000000000000000"; // 1000.00 REP

// the Augur API is organized by Contract and then Method like so:
// augur.api.<Contract>.<Method>(<argument object>);
augur.api.ReputationToken.transfer({
  _signer: privateKey,
  reputationToken: reputationTokenAddress,
  to: transferTarget,
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example sent output:
sentResponse = {
  txHash: '0x915f8f0b13244b0dd9b7377b252d9245ef0fc109c82931a87410d1bdad671fe6',
  callReturn: '1'
}
// example successful output:
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
// example failed output:
failedResponse = {
  error: '0x',
  message: 'no response or bad input'
}
```
Augur's Transaction API is made up of "setter" methods that can both read from and write to the blockchain using Ethereum's `eth_sendTransaction` RPC. Under the hood, the API uses augur.js's convenience wrapper for `eth_sendTransaction` which can be accessed using `augur.rpc.transact`. Although it is possible to call `augur.rpc.transact` directly, which is discussed in greater detail below, it is generally better and easier to use the built in API functions. The API functions are attached to the `augur.api` object and follow a pattern of `augur.api.<Contract>.<Method>(<Argument Object>)`.

### Arguments

All Transaction API methods accept a single object argument. This single object optionally contains the `onSent`, `onSuccess`, and `onFailed` callback fields. This single object should contain all required params for the transactions as key/value pairs. This is different to the Call API methods which accept a params object and a second optional argument for the callback.

### Callbacks

All Transaction API methods accept three callback functions within the argument object. The following keys are used for each callback:

#### onSent(sentResponse)

Fires when the initial `eth_sendTransaction` response is received.  If the transaction was broadcast to the network without problems, `sentResponse` will have two fields: `txHash` (the transaction hash as a hex string) and `callReturn` (the value returned by the invoked contract method). The optional `returns` field in the `payload` object sent to `augur.rpc.transact` can be used to specify the format of the `callReturn` value.

#### onSuccess(successResponse)

Fires when the transaction is successfully incorporated into a block and added to the blockchain, as indicated by a nonzero `blockHash` value.  `successResponse` is structured the same way as `eth_getTransactionByHash` responses (see code example for details), with the addition of a `callReturn` field which contains the contract method's return value.

#### onFailed(failedResponse)

Fires if the transaction is unsuccessful. `failedResponse` has `error` (error code) and `message` (error description) fields, describing the way in which the transaction failed.

No callbacks are required; if none are supplied, then the transaction hash (or error) will simply be returned. In this case, the transaction has been broadcast to the Ethereum network, but further confirmation of the transaction and/or lookups of its return value will need to be done manually. If an `onSent` but not an `onSuccess` callback is provided, the transaction will be broadcast, and `onSent` will be passed an `sentResponse` object containing `txHash` and `callReturn` fields, but `augur.rpc` will not poll the network repeatedly to check on the status of the transaction.

```javascript
// using augur.rpc.transact() directly example:
// payload object (generated by transfer method of the Cash Contract)
const payload = {
  to: "0x039546e174d6a2f4c9239e08094b114fa82efc23",
  events: ["Transfer"],
  label: "Transfer",
  method: "transfer",
  signature: ['int256', 'int256'],
  fixed: [1],
  from: "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b",
  params: [
    "0x6329a1eb5c9c6c5cd3739fd35a1fd028e50c1456",
    "1000",
  ],
  returns: "unfix",
  send: true
};

// onSent callback fires when the initial eth_sendTransaction response
// is received
const onSent = sentResponse => console.log("transaction sent:", sentResponse);  

// onSuccess callback fires
const onSuccess = successResponse => console.log("transaction successful:", successResponse);

// onFailed callback fires when the transaction is malformed, fails to confirm,
// or an object with an error field is received
const onFailed = failedResponse => console.error("transaction failed:", failedResponse);

// privateKeyorSigner is either the privateKey buffer of the account trying to send the transaction or a function to sign a transaction (generally from a hardware wallet). Below we are going to use the Buffer returned from augur.accounts.login.
const privateKeyOrSigner = <Buffer ...>;

augur.rpc.transact(payload, privateKeyOrSigner, onSent, onSuccess, onFailed);
// example outputs:
sentResponse = {
  txHash: '0xdf096e249638df143118f562868e90285579819e59b09f0784b95fa5fd920413',
  callReturn: '1000'
}
successResponse = {
  nonce: '0x4e1',
  blockHash: '0xb8e4e78c9cf949729bbb7b94e942d8d63e67e9f48b394f3208cf0d2928e44bad',
  blockNumber: '0x6a5f',
  transactionIndex: '0x0',
  from: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  to: '0x039546e174d6a2f4c9239e08094b114fa82efc23',
  value: '0x0',
  gas: '0x2fd618',
  gasPrice: '0xba43b7400',
  input: '0x867445580000000000000000000000006329a1eb5c9c6c5cd3739fd35a1fd028e50c145600000000000000000000000000000000000000000000000000000000000003e8',
  callReturn: '1000',
  txHash: '0xdf096e249638df143118f562868e90285579819e59b09f0784b95fa5fd920413'
}
failedResponse = {
  error: 501,
  message: 'polled network but could not confirm transaction'
}
```
### Using transact directly:

<aside class="warning">While it is possible to use <code>augur.rpc.transact</code> directly, it is generally easier and less error-prone to use one of the named API functions documented in the following sections. Readers who want to use the Transaction API and aren't terribly curious about the augur.js/ethrpc plumbing under the hood should jump to the next section!</aside>

#### augur.rpc.transact(payload, privateKeyOrSigner[, onSent, onSuccess, onFailed])

You can broadcast transactions to the Ethereum Network by calling `augur.rpc.transact` directly. If called directly, the `payload` object needs to be constructed by hand and must be structured correctly.

The `payload` object, also known as the "transaction object", is structured as follows:

**Required:**

- to: `<contract address> (hexstring)`
- name: `<function name> (string)`
- signature: `<function signature, e.g. ["int256", "bytes", "int256[]"]> (array)`
- params: `<parameters passed to the function>`

**Optional:**

- send: `<true to sendTransaction, false to call (default)>`
- from: `<sender's address> (hexstring; defaults to the coinbase account)`
- returns: `<"int256" (default), "int", "number", "int256[]", "number[]", or "string">`

The `params` and `signature` fields are required if your function accepts parameters; otherwise, these fields can be excluded. The `returns` field is used only to format the output, and does not affect the actual RPC request.

The `privateKeyOrSigner` is required if we are attempting to execute a transaction that will modify the blockchain (`eth_sendTransaction`). If we are simply getting information (`eth_call`) we don't need to pass anything for `privateKeyOrSigner`. `privateKeyOrSigner` should be the Private Key Buffer for the logged in account or a function to sign transactions provided by a hardware wallet.

**Under the hood `augur.rpc.transact` carries out the following sequence:**

1. `augur.rpc.transact` will first attempt to use `eth_call` on the transaction submitted in order to determine if there is enough GAS to perform the transaction and that the transaction is properly formed. If you have a malformed transaction, didn't provide enough GAS, or the transaction will fail then an error is passed to the `onFailed` handler and the `augur.rpc.transact` sequence terminates.

2. After confirming that the Transaction is valid, `augur.rpc.transact` will send a `eth_sendTransaction` RPC request (or `eth_sendRawTransaction` for transactions which are already signed), which broadcasts the transaction to the Ethereum Network. If no transaction hash is received or there is an error, then the error will be passed to the `onFailed` handler and the `augur.rpc.transact` sequence will terminate. Otherwise, the `onSent` handler will be called and return an object containing the `txHash` and `callReturn`.

3. After calling the `onSent` handler, Augur will add the transaction to the `transactions` object (which is indexed by transaction hash, e.g. `transactions[txHash]`) and assign the transaction a `status` of `"pending"`. Use `augur.rpc.getTransactions()` to access the `transactions` object.

4. Augur then uses `eth_getTransactionByHash` to determine if the transaction has been mined or not, indicated by a `null` response. A `null` response indicates that the transaction has been (silently) removed from geth's transaction pool. This can happen if the transaction is a duplicate of another transaction that has not yet cleared the transaction pool (and therefore geth does not fire a duplicate transaction error), or if the transaction's nonce (but not its other fields) is a duplicate. If a `null` response is received from `eth_getTransactionByHash` then Augur will attempt to re-submit the transaction to `augur.rpc.transact` as long the amount of attempts hasn't exceeded `augur.constants.TX_RETRY_MAX`. If the attempts to submit the transaction exceed `augur.constants.TX_RETRY_MAX` then a `TRANSACTION_RETRY_MAX_EXCEEDED` error will be sent to the `onFailed` handler and the `augur.rpc.transact` sequence will terminate.

5. Once the transaction has been successfully mined (`eth_getTransactionByHash` successfully returns the transaction object) the transaction is updated to include the `blockNumber` and `blockHash` and it's `status` is changed to `"sealed"`.

6. When the amount of confirmations of our transaction exceeds `augur.constants.REQUIRED_CONFIRMATIONS` then the transaction is updated to a status of `"confirmed"`. A `callReturn` field is added to the transaction object, which is then passed to the `onSuccess` handler, completing the sequence.

<aside class="notice">The <code>augur.rpc</code> object is simply an instance of <a href="https://github.com/AugurProject/ethrpc">ethrpc</a> that has its state synchronized with the <code>augur</code> object.</aside>


```javascript
// Dispute Bond Token Contract
// privateKey for the msg.sender of these transactions
const privateKey = <Buffer ...>;
const disputeBondToken = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
const destinationAddress = "0xaa895acf2091752393384b902f813da761ca421f";

augur.api.DisputeBondToken.transfer({
  _signer: privateKey,
  disputeBondToken,
  destinationAddress,
  attotokens: 1,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320480,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x6b0f32ca16855ab6a680ebff87f63837c78e1fcdd94f909cac9acf7768e73384",
  input: "0x86744558000000000000000000000000aa895acf2091752393384b902f813da761ca421f0000000000000000000000000000000000000000000000000000000000000001",
  nonce: "0x1",
  timestamp: 1501003125,
  to: "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4",
  value: "0x0"
}

augur.api.DisputeBondToken.withdraw({
  _signer: privateKey,
  disputeBondToken,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320481,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xf5681056bab571e0ec73411896a3e8f7a7b610e43f148ea96cd06f66a2e8472a",
  input: "0x3ccfd60b",
  nonce: "0x3",
  timestamp: 1501003126,
  to: "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4",
  value: "0x0"
}

const shadyBranch = "0x580f633f475614636ee132a0a355dcdc826d16c8";
augur.api.DisputeBondToken.withdrawToBranch({
  _signer: privateKey,
  disputeBondToken,
  shadyBranch,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320483,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xd3a9a88dd49f9eb485498190013fe8004df40a2549e5c2f3aafb452aab0a7a98",
  input: "0x8ac17bbb000000000000000000000000580f633f475614636ee132a0a355dcdc826d16c8",
  nonce: "0x4",
  timestamp: 1501003128,
  to: "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4",
  value: "0x0"
}
```
### [Dispute Bond Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/reporting/disputeBondToken.se)

#### augur.api.DisputeBondToken.transfer({ \_signer, disputeBondToken, destinationAddress, attotokens[, onSent, onSuccess, onFailed ]})

The `transfer` transaction will change the current bond holder to the specified `destinationAddress`. This transaction will fail if the `msg.sender` isn't the bond holder of the specified `disputeBondToken` or if the value of `attotokens` isn't equal to `1`. This transaction will spawn a `Transfer` event which will record the from address (`msg.sender`), to address (`destinationAddress`), and `attotokens` amount transferred (`1`). As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.DisputeBondToken.withdraw({ \_signer, disputeBondToken[, onSent, onSuccess, onFailed ]})

This transaction is used by the bond holder of the specified `disputeBondToken` to withdraw reputation tokens earned by correctly disputing the outcome of the `disputeBondToken`'s market that hasn't caused a fork. This transaction will fail to pay out reputation tokens if the `msg.sender` isn't the bond holder for the specified `disputeBondToken`, if the market for the `disputeBondToken` isn't finalized, if the market is finalized but the final payout distribution hash is the same distribution hash challenged by the `disputeBondToken`, or if this `disputeBondToken`'s market has caused a fork. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.DisputeBondToken.withdrawToBranch({ \_signer, disputeBondToken, shadyBranch[, onSent, onSuccess, onFailed ]})

This transaction is used by the bond holder of the specified `disputeBondToken` to withdraw reputation tokens earned by correctly disputing the outcome of the `disputeBondToken`'s market that has caused a fork. This transaction will fail to pay out reputation tokens if the `msg.sender` isn't the bond holder for the specified `disputeBondToken`, if the `shadyBranch` isn't the child branch of the branch containing this `disputeBondToken`, if this `disputeBondToken`'s market has not caused a fork, if the payout distribution hash for the parent branch of `shadyBranch` is the same distribution hash challenged by the `disputeBondToken`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Market Contract
const privateKey = <Buffer ...>;
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
const payoutNumerators = [ 0, 2 ];

augur.api.Market.approveSpenders({
  _signer: privateKey,
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

augur.api.Market.automatedReport({
  _signer: privateKey,
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
augur.api.Market.changeCreator({
  _signer: privateKey,
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

augur.api.Market.decreaseMarketCreatorSettlementFeeInAttoethPerEth({
  _signer: privateKey,
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

augur.api.Market.disputeAllReporters({
  _signer: privateKey,
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

augur.api.Market.disputeAutomatedReport({
  _signer: privateKey,
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

augur.api.Market.disputeLimitedReporters({
  _signer: privateKey,
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

augur.api.Market.migrateThroughAllForks({
  _signer: privateKey,
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

augur.api.Market.migrateThroughOneFork({
  _signer: privateKey,
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

augur.api.Market.tryFinalize({
  _signer: privateKey,
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

augur.api.Market.tryFinalizeAllReporting({
  _signer: privateKey,
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

augur.api.Market.tryFinalizeAutomatedReport({
  _signer: privateKey,
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

augur.api.Market.tryFinalizeFork({
  _signer: privateKey,
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

augur.api.Market.tryFinalizeLimitedReporting({
  _signer: privateKey,
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
augur.api.Market.updateTentativeWinningPayoutDistributionHash({
  _signer: privateKey,
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

#### augur.api.Market.approveSpenders({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction calls a number of `approve` transactions for the `market`'s `denominationToken` and `shareTokens` to allow other contracts the ability to transfer around the `market`'s tokens so the `market` can function. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.automatedReport({ \_signer, market, payoutNumerators[, onSent, onSuccess, onFailed ]})

This transaction is used by the `automatedReporter` for a specified `market` to report the winning outcome, by submitting `payoutNumerators`, of the `market`. This transaction will fail if the `msg.sender` isn't the `automatedReporterAddress` set for this `market`, or if this `market` isn't in the Automated Reporting Phase. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.changeCreator({ \_signer, market, newCreator[, onSent, onSuccess, onFailed ]})

Changes the `market` creator address to the `newCreator` address submitted. This transaction will fail if the `msg.sender` isn't set as the creator of the `market`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.decreaseMarketCreatorSettlementFeeInAttoethPerEth({ \_signer, market, newFeePerEthInWei[, onSent, onSuccess, onFailed ]})

Lowers the `market` creator's settlement fee in attoeth per `Eth` settled to the `newFeePerEthInWei` value. This transaction will fail if the `msg.sender` is not the creator of the `market`, if `newFeePerEthInWei` is 0 or less, or if `newFeePerEthInWei` isn't a lower number than the current fee per `Eth`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.disputeAllReporters({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction will trigger a dispute of the all reporters phase of reporting for the specified `market`. This transaction will take the bond amount from the `msg.sender` and then move the `market` into the upcoming reporting window. This transaction will fail if the `market` isn't in the all reporters dispute phase of reporting. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.disputeAutomatedReport({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction will trigger a dispute of the automated report phase of reporting for the specified `market`. This transaction will take the bond amount from the `msg.sender` and then update the `market` reporting phase to limited reporting. This transaction will fail if the `market` isn't in the automated dispute phase of reporting or if the `market` has been finalized. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.disputeLimitedReporters({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction will trigger a dispute of the limited report phase of reporting for the specified `market`. This transaction will take the bond amount from the `msg.sender` and then move the `market` into the next reporting window for all reporting. This transaction will fail if the `market` isn't in the limited dispute phase of reporting. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.migrateThroughAllForks({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction will call `migrateThroughOneFork` repeatedly until the `market` has moved through all forks or has reached an active fork which will throw. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.migrateThroughOneForks({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction will move the `market` onto the active branch following a fork and refund bond holders for limited or all reporting disputes. This transaction will fail if no move is required or if the forked market isn't finalized. Returns `1` if a move occurred, `0` if no move occurred, and throws if the forking market isn't finalized. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.tryFinalize({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction will attempt to finalize the `market` by calling 'tryFinalizeAutomatedReport', 'tryFinalizeLimitedReporting', 'tryFinalizeAllReporting', and 'tryFinalizeFork' in that order. If the `market` becomes finalized after any of those calls, this transaction will return `1`. If none of those calls finalized the market then this transaction returns `0`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.tryFinalizeAllReporting({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction will attempt to finalize the `market`'s all reporting phase by first moving the `market` through all forks, changing the `market` to a finalized state, and pays reward to bond holders. This transaction will fail if this `market` hasn't gone through a limited reporters phase, this `market` doesn't have a limited reporters dispute bond holder, this `market` does have an all reporters dispute bond holder, or if this `market`'s reporting window isn't complete. Returns `1` if successful, `0` if the `market` can't be finalized. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.tryFinalizeAutomatedReport({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction will attempt to finalize the `market`'s Automated reporting phase by changing the `market` to a finalized state, and pays reward to automated report bond holders. This transaction will fail if this `market` doesn't have a reported winning outcome, if this `market` doesn't have a Automated Reporter Dispute Bond Holder, or if this `market`'s automated report dispute period hasn't ended. Returns `1` if successful, `0` if the `market` can't be finalized. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.tryFinalizeFork({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction will attempt to finalize `market` after it caused a fork and pays out rewards to the bond holders. This transaction will fail if the market hasn't gone through the limited reporters phase, if the market doesn't have a limited reporter dispute bond holder, if the market hasn't gone through the all reporters phase, if the market doesn't have a all reporter dispute bond holder, if this market isn't the market that caused a fork, if this market doesn't have a winning branch, if the winning branch doesn't have at least half the total supply of REP (5.5 million), or if it's before the fork end time. Returns `1` if successful, `0` if the `market` can't be finalized. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.tryFinalizeLimitedReporting({ \_signer, market[, onSent, onSuccess, onFailed ]})

This transaction will attempt to finalize the `market`'s limited reporting phase by first moving the `market` through all forks, changing the `market` to a finalized state, and pays reward to bond holders. This transaction will fail if this `market` hasn't gone through a automated report phase, this `market` doesn't have a automated report dispute bond holder, or if this `market`'s reporting window isn't complete. Returns `1` if successful, `0` if the `market` can't be finalized. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Market.updateTentativeWinningPayoutDistributionHash({ \_signer, market, payoutDistributionHash[, onSent, onSuccess, onFailed ]})

This transaction will attempt to update the `tentativeWinningPayoutDistributionHash` for this `market` to the `payoutDistributionHash` provided. This transaction will not update the `tentativeWinningPayoutDistributionHash` if it already has a value and it's supply of reporting tokens is higher than the `payoutDistributionHash`'s supply of reporting tokens. This transaction will fail if the `payoutDistributionHash` provided isn't a hash contained within this `market`'s reporting tokens. Returns `1`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Registration Token Contract
const privateKey = <Buffer ...>;
const registrationToken = "0x8385755a52e85df2f571ce5e1550e5472c639352";
const spenderAddress = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";
const attotokens = "100000000000000000000";

augur.api.RegistrationToken.approve({
  _signer: privateKey,
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

augur.api.RegistrationToken.redeem({
  _signer: privateKey,
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

augur.api.RegistrationToken.register({
  _signer: privateKey,
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

augur.api.RegistrationToken.transfer({
  _signer: privateKey,
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

const sourceAddress = "0x39d3b15006e580077a2e8b51b93be90ccf1ec0e0";
augur.api.RegistrationToken.transferFrom({
  _signer: privateKey,
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

#### augur.api.RegistrationToken.approve({ \_signer, registrationToken, spenderAddress, attotokens[, onSent, onSuccess, onFailed ]})

Allows the `spenderAddress` the ability to spend up to `attotokens` worth of the specified `registrationToken` for the sender of this `approve` transaction (`msg.sender`). This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `spenderAddress`, and `attotokens` value approved. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.RegistrationToken.redeem({ \_signer, registrationToken[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of this transaction has a `registrationToken`, has completed reporting, and the reporting window for the specified `registrationToken` has completed then transfer the bond amount of reputation tokens to the `msg.sender`. Currently the bond amount is set to 10<sup>18</sup> attotokens, or 1 `REP`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.RegistrationToken.register({ \_signer, registrationToken[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of this transaction has the bond amount of `REP`, the reporting window for this `registrationToken` hasn't started yet, and the `msg.sender` doesn't already have a `registrationToken` then this transaction will transfer the bond amount of `reputationTokens` out of the `msg.sender`'s wallet and transfer 1 `registrationToken` to `msg.sender` in return. Currently the bond amount is set to 10<sup>18</sup> attotokens, or 1 `REP`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.RegistrationToken.transfer({ \_signer, registrationToken, destinationAddress, attotokens[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of the `transfer` transaction has enough of `registrationToken` to be able to transfer `attotokens` worth to the `destinationAddress` and `attotokens` is a valid value between 1 and 2<sup>254</sup> then this transaction will send `attotokens` worth of `registrationToken` to the specified `destinationAddress` from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address, to address, and `attotokens` amount transferred. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.RegistrationToken.transferFrom({ \_signer, registrationToken, sourceAddress, destinationAddress, attotokens[, onSent, onSuccess, onFailed ]})

If the `sourceAddress` of the `transferFrom` transaction has enough of `registrationToken` to be able to transfer `attotokens` worth to the `destinationAddress`, `attotokens` is a valid value between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `attotokens` worth of `registrationToken` for `sourceAddress` then this transaction will send `attotokens` worth of `registrationToken` to the specified `destinationAddress` from the `sourceAddress`. This transaction will spawn a `Transfer` event which will record the from address, to address, and `attotokens` amount transferred. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Reporting Token Contract
const privateKey = <Buffer ...>;
const reportingToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
const spenderAddress = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";
const attotokens = "100000000000000000000";

augur.api.ReportingToken.approve({
  _signer: privateKey,
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

augur.api.ReportingToken.buy({
  _signer: privateKey,
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

augur.api.ReportingToken.extractBondHolderPayments({
  _signer: privateKey,
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

augur.api.ReportingToken.migrateLosingTokens({
  _signer: privateKey,
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

augur.api.ReportingToken.redeemDisavowedTokens({
  _signer: privateKey,
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

augur.api.ReportingToken.redeemForkedTokens({
  _signer: privateKey,
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

augur.api.ReportingToken.redeemWinningTokens({
  _signer: privateKey,
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

augur.api.ReportingToken.transfer({
  _signer: privateKey,
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
augur.api.ReportingToken.transferFrom({
  _signer: privateKey,
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

#### augur.api.ReportingToken.approve({ \_signer, reportingToken, spenderAddress, attotokens[, onSent, onSuccess, onFailed ]})

Allows the `spenderAddress` the ability to spend up to `attotokens` worth of the specified `reportingToken` for the `msg.sender` of this `approve` transaction. This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `spenderAddress`, and `attotokens` value approved. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReportingToken.buy({ \_signer, reportingToken, attotokens[, onSent, onSuccess, onFailed ]})

Purchases `attotokens` worth of `reportingToken` for `msg.sender` using the `msg.sender`'s `reputationToken`s. If the `msg.sender` doesn't have `attotokens` worth of `reputationToken`, if `attotokens` value isn't between 1 and 2<sup>254</sup>, the `market` for this `reportingToken` can't be reported on, or the `msg.sender` doesn't have a `registrationToken` for the `reportingWindow` containing the `reportingToken`'s `market` then this transaction will fail. This transaction will spawn a `Transfer` event which will record the from address, to address, and the amount of `attotokens` purchased. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReportingToken.extractBondHolderPayments({ \_signer, reportingToken[, onSent, onSuccess, onFailed ]})

Extracts reputation from the `reportingToken` and transfers it to the `market` for this `reportingToken`. Does nothing if the `market` already has the desired amount of `REP` to reward a successful dispute. Returns 1 if reputation was moved from `reportingToken` to the market, returns 0 if the market already has enough `REP` to reward a successful dispute. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReportingToken.migrateLosingTokens({ \_signer, reportingToken[, onSent, onSuccess, onFailed ]})

Sets the supply of this `reportingToken` to 0 and transfers all `REP` on this `reportingToken` to this `reportingToken`'s `market`'s winning reporting token contract. This will fail if the `market` isn't finalized, the `market` isn't a container for this `reportingToken`, the `market` for this `reportingToken` is the cause of a fork, and the `market`'s winning reporting token isn't this `reportingToken`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReportingToken.redeemDisavowedTokens({ \_signer, reportingToken, reporter[, onSent, onSuccess, onFailed ]})

Transfers `REP` to the `reporter` from the `reportingToken` for the value of `reportingToken` owned by `reporter`. This transaction will fail if the `reportingToken` is still contained within the `reportingToken`'s `market`. This transaction will spawn a `Transfer` event which will record the from address, to address, and the amount of `attotokens` of `REP` transferred to the `reporter`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReportingToken.redeemForkedTokens({ \_signer, reportingToken, reporter[, onSent, onSuccess, onFailed ]})

Transfers `REP` to the `reporter` on the new `branch` for the amount of `reportingToken`s owned by `reporter`. This transaction will fail if the `msg.sender` isn't the `reporter` address, if the `market` isn't a container for the `reportingToken`, or if the `market` isn't the cause of a fork. This transaction will spawn a `Transfer` event which will record the from address, to address, and the amount of `attotokens` of `REP` transferred to the `reporter` on the new branch. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReportingToken.redeemWinningTokens({ \_signer, reportingToken, reporter[, onSent, onSuccess, onFailed ]})

Transfers `REP` to the `reporter` based on how many `reportingToken`s the `reporter` owned. This transaction will fail if the `msg.sender` isn't the `reporter`, if the `market` isn't finalized, if the `market` isn't a container for `reportingToken`, if the `market` is the cause of a fork, or if the `reportingToken` isn't the winning `reportingToken`.
This transaction will spawn a `Transfer` event which will record the from address, to address, and the amount of `attotokens` of `REP` transferred to the `reporter`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReportingToken.transfer({ \_signer, reportingToken, destinationAddress, attotokens[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of the `transfer` transaction has enough of `reportingToken` to be able to transfer `attotokens` worth to the `destinationAddress` and `attotokens` is a valid value between 1 and 2<sup>254</sup> then this transaction will send `attotokens` worth of `reportingToken` to the specified `destinationAddress` from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address, to address, and `attotokens` amount transferred. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReportingToken.transferFrom({ \_signer, reportingToken, sourceAddress, destinationAddress, attotokens[, onSent, onSuccess, onFailed ]})

If the `sourceAddress` of the `transferFrom` transaction has enough of `reportingToken` to be able to transfer `attotokens` worth to the `destinationAddress`, `attotokens` is a valid value between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `attotokens` worth of `reportingToken` for `sourceAddress` then this transaction will send `attotokens` worth of `registrationToken` to the specified `destinationAddress` from the `sourceAddress`. This transaction will spawn a `Transfer` event which will record the from address, to address, and `attotokens` amount transferred. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Reporting Window Contract
const privateKey = <Buffer ...>;
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

augur.api.ReportingWindow.createNewMarket({
  _signer: privateKey,
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

#### augur.api.ReportingWindow.createNewMarket({ \_signer, reportingWindow, endTime, numOutcomes, payoutDenominator, feePerEthInWei, denominationToken, creator, minDisplayPrice, maxDisplayPrice, automatedReporterAddress, topic[, onSent, onSuccess, onFailed ]})

This function will create a new market for the given `reportingWindow` that will be constructed using the arguments passed and return the new market's address if successful. This transaction will fail if the `numOutcomes` value isn't within the range of 2 and 8, if the `payoutDenominator` isn't between 2 and 2<sup>254</sup>, if the current time is not before the start time of the `reportingWindow`, if the `payoutDenominator` isn't a multiple of `numOutcomes`, if `feesPerEthInWei` is lower than or equal to 0 or greater than or equal to 0.5 ETH (5 * 10<sup>18</sup>), if the `maxDisplayPrice` and `minDisplayPrice` isn't between -2<sup>254</sup> to 2<sup>254</sup>, if  `maxDisplayPrice` - `minDisplayPrice` must be between 1 and 2<sup>254</sup>, or if the `msg.value` amount sent isn't enough to cover the market's validity bond and the estimated gas cost for the target amount of reporters to report. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Reputation Token Contract
const privateKey = <Buffer ...>;
const reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
const spender = "0xea674fdde714fd979de3edf0f56aa9716b898ec8";
const source = "0x1a05071893b764109f0bbc5b75d78e3e38b69ab3";
const attotokens = "100000000000000000000";

augur.api.ReputationToken.approve({
  _signer: privateKey,
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

augur.api.ReputationToken.migrateFromLegacyRepContract({
  _signer: privateKey,
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
augur.api.ReputationToken.migrateOut({
  _signer: privateKey,
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

augur.api.ReputationToken.transfer({
  _signer: privateKey,
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

augur.api.ReputationToken.transferFrom({
  _signer: privateKey,
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

#### augur.api.ReputationToken.approve({ \_signer, reputationToken, spender, value[, onSent, onSuccess, onFailed ]})

Allows the `spender` the ability to spend up to `value` (denoted in attotokens) worth of the specified `reputationToken` for the `msg.sender` of this `approve` transaction. This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `spender`, and `value` in attotokens approved. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReputationToken.migrateFromLegacyRepContract({ \_signer, reputationToken[, onSent, onSuccess, onFailed ]})

This function will migrate REP tokens from the legacy rep contract owned by `msg.sender` to the `reputationToken` provided. `msg.sender` will add whatever `msg.sender`'s balance was for the legacy rep contract to the `reputationToken` contract. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReputationToken.migrateOut({ \_signer, reputationToken, destination, reporter, attotokens[, onSent, onSuccess, onFailed ]})

This function migrates a `reporter`'s REP (amount of REP denoted in `attotokens`) from one `reputationToken` address to another (`destination`). The `msg.sender` of this transaction must be the `reporter` provided or the `msg.sender` must be approved to spend `attotokens` amount of REP for the `reporter` provided. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReputationToken.transfer({ \_signer, reputationToken, to, value[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of the `transfer` transaction has enough of `reputationToken` to be able to transfer `value` (denoted in attotokens) worth to the `to` address and `value` is a number between 1 and 2<sup>254</sup> then this transaction will send `value` (in attotokens) worth of `reputationToken` to the specified `to` address from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address (`msg.sender`), `to` address, and `value` amount transferred. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ReputationToken.transferFrom({ \_signer, reputationToken, from, to, value[, onSent, onSuccess, onFailed ]})

If the `from` address of the `transferFrom` transaction has enough of `reputationToken` to be able to transfer `value` (denoted in attotokens) worth to the `to` address, `value` is a number between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `value` worth of `reputationTokens` for the `from` address then this transaction will send `value` worth of `reputationToken` to the specified `to` address from the `from` address. This transaction will spawn a `Transfer` event which will record the `from` address, `to` address, and `value` (in attotokens) amount transferred. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Cancel Order Contract
const privateKey = <Buffer ...>;
const orderID = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
const type = "1";
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
const outcome = "1";

augur.api.CancelOrder.publicCancelOrder({
  _signer: privateKey,
  orderID,
  type,
  market,
  outcome,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
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
  hash: "0xa031604c8ef75ab5b1d07f7358d594e1cb57c927a86be92459d78ae3415bb3b0",
  input: "0x309d23e17ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f87000000000000000000000000000000000000000000000000000000000000000010000000000000000000000009368ff3e9ce1c0459b309fac6dd4e69229b91a420000000000000000000000000000000000000000000000000000000000000001",
  nonce: "0xf61",
  timestamp: 1501003156,
  to: "0xb2930b35844a230f00e51431acae96fe543a0347",
  value: "0x0"
}
```
### [Cancel Order Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/cancelOrder.se)

#### augur.api.CancelOrder.publicCancelOrder({ \_signer, orderID, type, market, outcome[, onSent, onSuccess, onFailed ]})

The `publicCancelOrder` transaction is used to cancel and refund an existing order on the specified `market` of `type` for the `outcome` given it's `orderID`. This will fail if `msg.sender` isn't the owner of the order, if the `market` or `orderID` is not defined, or if `type` is not an expected value (`1` for a `BID`, `2` for an `ASK`). As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Cash Token Contract
const privateKey = <Buffer ...>;
const owner = "0x438f2aeb8a16745b1cd711e168581ebce744ffaa";
const spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";
const attotokens = "100000000000000000000";

augur.api.Cash.approve({
  _signer: privateKey,
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

augur.api.Cash.publicDepositEther({
  _signer: privateKey,
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

augur.api.Cash.publicWithdrawEther({
  _signer: privateKey,
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

augur.api.Cash.transfer({
  _signer: privateKey,
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

augur.api.Cash.transferFrom({
  _signer: privateKey,
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

#### augur.api.Cash.approve({ \_signer, spender, value[, onSent, onSuccess, onFailed ]})

Allows the `spender` the ability to spend up to `value` (denoted in attotokens) worth of Cash Tokens for the `msg.sender` of this `approve` transaction. This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `spender`, and `value` in attotokens approved. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Cash.publicDepositEther({ \_signer, value[, onSent, onSuccess, onFailed ]})

This transaction is used to convert Ether (ETH) into a Cash token that is used on the augur markets. `value` is the amount of Ether (ETH) denoted in attotokens to deposit into the Cash Token Contract for the `msg.sender`. This will spawn a `DepositEther` event which will record the owner address (`msg.sender`), `value` deposited, and the total balance for this `msg.sender`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Cash.publicWithdrawEther({ \_signer, to, amount[, onSent, onSuccess, onFailed ]})

This transaction is used to convert Cash Tokens back into Ether (ETH) by sending the `amount` of `msg.sender`'s CASH Tokens `to` the address specified denoted in attotokens. This transaction requires a 3-day wait period from the initial call to withdraw. Once three days have passed, calling `publicWithdrawEther` again will withdraw the `amount` specified. This transaction will fail if we have initiated a withdraw but it hasn't been 3 days since the initiated withdraw, if the `msg.sender` doesn't have at least `amount` of Cash Tokens denoted in attotokens, or if the `amount` specified is less than 1. This transaction can spawn two different events depending on when it was called. If a withdraw hasn't been initiated then calling `publicWithdrawEther` will spawn a `InitiateWithdrawEther` event which records the `msg.sender`, the `amount` specified for withdraw, and the current balance of Cash Tokens for the `msg.sender`. If it has been at least 3 days since we have initiated a withdraw then when the withdraw takes place, this transaction will spawn a `WithdrawEther` event. The `WithdrawEther` event records `msg.sender`, the `amount` withdrawn, and the balance of Cash Tokens after withdrawal has been completed. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Cash.transfer({ \_signer, to, value[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of the `transfer` transaction has enough of Cash Tokens to be able to transfer `value` (denoted in attotokens) worth to the `to` address and `value` is a number between 1 and 2<sup>254</sup> then this transaction will send `value` (in attotokens) worth of Cash Tokens to the specified `to` address from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address (`msg.sender`), `to` address, and `value` amount transferred. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Cash.transferFrom({ \_signer, from, to, value[, onSent, onSuccess, onFailed ]})

If the `from` address of the `transferFrom` transaction has enough Cash Tokens to be able to transfer `value` (denoted in attotokens) worth to the `to` address, `value` is a number between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `value` worth of Cash Tokens for the `from` address then this transaction will send `value` worth of Cash Tokens to the specified `to` address from the `from` address. This transaction will spawn a `Transfer` event which will record the `from` address, `to` address, and `value` (in attotokens) amount transferred. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Claim Proceeds Contract
const privateKey = <Buffer ...>;
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.ClaimProceeds.publicClaimProceeds({
  _signer: privateKey,
  market,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320522,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x0f6152b9a39055b34bc092d9e96e852e3137e3d42f4e78b1dc5848f8b2abf12f",
  input: "0x5fd65fba0000000000000000000000009368ff3e9ce1c0459b309fac6dd4e69229b91a42",
  nonce: "0xfc",
  timestamp: 1501003122,
  to: "0x96c7ed5f9465a8661df4df3bbbf16cc13ad7e115",
  value: "0x0"
}
```
### [Claim Proceeds Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/claimProceeds.se)

#### augur.api.ClaimProceeds.publicClaimProceeds({ \_signer, market[, onSent, onSuccess, onFailed ]})

The `publicClaimProceeds` transaction attempts to collect trading profits from outstanding shares in a finalized `market` owned by the `msg.sender`. This transaction will fail if the `market` specified is not finalized or if it hasn't been at least 3 days since the `market` was finalized. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Complete Sets Contract
const privateKey = <Buffer ...>;
const market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
const fxpAmount = "50000000000000000000" // 50.0

augur.api.CompleteSets.publicBuyCompleteSets({
  _signer: privateKey,
  market,
  fxpAmount,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320523,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xc77149d6a3fef8755a20aa2100fc79c02ca7dd198d6f3c65aabe638883d8d017",
  input: "0xfadc758a0000000000000000000000009368ff3e9ce1c0459b309fac6dd4e69229b91a42000000000000000000000000000000000000000000000002b5e3af16b1880000",
  nonce: "0xfd",
  timestamp: 1501003123,
  to: "0xfbb1b73c4f0bda4f67dca266ce6ef42f520fbb98",
  value: "0x0"
}

augur.api.CompleteSets.publicSellCompleteSets({
  _signer: privateKey,
  market,
  fxpAmount,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320524,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x21121bf9a8d32969b5ce4d53b6021ad6b7b5e7c658e9d98d582c720c8abce220",
  input: "0x04b586670000000000000000000000009368ff3e9ce1c0459b309fac6dd4e69229b91a42000000000000000000000000000000000000000000000002b5e3af16b1880000",
  nonce: "0xfe",
  timestamp: 1501003124,
  to: "0xfbb1b73c4f0bda4f67dca266ce6ef42f520fbb98",
  value: "0x0"
}
```
### [Complete Sets Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/completeSets.se)

#### augur.api.CompleteSets.publicBuyCompleteSets({ \_signer, market, fxpAmount[, onSent, onSuccess, onFailed ]})

This transaction will attempt to purchase `fxpAmount` worth of shares in all outcomes for a specified `market`. This transaction will fail if the `msg.sender` doesn't have enough of the `market`'s denomination token to be able to afford `fxpAmount` shares in all outcomes, or if the `fxpAmount` is not a number between 1 and 2<sup>254</sup>. When successful this transaction will spawn a `CompleteSets` event which will record the `msg.sender`, `market`, type (buy), `fxpAmount` purchased, number of outcomes in the `market`, `marketCreatorFee`, and the `reportingFee`. During a buy, the `marketCreatorFee` and `reportingFee` will be `0` because no fees are paid for purchasing shares, only selling/settling shares. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.CompleteSets.publicSellCompleteSets({ \_signer, market, fxpAmount[, onSent, onSuccess, onFailed ]})

This transaction will attempt to sell `fxpAmount` worth of shares in all outcomes for a specified `market`. This transaction will fail if the `msg.sender` doesn't own `fxpAmount` of shares in all outcomes for the `market`, or if the `fxpAmount` is not a number between 1 and 2<sup>254</sup>. When successful this transaction will spawn a `CompleteSets` event which will record the `msg.sender`, `market`, type (sell), `fxpAmount` sold, number of outcomes in the `market`, `marketCreatorFee` paid for selling the shares, and the `reportingFee` paid for selling the shares. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Make Order Contract
const privateKey = <Buffer ...>;
const type = "1";
const attoshares = "10000000000000000000"; // 10 shares
const displayPrice = "0.5";
const market = "0xd3273eba07248020bf98a8b560ec1576a612102f";
const outcome = "1";
const betterOrderID = "0xea2c7476e61f5e2625e57df17fcce74741d3c2004ac657675f686a23d06e6091";
const worseOrderID = "0xed42c0fab97ee6fbde7c47dc62dc3ad09e8d3af53517245c77c659f7cd561426";
const tradeGroupID = "1";

augur.api.MakeOrder.publicMakeOrder({
  _signer: privateKey,
  type,
  attoshares,
  displayPrice,
  market,
  outcome,
  betterOrderID,
  worseOrderID,
  tradeGroupID,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320525,
  callReturn: "0x5f80029d47ca806002b6b6bbbb0077124f8da9b69a885f7714907bd773bcf8a7",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xcf6729b0e27fffc81c4f5c824992a780daf1f41bf590c7f62f16777edbc2c08e",
  input: "0x138dcef200000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000008ac7230489e8000000000000000000000000000000000000000000000000000000000000fffffff1000000000000000000000000d3273eba07248020bf98a8b560ec1576a612102f0000000000000000000000000000000000000000000000000000000000000001ea2c7476e61f5e2625e57df17fcce74741d3c2004ac657675f686a23d06e6091ed42c0fab97ee6fbde7c47dc62dc3ad09e8d3af53517245c77c659f7cd5614260000000000000000000000000000000000000000000000000000000000000001",
  nonce: "0xff",
  timestamp: 1501003125,
  to: "0x82378b6ee7b6e8c69874b491e4488c72a390c367",
  value: "0x0"
}
```
### [Make Order Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/makeOrder.se)

#### augur.api.MakeOrder.publicMakeOrder({ \_signer, type, attoshares, displayPrice, market, outcome[, betterOrderID, worseOrderID, tradeGroupID, onSent, onSuccess, onFailed ]})

This transaction will create a new order on the order book for the specified `market` trading on the `outcome` provided. The required fields besides market and outcome are the `type` of order (1 for a bid, 2 for an ask), amount of shares denoted in `attoshares`, and the `displayPrice` for the order. Optional params include `betterOrderID`, `worseOrderID`, `tradeGroupID`, and the callbacks. The `betterOrderID` and `worseOrderID` are orderIDs of orders already on the order book which should be better and worse than the order we are intending to create with this transaction. The `tradeGroupID` is a field used by the Augur UI to group transactions and can be left blank.

This transaction will fail if `type` is not a valid value of 1 or 2, If the `attoshares` value is less than 0, if the `market` isn't defined, if the `outcome` is less than 0 or greater than the total number of outcomes for the `market`, or if the `displayPrice` is below the `market`'s minimum `displayPrice` or if the `displayPrice` is above the market's maximum `displayPrice`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Share Token Contract
const privateKey = <Buffer ...>;
const shareToken = "0xa22c79a48f51df6d0863821bd1dd2c5d6f511bc5";
const spenderAddress = "0x01f50356c280cd886dd058210937160c73700a4b";
const attotokens = "100000000000000000000";

augur.api.ShareToken.approve({
  _signer: privateKey,
  shareToken,
  spender: spenderAddress,
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320526,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x02730239e82d37ec032ecde79f27ae75d7cc59c277ab44c6eb5b67520ee487e9",
  input: "0x83b5863800000000000000000000000001f50356c280cd886dd058210937160c73700a4b0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xff1",
  timestamp: 1501003126,
  to: "0xa22c79a48f51df6d0863821bd1dd2c5d6f511bc5",
  value: "0x0"
}
augur.api.ShareToken.transfer({
  _signer: privateKey,
  shareToken,
  to: spenderAddress,
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320527,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x7dfbd9d87964814e0da2d874d8d7c2d886df6b852a46d69562018620d62fd773",
  input: "0x8674455800000000000000000000000001f50356c280cd886dd058210937160c73700a4b0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xff2",
  timestamp: 1501003127,
  to: "0xa22c79a48f51df6d0863821bd1dd2c5d6f511bc5",
  value: "0x0"
}

const sourceAddress = "0x4b01721f0244e7c5b5f63c20942850e447f5a5ee";
augur.api.ShareToken.transferFrom({
  _signer: privateKey,
  shareToken,
  from: sourceAddress,
  to: spenderAddress,
  value: attotokens,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result)
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320528,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x619a9d4a75f43cf5d5b8e81365f9859e60aa96554915e912cdf7ffd96f0d4d96",
  input: "0x27f08b000000000000000000000000004b01721f0244e7c5b5f63c20942850e447f5a5ee00000000000000000000000001f50356c280cd886dd058210937160c73700a4b0000000000000000000000000000000000000000000000056bc75e2d63100000",
  nonce: "0xff3",
  timestamp: 1501003128,
  to: "0xa22c79a48f51df6d0863821bd1dd2c5d6f511bc5",
  value: "0x0"
}
```
### [Share Token Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/shareToken.se)

#### augur.api.ShareToken.approve({ \_signer, shareToken, spender, value[, onSent, onSuccess, onFailed ]})

Allows the `spender` the ability to spend up to `value` worth of the specified `shareToken` for the `msg.sender` of this `approve` transaction. This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `spender`, and `value` denoted in attotokens approved. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ShareToken.transfer({ \_signer, shareToken, to, value[, onSent, onSuccess, onFailed ]})

If the `msg.sender` of the `transfer` transaction has enough of `shareToken`s to be able to transfer `value` worth to the `to` address and `value` is a valid number between 1 and 2<sup>254</sup> then this transaction will send `value` worth of `shareToken` to the specified `to` address from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address (`msg.sender`), `to` address, and `value` amount transferred denoted in attotokens. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.ShareToken.transferFrom({ \_signer, shareToken, from, to, value[, onSent, onSuccess, onFailed ]})

If the `from` address of the `transferFrom` transaction has enough of `shareToken` to be able to transfer `value` worth to the `to` address, `value` is a valid number between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `value` worth of `shareToken` for `from` address then this transaction will send `value` worth of `shareToken` to the specified `to` address from the `from` address. This transaction will spawn a `Transfer` event which will record the `from` address, `to` address, and `value` amount transferred denoted in attotokens. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Take Order Contract
const privateKey = <Buffer ...>;
const orderID = "0xea2c7476e61f5e2625e57df17fcce74741d3c2004ac657675f686a23d06e6091";
const type = "1";
const market = "0x71a4e3899629f6023cde649a40982eda46ef2777";
const outcome = "1";
const fxpAmountTakerWants = "10000000000000000000"; // 10.0
const tradeGroupID = "1";

augur.api.TakeOrder.publicTakeOrder({
  _signer: privateKey,
  orderID,
  type,
  market,
  outcome,
  fxpAmountTakerWants,
  tradeGroupID,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320529,
  callReturn: "0",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x3efb4102dc3b9e1bb145ca21310233646a4eba24894b04f12ee4d390281301ac",
  input: "0x49a2cba0ea2c7476e61f5e2625e57df17fcce74741d3c2004ac657675f686a23d06e6091000000000000000000000000000000000000000000000000000000000000000100000000000000000000000071a4e3899629f6023cde649a40982eda46ef277700000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000008ac7230489e800000000000000000000000000000000000000000000000000000000000000000001",
  nonce: "0xff4",
  timestamp: 1501003129,
  to: "0x077f28f038dd94ed9c444b806137302b1c4cbd5a",
  value: "0x0"
}
```
### [Take Order Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/takeOrder.se)

#### augur.api.TakeOrder.publicTakeOrder({ \_signer, orderID, type, market, outcome, fxpAmountTakerWants[, tradeGroupID, onSent, onSuccess, onFailed ]})

Given an `orderID`, the `type` of order, the `market` containing this order, the `outcome` this order trades on, and the amount a taker wants as `fxpAmountTakerWants` denoted in attoshares this transaction will attempt to fill the order specified. If the `fxpAmountTakerWants` is enough to fill the order completely then the order will be removed from the order book, otherwise it will be adjusted to only include the remaining amount after filling the `fxpAmountTakerWants` value that the taker requests. This transaction requires `orderID`, `type`, `market`, `outcome`, and `fxpAmountTakerWants` are defined. The maker of the order specified by `orderID` cannot be the `msg.sender` of this transaction. This transaction will return the fixed point amount remaining of the order specified by `orderID` after being filled, if it's completely filled this will return `0`. The `tradeGroupID` is an optional value that is used by the Augur UI and can be left `undefined`. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

```javascript
// Trade Contract
const privateKey = <Buffer ...>;
const market = "0x7e8e07364ccde43ba5159537404924e86ca53c92";
const outcome = "1";
const fxpAmount = "10000000000000000000"; // 10.0
const fxpPrice = "500000000000000000"; // 0.5
const tradeGroupID = "1";

augur.api.Trade.publicBuy({
  _signer: privateKey,
  market,
  outcome,
  fxpAmount,
  fxpPrice,
  tradeGroupID,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320530,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x027e3b312f5949388830a0f5c945cbfcbf4ec06edc3b342ef8d6e85f631a50c3",
  input: "0x888b82010000000000000000000000007e8e07364ccde43ba5159537404924e86ca53c9200000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000008ac7230489e8000000000000000000000000000000000000000000000000000006f05b59d3b200000000000000000000000000000000000000000000000000000000000000000001",
  nonce: "0xff5",
  timestamp: 1501003130,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  value: "0x0"
}

augur.api.Trade.publicSell({
  _signer: privateKey,
  market,
  outcome,
  fxpAmount,
  fxpPrice,
  tradeGroupID,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320531,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x1072dd3501dd0796f3b986bb45704798d198265ca5b75303488a8eb69c76bae5",
  input: "0xf049066b0000000000000000000000007e8e07364ccde43ba5159537404924e86ca53c9200000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000008ac7230489e8000000000000000000000000000000000000000000000000000006f05b59d3b200000000000000000000000000000000000000000000000000000000000000000001",
  nonce: "0xff6",
  timestamp: 1501003131,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  value: "0x0"
}

augur.api.Trade.publicTrade({
  _signer: privateKey,
  direction: '1',
  market,
  outcome,
  fxpAmount,
  fxpPrice,
  tradeGroupID,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320532,
  callReturn: "0xa754c0437ff499df19d163199dcb43a539cbd0a9670b976f0ac66f33a88b3ac6",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x2dc8b6f31e83e0e558863528130af2c66309f582ed02d96c7d7cfbe6a2c7179f",
  input: "0x1ee6ba1000000000000000000000000000000000000000000000000000000000000000010000000000000000000000007e8e07364ccde43ba5159537404924e86ca53c9200000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000008ac7230489e8000000000000000000000000000000000000000000000000000006f05b59d3b200000000000000000000000000000000000000000000000000000000000000000001",
  nonce: "0xff7",
  timestamp: 1501003132,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  value: "0x0"
}

augur.api.Trade.publicTakeBestOrder({
  _signer: privateKey,
  direction: '2',
  market,
  outcome,
  fxpAmount,
  fxpPrice,
  tradeGroupID,
  onSent: (result) => console.log(result),
  onSuccess: (result) => console.log(result),
  onFailed: (result) => console.log(result),
});
// example output:
successResponse = {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320533,
  callReturn: "0",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x7ab9bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1aefd1",
  input: "0x048db15000000000000000000000000000000000000000000000000000000000000000020000000000000000000000007e8e07364ccde43ba5159537404924e86ca53c9200000000000000000000000000000000000000000000000000000000000000010000000000000000000000000000000000000000000000008ac7230489e8000000000000000000000000000000000000000000000000000006f05b59d3b200000000000000000000000000000000000000000000000000000000000000000001",
  nonce: "0xff8",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  value: "0x0"
}
```
### [Trade Contract](https://github.com/AugurProject/augur-core/blob/develop/src/trading/trade.se)

#### augur.api.Trade.publicBuy({ \_signer, market, outcome, fxpAmount, fxpPrice[, tradeGroupID, onSent, onSuccess, onFailed ]})

The `publicBuy` transaction is used to purchase shares for a specified `market` and `outcome`. The amount of shares you wish to purchase is `fxpAmount` denoted in attoshares and the price point you would like to buy at is `fxpPrice` denoted in attotokens of the `market`'s denomination token. This transaction will take orders off the order book that can be filled with this request, otherwise this transaction will create a new order to buy `fxpAmount` of shares at `fxpPrice`. The `tradeGroupID` is an optional argument used by the Augur UI and can be left `undefined`. This transaction returns `1` if this order was filled completely, or if this order can't be filled immediately an order will be created and the `orderID` of that new order will be returned. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Trade.publicSell({ \_signer, market, outcome, fxpAmount, fxpPrice[, tradeGroupID, onSent, onSuccess, onFailed ]})

The `publicSell` transaction is used to purchase shares for a specified `market` and `outcome`. The amount of shares you wish to purchase is `fxpAmount` denoted in attoshares and the price point you would like to sell at is `fxpPrice` denoted in attotokens of the `market`'s denomination token. This transaction will take orders off the order book that can be filled with this request, otherwise this transaction will create a new order to sell `fxpAmount` of shares at `fxpPrice`. The `tradeGroupID` is an optional argument used by the Augur UI and can be left `undefined`. This transaction returns `1` if this order was filled completely, or if this order can't be filled immediately an order will be created and the `orderID` of that new order will be returned. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Trade.publicTrade({ \_signer, direction, market, outcome, fxpAmount, fxpPrice[, tradeGroupID, onSent, onSuccess, onFailed ]})

The `publicTrade` transaction is works exactly like `publicBuy` or `publicSell` however a direction must be specified this time. The `direction` must be either `1` for buying or `2` for selling. This transaction returns `1` if this order was filled completely, or if this order can't be filled immediately an order will be created and the `orderID` of that new order will be returned. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).

#### augur.api.Trade.publicTakeBestOrder({ \_signer, direction, market, outcome, fxpAmount, fxpPrice[, tradeGroupID, onSent, onSuccess, onFailed ]})

The `publicTakeBestOrder` transaction will work very similarly to `publicTrade` except it will not create an order if the request can't be filled. The `direction` must be either `1` for buying or `2` for selling. This transaction returns the fixed point amount not filled by the order, so `0` for a completely filled order, some other number if this request could only be partially filled. As with all transactions that will modify the blockchain, a `_signer` is required and should be the `privateKey` Buffer for the account sending the transaction or a signing function (hardware wallets).
