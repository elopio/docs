Transaction API
================
<aside class="notice">The Transaction section is still under construction and may be missing some information. Don't worry! We plan to update the entire documentation prior to Augur launching. Thank you for your patience as we make these updates.</aside>

```javascript
// Transaction API (Tx API for short) example:
var privateKey = <Buffer ...>;
var reputationTokenAddress = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
var transferTarget = "0xea674fdde714fd979de3edf0f56aa9716b898ec8";
var attotokens = "100000000000000000000"; // 1000.00 REP

// the Augur API is organized by Contract and then Method like so:
// augur.api.<Contract>.<Method>(<argument object>);
augur.api.ReputationToken.transfer({
  _signer: privateKey,
  reputationToken: reputationTokenAddress,
  _to: transferTarget,
  _value: attotokens,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSent output:
{
  callReturn: '100000000000000000000',
  hash: '0x915f8f0b13244b0dd9b7377b252d9245ef0fc109c82931a87410d1bdad671fe6'

}
// example onSuccess output:
{
  blockHash: "0xdc5e31404be698a6866fbe7d7cf435a2c6fab7deb3acf41d6c9bb981a2630fed",
  blockNumber: 1330897,
  callReturn: "100000000000000000000",
  from: "0x40485264986740c8fb3d11e814bd94cf86012d29",
  gas: "0x2fd618",
  gasFees: "0.00081266",
  gasPrice: "0x4a817c800",
  hash: "0x915f8f0b13244b0dd9b7377b252d9245ef0fc109c82931a87410d1bdad671fe6",
  input: "0x39d26051",
  nonce: "0x0",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1512018567,
  to: "0xfc6bba7d664e8cc242857f069e9fb1e7c25ecb44",
  transactionIndex: "0x1",
  v: "0x2c",
  value: "0x0"
}
// example onFailed output:
{
  error: '0x',
  message: 'no response or bad input'
}
```
Augur's Transaction API ("Tx API" for short) is made up of "setter" methods that can both read from and write to the blockchain using Ethereum's `eth_sendTransaction` RPC. Under the hood, the API uses augur.js's convenience wrapper for `eth_sendTransaction` which can be accessed using `augur.rpc.transact`. Although it is possible to call `augur.rpc.transact` directly (which is discussed in greater detail below), it is generally better and easier to use the built in API functions. The API functions are attached to the `augur.api` object and follow a pattern of `augur.api.<Contract>.<Method>(<Argument Object>)`. The API method name, as well as its parameters as keys in the params object, are generally identical to those of the underlying smart contract method.

### Arguments

All Transaction API methods accept a single object argument. This single object must contain the `onSent`, `onSuccess`, and `onFailed` callback fields. This single object should contain all required params for the transactions as key/value pairs. This is different to the [Call API](#call-api) methods which accept a params object and a second optional argument for the callback. It is **strongly recommended** that you use a callback, even if it's optional in the Call API, as that will ensure your requests are made asynchronously.

### Callbacks

All Transaction API methods accept three callback functions within the argument object. The following keys are used for each callback:

#### onSent(sentResponse)

Fires when the initial `eth_sendTransaction` response is received.  If the transaction was broadcast to the network without problems, `sentResponse` will have two fields: `txHash` (the transaction hash as a hex string) and `callReturn` (the value returned by the invoked contract method). The optional `returns` field in the `payload` object sent to `augur.rpc.transact` can be used to specify the format of the `callReturn` value.

#### onSuccess(successResponse)

Fires when the transaction is successfully incorporated into a block and added to the blockchain, as indicated by a nonzero `blockHash` value. `successResponse` is structured the same way as `eth_getTransactionByHash` responses (see code example for details), with the addition of a `callReturn` field which contains the contract method's return value.

#### onFailed(failedResponse)

Fires if the transaction is unsuccessful. `failedResponse` has `error` (error code) and `message` (error description) fields, describing the way in which the transaction failed.

### Signer

In addition to the arguments and callbacks, all Transaction API functions require a `_signer` param. The `_signer` param should be set to the private key buffer or a signing function, provided by a hardware wallet, of the account who wishes to initiate the transaction. The Transaction API functions all attempt to modify information on the blockchain which requires a signed transaction. Anytime you plan to modify or "set" information on the blockchain you will need to include a `_signer` param. This concept is discussed in a bit more detail in the next section.

Using Transact Directly
-----------------------
```javascript
// using augur.rpc.transact() directly example:
// payload object (generated by transfer method of the ReputationToken Contract)
var payload = {
  constant: false,  
  from: "0x40485264986740c8fb3d11e814bd94cf86012d29", 
  name: "getOrCacheMarketCreationCost",
  params: [],
  returns: "uint256",
  send: true,
  to: "0x6eabb9367012c0a84473e1e6d7a7ce39a54d77bb"
};

// privateKeyorSigner is either the privateKey buffer of the account trying to send the transaction or a function to sign a transaction (generally from a hardware wallet). Below we are going to use the Buffer returned from augur.accounts.login.
var privateKeyOrSigner = <Buffer ...>;

// accountType is a string set to "privateKey", "uPort", or "ledger".
var accountType = "privateKey";

// onSent callback fires when the initial eth_sendTransaction response is received
var onSent = function (sentResponse) { console.log("Transaction sent: ", sentResponse); };

// onSuccess callback fires
var onSuccess = function (successResponse) { console.log("Transaction successful: ", successResponse); };

// onFailed callback fires when the transaction is malformed, fails to confirm,
// or an object with an error field is received
var onFailed = function (failedResponse) { console.error("Transaction failed: ", failedResponse); };

augur.rpc.transact(payload, privateKeyOrSigner, accountType, onSent, onSuccess, onFailed);
// example onSent output:
Transaction sent: {
  callReturn: "10000000006000000",
  hash: "0x269011fe4ed9c7370f8e8237c525062988e8fcce485d93a1a6a419bb3a8e70d3"
}
// example onSuccess output:
Transaction successful: {
  blockHash: "0x5090c1a25a2accf4cb47a1d99f4fa4215146ac29402688ad3e554169af332e4c",
  blockNumber: 1348278,
  callReturn: "10000000006000000",
  from: "0x40485264986740c8fb3d11e814bd94cf86012d29",
  gas: "0x2fd618",
  gasFees: "0.001882121837379",
  gasPrice: "0x5b793ccec",
  hash: "0x0f8e4cdf3de1aa3c01bea8b12cbde32b2bc7701d2bc1b6403634cdd5999ad729",
  input: "0xec86fdbd",
  nonce: "0x12",
  r: "0x3bedf73900da86c9afb6721e472a268108c66694a589b3083935a3c3cc0cc764",
  s: "0x2c9c24f3bbdd63ba2218de2eddb120f52e966d36fa82989ef6b1e3df0b205ce2",
  timestamp: 1512278406,
  to: "0x6eabb9367012c0a84473e1e6d7a7ce39a54d77bb",
  transactionIndex: "0x1",
  v: "0x2c",
  value: "0x0"
}
//example onFailed output: 
Transaction failed: {
  error: 501,
  message: 'polled network but could not confirm transaction'
}
```

<aside class="warning">While it is possible to use <code>augur.rpc.transact</code> directly, it is generally easier and less error-prone to use one of the named API functions documented in the following sections. Readers who want to use the Transaction API and aren't terribly curious about the augur.js/ethrpc plumbing under the hood should jump to the next section!</aside>

#### augur.rpc.transact(payload, privateKeyOrSigner, accountType, onSent, onSuccess, onFailed)

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

The `accountType` is a string that can be "privateKey", "uPort", or "ledger", depending on the type of account that is sending the transaction.

**Under the hood `augur.rpc.transact` carries out the following sequence:**

1. `augur.rpc.transact` will first attempt to use `eth_call` on the transaction submitted in order to determine if there is enough gas to perform the transaction and that the transaction is properly formed. If you have a malformed transaction, didn't provide enough gas, or the transaction will fail then an error is passed to the `onFailed` handler and the `augur.rpc.transact` sequence terminates.

2. After confirming that the Transaction is valid, `augur.rpc.transact` will send a `eth_sendTransaction` RPC request (or `eth_sendRawTransaction` for transactions which are already signed), which broadcasts the transaction to the Ethereum Network. If no transaction hash is received or there is an error, then the error will be passed to the `onFailed` handler and the `augur.rpc.transact` sequence will terminate. Otherwise, the `onSent` handler will be called and return an object containing the `txHash` and `callReturn`.

3. After calling the `onSent` handler, Augur will add the transaction to the `transactions` object (which is indexed by transaction hash, e.g. `transactions[txHash]`) and assign the transaction a `status` of `"pending"`. Use `augur.rpc.getTransactions()` to access the `transactions` object.

4. Augur then uses `eth_getTransactionByHash` to determine if the transaction has been mined or not, indicated by a `null` response. A `null` response indicates that the transaction has been (silently) removed from geth's transaction pool. This can happen if the transaction is a duplicate of another transaction that has not yet cleared the transaction pool (and therefore geth does not fire a duplicate transaction error), or if the transaction's nonce (but not its other fields) is a duplicate. If a `null` response is received from `eth_getTransactionByHash` then Augur will attempt to re-submit the transaction to `augur.rpc.transact` as long the amount of attempts hasn't exceeded `augur.constants.TX_RETRY_MAX`. If the attempts to submit the transaction exceed `augur.constants.TX_RETRY_MAX` then a `TRANSACTION_RETRY_MAX_EXCEEDED` error will be sent to the `onFailed` handler and the `augur.rpc.transact` sequence will terminate.

5. Once the transaction has been successfully mined (`eth_getTransactionByHash` successfully returns the transaction object) the transaction is updated to include the `blockNumber` and `blockHash` and its `status` is changed to `"sealed"`.

6. When the amount of confirmations of our transaction exceeds `augur.constants.REQUIRED_CONFIRMATIONS` then the transaction is updated to a status of `"confirmed"`. A `callReturn` field is added to the transaction object, which is then passed to the `onSuccess` handler, completing the sequence.

<aside class="notice">The <code>augur.rpc</code> object is simply an instance of <a href="https://github.com/AugurProject/ethrpc">ethrpc</a> that has its state synchronized with the <code>augur</code> object.</aside>

Cancel Order Tx API
----------------------------
```javascript
// Cancel Order Contract Transaction API Examples:
var _orderId = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";
var _type = "1";
var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var _outcome = "1";

augur.api.CancelOrder.cancelOrder({
  _orderId: _orderId,
  _type: _type,
  _market: _market,
  _outcome: _outcome,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Cancel Order Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/cancelOrder.se)

#### augur.api.CancelOrder.cancelOrder({ \_orderId, \_type, \_market, \_outcome, onSent, onSuccess, onFailed })

The `cancelOrder` transaction is used to cancel and refund an existing order on the specified `_market` of `_type` for the `_outcome` given its `_orderId`. This will fail if `msg.sender` isn't the owner of the order, if the `_market` or `_orderId` is not defined, or if `_type` is not an expected value (`1` for a `BID`, `2` for an `ASK`). It returns true if the order was successfully canceled. (NOTE: The return value cannot be obtained reliably when calling externally.)

Claim Trading Proceeds Tx API
------------------------------
```javascript
// Claim Trading Proceeds Contract Transaction API Examples:
var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.ClaimTradingProceeds.claimTradingProceeds({
  _market: _market,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```

#### [Claim Trading Proceeds Contract Code](https://github.com/AugurProject/augur-core/blob/master/src/trading/ClaimProceeds.sol)

#### augur.api.ClaimTradingProceeds.claimTradingProceeds({ \_market, onSent, onSuccess, onFailed })

The `claimTradingProceeds` transaction attempts to collect trading profits from outstanding shares in a finalized `_market` owned by the `msg.sender`. This transaction will fail if the `_market` specified is not finalized or if it hasn't been at least 3 days since the `_market` was finalized. (This 3-day waiting period is intended as a security precaution. In the event that an attacker could somehow cause a Market to Finalize incorrectly, the Augur team would have 3 days to notice and halt the Augur system before the attacker could claim the proceeds.)

Complete Sets Tx API
-----------------------------
```javascript
// Complete Sets Contract
var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var _amount = "50000000000000000000" // 50.0

augur.api.CompleteSets.publicBuyCompleteSets({
  _market: _market,
  _amount: _amount,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.CompleteSets.publicSellCompleteSets({
  _market: _market,
  _amount: _amount,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Complete Sets Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/CompleteSets.sol)

#### augur.api.CompleteSets.publicBuyCompleteSets({ \_market, \_amount, onSent, onSuccess, onFailed })

This transaction will attempt to purchase `_amount` worth of shares in all outcomes for a specified `_market`. This transaction will fail if the `msg.sender` doesn't have enough of the `_market`'s denomination token to be able to afford `_amount` shares in all outcomes, or if the `_amount` is not a number between 1 and 2<sup>254</sup>. When successful this transaction will spawn a `CompleteSets` event which will record the `msg.sender`, `_market`, type (buy), `_amount` purchased, number of outcomes in the `_market`, `marketCreatorFee`, and the `reportingFee`. During a buy, the `marketCreatorFee` and `reportingFee` will be `0` because no fees are paid for purchasing shares, only selling/settling shares.

#### augur.api.CompleteSets.publicSellCompleteSets({ \_market, \_amount, onSent, onSuccess, onFailed })

This transaction will attempt to sell `_amount` worth of shares in all outcomes for a specified `_market`. This transaction will fail if the `msg.sender` doesn't own `_amount` of shares in all outcomes for the `_market`, or if the `_amount` is not a number between 1 and 2<sup>254</sup>. When successful this transaction will spawn a `CompleteSets` event which will record the `msg.sender`, `_market`, type (sell), `_amount` sold, number of outcomes in the `_market`, `marketCreatorFee` paid for selling the shares, and the `reportingFee` paid for selling the shares.

Create Order Tx API
--------------------------
```javascript
// Make Order Contract Transaction API Examples:
var _type = "1";
var _attoshares = "10000000000000000000"; // 10 shares
var _displayPrice = "0.5";
var _market = "0xd3273eba07248020bf98a8b560ec1576a612102f";
var _outcome = "1";
var _betterOrderId = "0xea2c7476e61f5e2625e57df17fcce74741d3c2004ac657675f686a23d06e6091";
var _worseOrderId = "0xed42c0fab97ee6fbde7c47dc62dc3ad09e8d3af53517245c77c659f7cd561426";
var _tradeGroupId = "0x0000000000000000000000000000000000000000000000000000000000000001";

augur.api.CreateOrder.publicCreateOrder({
  _type: _type,
  _attoshares: _attoshares,
  _displayPrice: _displayPrice,
  _market: _market,
  _outcome: _outcome,
  _betterOrderId: _betterOrderId,
  _worseOrderId: _worseOrderId,
  _tradeGroupId: _tradeGroupId,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Create Order Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/CreateOrder.sol)

#### augur.api.CreateOrder.publicCreateOrder({ \_type, \_attoshares, \_displayPrice, \_market, \_outcome, \_betterOrderId, \_worseOrderId, \_tradeGroupId, onSent, onSuccess, onFailed })

This transaction will create a new order on the order book for the specified `_market` trading on the `_outcome` provided. The required fields besides market and outcome are the `_type` of order (1 for a bid, 2 for an ask), amount of shares denoted in `_attoshares`, and the `_displayPrice` for the order. Optional params include `_betterOrderId`, `_worseOrderId`, `_tradeGroupId`, and the callbacks. The `_betterOrderId` and `_worseOrderId` are orderIDs of orders already on the order book which should be better and worse than the order we are intending to create with this transaction. The `_tradeGroupId` is a field used by the Augur UI to group transactions and can be left blank.

This transaction will fail if `_type` is not a valid value of 1 or 2, If the `_attoshares` value is less than 0, if the `_market` isn't defined, if the `_outcome` is less than 0 or greater than the total number of outcomes for the `_market`, or if the `_displayPrice` is below the `_market`'s minimum `_displayPrice` or if the `_displayPrice` is above the market's maximum `_displayPrice`.

Dispute Bond Tx API
----------------------------------
```javascript
// Dispute Bond Contract Transaction API Examples:
var disputeBond = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
var _newOwner = "0xaa895acf2091752393384b902f813da761ca421f";

augur.api.DisputeBond.transferOwnership({
  disputeBond: disputeBond,
  _newOwner: _newOwner,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.DisputeBond.withdraw({
  disputeBond: disputeBond,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.DisputeBond.withdrawDisavowedTokens({
  disputeBond: disputeBond,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320482,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xf5681056bab571e0ec73411896a3e8f7a7b610e43f148ea96cd06f66a2e8472a",
  input: "0x3ccfd60b",
  nonce: "0x3",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.DisputeBond.withdrawInEmergency({
  disputeBond: disputeBond,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320483,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xf5681056bab571e0ec73411896a3e8f7a7b610e43f148ea96cd06f66a2e8472a",
  input: "0x00000001",
  nonce: "0x3",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

var _shadyUniverse = "0x580f633f475614636ee132a0a355dcdc826d16c8";
augur.api.DisputeBond.withdrawToUniverse({
  disputeBond: disputeBond,
  _shadyUniverse: _shadyUniverse,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320484,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xd3a9a88dd49f9eb485498190013fe8004df40a2549e5c2f3aafb452aab0a7a98",
  input: "0x8ac17bbb000000000000000000000000580f633f475614636ee132a0a355dcdc826d16c8",
  nonce: "0x4",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Dispute Bond Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/DisputeBond.sol)

The [Dispute Bond](#dispute-bond) Token is used by [REP](#rep) holders to [Challenge](#challenge) the [Outcome](#outcome) of [Markets Awaiting Finalization](#market-awaiting-finalization). The Dispute Bond is only purchasable with REP. Only one Dispute Bond needs to be purchased for a Market per round of [Reporting](#reporting) in order for the Market to move to the next state of [Reporting](#report) in the next upcoming [Reporting Window](#reporting-window). If a Challenge is successful, which means the [Final Outcome](#final-outcome) of the [Market](#market) is something other than the [Tentative Outcome](#tentative-outcome) that was disputed, then the Bond Holder is entitled to up to 2x the Dispute Bond cost in REP. After 2x the cost of the Dispute Bond is paid to the Bond Holder any remaining REP is redistributed as normal to other Reporters who correctly staked on the Final Outcome.

#### augur.api.DisputeBond.transferOwnership({ disputeBond, \_newOwner, onSent, onSuccess, onFailed })

The `transferOwnership` transaction will change the current bond holder to the specified `_newOwner`. This is used to transfer ownership of a Dispute Bond from one address to another. This transaction will fail if the `msg.sender` isn't the bond holder of the specified `disputeBond`. 

#### augur.api.DisputeBond.withdraw({ disputeBond, onSent, onSuccess, onFailed })

This transaction is used by the bond holder of the specified `disputeBond` to withdraw [Reputation Tokens](#rep) earned by correctly [Challenging](#challenge) the [Tentative Outcome](#tentative-outcome) of the `disputeBond`'s [Market](#market). This transaction will fail if the `msg.sender` isn't the bond holder for the specified `disputeBond`, if the Market for the `disputeBond` isn't [Finalized](#finalized-market), if the Market is Finalized but the final [Payout Distribution Hash](#payout-distribution-hash) is the same Distribution Hash Challenged by the `disputeBond`, or if the `disputeBond`'s Market isn't Finalized.

#### augur.api.DisputeBond.withdrawDisavowedTokens({ disputeBond, onSent, onSuccess, onFailed })

This transaction is used by the bond holder of the specified `disputeBond` to withdraw [Reputation Tokens](#rep) in the event that another [Market](#market) forks. This transaction will fail if the `msg.sender` isn't the bond holder for the specified `disputeBond` or if the Market for the `disputeBond` is the [Forked Market](#forked-market).

#### augur.api.DisputeBond.withdrawInEmergency({ disputeBond, onSent, onSuccess, onFailed })

If a critical bug or vulnerability is found in Augur, the development team can put it the system into a [halted](#developer-mode) state until the issue is resolved. In such instances, most regularly-used functions in Augur's backend will become unuseable until the system is returned to its normal state. When this happens, users can call the `withdrawInEmergency` function to withdraw their Dispute Bond and convert it into a Reputation Token.

This transaction will fail if Augur is not currently in a halted state.

It returns true if the Dispute Bond was successfully withdrawn and converted to a Reputation Token. (NOTE: The return value cannot be obtained reliably when calling externally.)

#### augur.api.DisputeBond.withdrawToUniverse({ disputeBond, \_shadyUniverse, onSent, onSuccess, onFailed })

This transaction is used by the bond holder of the specified `disputeBond` to withdraw [Reputation Tokens](#rep) earned by correctly [Challenging](#challenge) the [Tentative Outcome](#tentative-outcome) of the `disputeBond`'s [Market](#market). This transaction will fail to payout if the `msg.sender` isn't the bond holder for the specified `disputeBond`, if the `_shadyUniverse` isn't the [Child Universe](#child-universe) of the [Universe](#universe) containing this `disputeBond`, if the [Payout Distribution Hash](#payout-distribution-hash) for the [Parent Universe](#parent-universe) of `shadyUniverse` is the same Distribution Hash Challenged by the `disputeBond`.

Fill Order Tx API
--------------------------
```javascript
// Fill Order Contract Transaction API Examples:
var _orderId = "0xea2c7476e61f5e2625e57df17fcce74741d3c2004ac657675f686a23d06e6091";
var _amountFillerWants = "10000000000000000000"; // 10.0
var _tradeGroupId = "0x0000000000000000000000000000000000000000000000000000000000000002";

augur.api.FillOrder.publicFillOrder({
  _orderId: _orderId,
  _amountFillerWants: _amountFillerWants,
  _tradeGroupId: _tradeGroupId,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320529,
  callReturn: "0",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x3efb4102dc3b9e1bb145ca21310233646a4eba24894b04f12ee4d390281301ac",
  input: "0x49a2cba0ea2c7476e61f5e2625e57df17fcce74741d3c2004ac657675f686a23d06e60910000000000000000000000000000000000000000000000008ac7230489e800000000000000000000000000000000000000000000000000000000000000000001",
  nonce: "0xff4",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Fill Order Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/TakeOrder.sol)

#### augur.api.FillOrder.publicFillOrder({ \_orderID, \_amountFillerWants, \_tradeGroupID, onSent, onSuccess, onFailed })

Given an `_orderId` and the amount a [Filler](#order-filler) wants as `_amountFillerWants` denoted in attoshares this transaction will attempt to fill the order specified. If the `_amountFillerWants` is enough to fill the order completely then the order will be removed from the order book, otherwise it will be adjusted to only include the remaining amount after filling the `_amountFillerWants` value that the filler requests. This transaction requires `_orderId` and `_amountFillerWants` are defined. The [Creator](#order-creator) of the order specified by `_orderId` cannot be the `msg.sender` of this transaction. This transaction will return the fixed point amount remaining of the order specified by `_orderId` after being filled, if it's completely filled this will return `0`. The `_tradeGroupId` is an optional value that is used by the Augur UI and can be left `undefined`.

Mailbox Tx API
----------------------
```javascript
// Mailbox Contract Transaction API Examples:
var mailbox = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.Mailbox.transferOwnership({
  mailbox: mailbox,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
})
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320488,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x6544da2190a54542e95473a6c9b18b7259480b8b48953eaa750d3e1379d8ccd6",
  input: "0x4c92c4b38832d970d090b6403905de4ec2d1ae0adf4ca4972343c27fa86f7c61ea62e250",
  nonce: "0x4",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Mailbox.withdrawEther({
  mailbox: mailbox,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
})
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320489,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x6544da2190a54542e95473a6c9b18b7259480b8b48953eaa750d3e1379d8ccd6",
  input: "0x4c92c4b38832d970d090b6403905de4ec2d1ae0adf4ca4972343c27fa86f7c61ea62e250",
  nonce: "0x4",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

var _repToken = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
augur.api.Mailbox.withdrawTokens({
  mailbox: mailbox,
  token: _repToken,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
})
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320489,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x6544da2190a54542e95473a6c9b18b7259480b8b48953eaa750d3e1379d8ccd6",
  input: "0x4c92c4b38832d970d090b6403905de4ec2d1ae0adf4ca4972343c27fa86f7c61ea62e250",
  nonce: "0x4",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Mailbox Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/libraries/Mailbox.sol)

#### augur.api.Mailbox.transferOwnership({ mailbox, \_newOwner, onSent, onSuccess, onFailed })

The `transferOwnership` transaction will change the current [Market Creator Mailbox](#market-creator-mailbox) owner to the specified `_newOwner`. This transaction will fail if the `msg.sender` isn't the owner of the specified `mailbox`. 

#### augur.api.Mailbox.withdrawEther({ mailbox, onSent, onSuccess, onFailed })

Transfers all ETH in the [Market Creator Mailbox](#market-creator-mailbox) to the Market Creator's address.  This transaction will fail if the `msg.sender` isn't the owner of the specified `mailbox`. 

#### augur.api.Mailbox.withdrawTokens({ mailbox, \_token, onSent, onSuccess, onFailed })

Transfers all tokens of type `_token` in the [Market Creator Mailbox](#market-creator-mailbox) to the Market Creator's address.  This transaction will fail if the `msg.sender` isn't the owner of the specified `mailbox`. 

Market Tx API
----------------------
```javascript
// Market Contract Transaction API Examples:
var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var _payoutNumerators = [ 0, 1000 ];

var _invalid = false;
augur.api.Market.derivePayoutDistributionHash({
  market: market,
  _payoutNumerators: _payoutNumerators,
  _invalid: _invalid,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
})
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320488,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x6544da2190a54542e95473a6c9b18b7259480b8b48953eaa750d3e1379d8ccd6",
  input: "0x4c92c4b38832d970d090b6403905de4ec2d1ae0adf4ca4972343c27fa86f7c61ea62e250",
  nonce: "0x4",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Market.disavowTokens({
  market: market,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
})
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320490,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x0193add7b8dc3868944cb8b7748ab53cec5a2d9f5041f882b04b0bf0a31e1ff2",
  input: "0xf12afe02",
  nonce: "0x5",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Market.getOrCreateStakeToken({ 
  _payoutNumerators: _payoutNumerators, 
  _invalid: _invalid,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
}
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Market.migrateThroughAllForks({
  market: market,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
})
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Market.migrateThroughOneFork({
  market: market,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
})
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Market.tryFinalize({
  market: market,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
})
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

var _payoutDistributionHash = "0x4480ed40f94e2cb2ca244eb862df2d350300904a96039eb53cba0e34b8ace90a";
augur.api.Market.updateTentativeWinningPayoutDistributionHash({
  market: market,
  _payoutDistributionHash: _payoutDistributionHash,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
})
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Market.withdrawInEmergency {
  market: market,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
}
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320500,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xf5681056bab571e0ec73411896a3e8f7a7b610e43f148ea96cd06f66a2e8472a",
  input: "0x00000001",
  nonce: "0x3",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Market Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Market.sol)

#### augur.api.Market.derivePayoutDistributionHash({ market, \_payoutNumerators, \_invalid, onSent, onSuccess, onFailed })

Returns the [Payout Distribution Hash](#payout-distribution-hash) for the `_payoutNumerators` and `_invalid` passed in. If the [Payout Sets](#payout-set), referred to as Payout Numerator in the function, is valid, then a Payout Distribution Hash is created by hashing the Payout Sets and `_invalid` using keccak256.

#### augur.api.Market.disavowTokens({ market, onSent, onSuccess, onFailed })

This transaction is used to disavow Stake Tokens for this [Market](#market). When a Stake Token is disavowed it is able to be redeemed from the Market for the full [REP](#rep) price of purchase. This is used in the event of a [Fork](#fork), which causes all Markets that aren't [Finalized](#finalized-market) to enter an Awaiting Fork Migration state for the duration of the [Fork Period](#fork-period). During this time, the Market is unable to proceed through the [Market Resolution](#market-resolution) process until the Fork Period is completed and a winning [Child Universe](#child-universe) is known. To withdraw REP from the Staked Tokens on Markets Awaiting Fork Migration the Stake Tokens must be disavowed by calling `disavowTokens` on the Market. The Stake Tokens are then disavowed for the entire Market and can be sold back to the Market for full REP price. This then allows the REP holder to migrate their REP to their chosen Child Universe.

#### augur.api.Market.getOrCreateStakeToken({ \_payoutNumerators, \_invalid , onSent, onSuccess, onFailed })

Returns the Stake Token address for `_payoutNumerators` and `_invalid` on the `market` specified. (If the Stake Token with the specified `payoutNumerators` does not exist, it will be created.) When a [Reporter](#reporter) submits a [Report](#report), they submit a [Payout Set](#payout-set) (`_payoutNumerators`) array indicating how the [Market](#market) should payout. Each Payout Set has a Stake Token associated with it, so if two different Reporters submit the same Payout Set as their Report then they will receive the same Stake Token currency. This method will return the Stake Token associated with a specific Payout Set.

#### augur.api.Market.migrateThroughAllForks({ market, onSent, onSuccess, onFailed })

This transaction will call `migrateThroughOneFork` repeatedly until the [Market](#market) has migrated through all of the [Forks](#fork) or has reached an active Fork which will throw. See the `migrateThroughOneFork` function for a better explanation of what is going on here (the next method).

#### augur.api.Market.migrateThroughOneFork({ market, onSent, onSuccess, onFailed })

This transaction attempts to migrate the [Market](#market) into a winning [Child Universe](#child-universe) from a [Forked](#fork) [Parent Universe](#parent-universe). When a Fork occurs, there is a 60 day long [Fork Period](#fork-period) that occurs where in [REP](#rep) holders migrate their REP to the [Universe](#universe) they want to continue in. Once the Fork Period ends, the Child Universe with the most REP migrated to it will be declared the Winning Universe. Calling `migrateThroughOneFork` attempts to move the Market from a Parent Universe to the Winning Universe after it's been decided. This method will throw if the Fork Period isn't over, so there is no Winning Universe to migrate to yet. This will return `1` if the `market` was successfully migrated and return `0` if no migration was required.

#### augur.api.Market.transferOwnership({ market, \_newOwner, onSent, onSuccess, onFailed })

The `transferOwnership` transaction will change the current [Market](#market) owner to the specified `_newOwner`. This is used to transfer ownership of a Market from one address to another. This transaction will fail if the `msg.sender` isn't the owner of the specified `market`. 

#### augur.api.Market.tryFinalize({ market, onSent, onSuccess, onFailed })

This transaction will attempt to finalize a [Market](#market) that is [Awaiting Finalization](#market-awaiting-finalization). If the Market isn't Awaiting Finalization then this will fail and return `0`. If the Market is Awaiting Finalization and can be finalized then this function will return 1 to indicate that the Market is now finalized and the [Tentative Outcome](#tentative-outcome) is now the [Final Outcome](#final-outcome). Further the Tentatively Winning [Payout Distribution Hash](#payout-distribution-hash) will become the Winning Payout Distribution Hash.

#### augur.api.Market.updateTentativeWinningPayoutDistributionHash({ market, \_payoutDistributionHash, onSent, onSuccess, onFailed })

This method is used to potentially update the Tentatively Winning [Payout Distribution Hash](#payout-distribution-hash). The Tentatively Winning Payout Distribution Hash is the Payout Distribution Hash with the most [REP](#rep) staked on it so far by [Reporters](#reporter). If the [Market](#market) is successfully [Finalized](#finalized-market) the Tentatively Winning Payout Distribution Hash becomes the Winning Payout Distribution Hash and determines how various [Shares](#share) of [Outcomes](#outcome) will payout during [Settlement](#settlement). This transaction will not change the Tentative Winning Payout Distribution Hash if the `_payoutDistributionHash` submitted to it doesn't have enough REP stakes to overtake the current Tentatively Winning Payout Distribution Hash. Returns `1` regardless of if the Tentatively Winning Distribution Hash was changed or not.

#### augur.api.Market.withdrawInEmergency({ market, onSent, onSuccess, onFailed })

If a critical bug or vulnerability is found in Augur, the development team can put it the system into a [halted](#developer-mode) state until the issue is resolved. In such instances, most regularly-used functions in Augur's backend will become unuseable until the system is returned to its normal state. When this happens, users can call the `withdrawInEmergency` function to withdraw their Reputation Tokens from a particular Market.

This transaction will fail if Augur is not currently in a halted state.

It returns true if the Reputation Tokens were successfully withdrawn from the Market. (NOTE: The return value cannot be obtained reliably when calling externally.)

Participation Token Tx API
--------------------------------
```javascript
// Participation Token Transaction API Examples:
var participationToken = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";
var _attotokens = 100;

augur.api.ParticipationToken.buy({
  participationToken: participationToken,
  _attotokens: _attotokens,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320486,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xf5681056bab571e0ec73411896a3e8f7a7b610e43f148ea96cd06f66a2e8472a",
  input: "0x0000000000000000000000000000000000000000000000000000000000000100",
  nonce: "0x3",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

var _forgoFees = true;
augur.api.ParticipationToken.redeem({
  participationToken: participationToken,
  _forgoFees: _forgoFees,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320487,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xf5681056bab571e0ec73411896a3e8f7a7b610e43f148ea96cd06f66a2e8472a",
  input: "0x00000001",
  nonce: "0x3",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.ParticipationToken.withdrawInEmergency({
  participationToken: participationToken,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320488,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xf5681056bab571e0ec73411896a3e8f7a7b610e43f148ea96cd06f66a2e8472a",
  input: "0x00000001",
  nonce: "0x3",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Participation Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/ParticipationToken.sol)

#### augur.api.ParticipationToken.buy({ participationToken, \_attotokens, onSent, onSuccess, onFailed })

Purchases the number of participation tokens specified by `_attotokens`. 

This transaction will fail if:

- `_attotokens` is <= 0.
- reporting is not currently active in the reporting window.
- all markets have not been finalized in the reporting window.

Returns: true if the Participation Tokens were successfully purchased. (NOTE: The return value cannot be obtained reliably when calling externally.)

#### augur.api.ParticipationToken.redeem({ participationToken, \_forgoFees, onSent, onSuccess, onFailed })

Converts the user's Participation Tokens to Reputation Tokens. Due to the fact that Markets can sometimes take months to Finalize (and, therefore, fees can take months to claim), the parameter `forgoFees` can be set to true if the user would like to redeem his or her Participation Tokens immediately without having to wait.

This transaction will fail if `msg.sender` does not have any Participation Tokens.

It returns true if the Participation Tokens were successfully withdrawn and converted to Reputation Tokens. (NOTE: The return value cannot be obtained reliably when calling externally.)

#### augur.api.ParticipationToken.withdrawInEmergency({ participationToken, onSent, onSuccess, onFailed })

If a critical bug or vulnerability is found in Augur, the development team can put it the system into a [halted](#developer-mode) state until the issue is resolved. In such instances, most regularly-used functions in Augur's backend will become unuseable until the system is returned to its normal state. When this happens, users can call the `withdrawInEmergency` function to withdraw their Participation Tokens and convert them into Reputation Tokens.

This transaction will fail if Augur is not currently in a halted state.

It returns true if the Participation Tokens were successfully withdrawn and converted to Reputation Tokens. (NOTE: The return value cannot be obtained reliably when calling externally.)

Reputation Token Tx API
--------------------------------
```javascript
// Reputation Token Contract Transaction API Examples:
var reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
var _spender = "0xea674fdde714fd979de3edf0f56aa9716b898ec8";
var _from = "0x1a05071893b764109f0bbc5b75d78e3e38b69ab3";
var _value = "100000000000000000000";

augur.api.ReputationToken.approve({
  reputationToken: reputationToken,
  _spender: _spender,
  _value: _value,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.ReputationToken.migrateFromLegacyReputationToken({
  reputationToken: reputationToken,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

var _destination = "0x73295d3c0ca46113ca226222c81c79adabf9f391";
augur.api.ReputationToken.migrateOut({
  reputationToken: reputationToken,
  _destination: _destination,
  _reporter: _source,
  _attotokens: _value,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.ReputationToken.transfer({
  reputationToken: reputationToken,
  _to: _spender,
  _value,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.ReputationToken.transferFrom({
  reputationToken: reputationToken,
  _from: _from,
  _to: _spender,
  _value: _value,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Reputation Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/ReputationToken.sol)

#### augur.api.ReputationToken.approve({ reputationToken, \_spender, \_value, onSent, onSuccess, onFailed })

Allows the `_spender` the ability to spend up to `_value` (denoted in attotokens) worth of the specified `reputationToken` for the `msg.sender` of this `approve` transaction. This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `_spender`, and `_value` in attotokens approved.

#### augur.api.ReputationToken.migrateFromLegacyReputationToken({ reputationToken, onSent, onSuccess, onFailed })

This function will migrate REP tokens from the legacy rep contract owned by `msg.sender` to the `reputationToken` provided. `msg.sender` will add whatever `msg.sender`'s balance was for the legacy rep contract to the `reputationToken` contract.

#### augur.api.ReputationToken.migrateOut({ reputationToken, \_destination, \_reporter, \_attotokens, onSent, onSuccess, onFailed })

This function migrates a `_reporter`'s REP (amount of REP denoted in `_attotokens`) from one `reputationToken` address to another (`_destination`). The `msg.sender` of this transaction must be the `_reporter` provided or the `msg.sender` must be approved to spend `_attotokens` amount of REP for the `_reporter` provided.

#### augur.api.ReputationToken.transfer({ reputationToken, \_to, \_value, onSent, onSuccess, onFailed })

If the `msg.sender` of the `transfer` transaction has enough of `reputationToken` to be able to transfer `_value` (denoted in attotokens) worth to the `_to` address and `_value` is a number between 1 and 2<sup>254</sup> then this transaction will send `_value` (in attotokens) worth of `reputationToken` to the specified `_to` address from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address (`msg.sender`), `_to` address, and `_value` amount transferred.

#### augur.api.ReputationToken.transferFrom({ reputationToken, \_from, \_to, \_value, onSent, onSuccess, onFailed })

If the `_from` address of the `transferFrom` transaction has enough of `reputationToken` to be able to transfer `_value` (denoted in attotokens) worth to the `_to` address, `_value` is a number between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `_value` worth of `reputationTokens` for the `_from` address then this transaction will send `_value` worth of `reputationToken` to the specified `_to` address from the `_from` address. This transaction will spawn a `Transfer` event which will record the `_from` address, `_to` address, and `_value` (in attotokens) amount transferred.

Share Token Tx API
---------------------------
```javascript
// Share Token Contract Transaction API Examples:
var shareToken = "0xa22c79a48f51df6d0863821bd1dd2c5d6f511bc5";
var _spender = "0x01f50356c280cd886dd058210937160c73700a4b";
var _attotokens = "100000000000000000000";

augur.api.ShareToken.approve({
  shareToken: shareToken,
  _spender: _spender,
  _value: _attotokens,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
augur.api.ShareToken.transfer({
  shareToken: shareToken,
  _to: _spender,
  _value: _attotokens,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

var _from = "0x4b01721f0244e7c5b5f63c20942850e447f5a5ee";
augur.api.ShareToken.transferFrom({
  shareToken: shareToken,
  _from: _from,
  _to: _spender,
  _value: _attotokens,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Share Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/ShareToken.sol)

#### augur.api.ShareToken.approve({ shareToken, \_spender, \_value, onSent, onSuccess, onFailed })

Allows the `_spender` the ability to spend up to `_value` worth of the specified `shareToken` for the `msg.sender` of this `approve` transaction. This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `_spender`, and `_value` denoted in attotokens approved.

#### augur.api.ShareToken.transfer({ shareToken, \_to, \_value, onSent, onSuccess, onFailed })

If the `msg.sender` of the `transfer` transaction has enough of `shareToken`s to be able to transfer `_value` worth to the `_to` address and `_value` is a valid number between 1 and 2<sup>254</sup> then this transaction will send `_value` worth of `shareToken` to the specified `_to` address from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address (`msg.sender`), `_to` address, and `_value` amount transferred denoted in attotokens.

#### augur.api.ShareToken.transferFrom({ shareToken, \_from, \_to, \_value, onSent, onSuccess, onFailed })

If the `_from` address of the `transferFrom` transaction has enough of `shareToken` to be able to transfer `_value` worth to the `_to` address, `_value` is a valid number between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `_value` worth of `shareToken` for `_from` address then this transaction will send `_value` worth of `shareToken` to the specified `_to` address from the `_from` address. This transaction will spawn a `Transfer` event which will record the `_from` address, `_to` address, and `_value` amount transferred denoted in attotokens.

Stake Token Tx API
-------------------------------
```javascript
// Stake Token Contract Transaction API Examples:
var stakeToken = "0xbb87186146569514b8cd8b72e57eec3849e3981f";
var _spender = "0xfe9d0408be14d1d1ec28671b03bda1b80748977e";
var _attotokens = "100000000000000000000";

augur.api.StakeToken.approve({
  stakeToken: stakeToken,
  _spender: _spender,
  _value: _attotokens,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.StakeToken.buy({
  stakeToken: stakeToken,
  _attotokens: _attotokens,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.StakeToken.migrateLosingTokens({
  stakeToken: stakeToken,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.StakeToken.redeemDisavowedTokens({
  stakeToken: stakeToken,
  _reporter: _spender,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.StakeToken.redeemForkedTokens({
  stakeToken: stakeToken,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

var _forgoFees = true;
augur.api.StakeToken.redeemWinningTokens({
  stakeToken: stakeToken,
  _forgoFees: _forgoFees,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.StakeToken.transfer({
  stakeToken: stakeToken,
  _to: _spender,
  _value: _attotokens,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

var _from = "0x34c85afe56c392e240c64dc09d2a7962afe2920a";
augur.api.StakeToken.transferFrom({
  stakeToken: stakeToken,
  _from: _from,
  _to: _spender,
  _value: _attotokens,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.StakeToken.withdrawInEmergency {
  stakeToken: stakeToken,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
}
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320502,
  callReturn: "1",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0xf5681056bab571e0ec73411896a3e8f7a7b610e43f148ea96cd06f66a2e8472a",
  input: "0x00000001",
  nonce: "0x3",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Stake Token Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/StakeToken.sol)

#### augur.api.StakeToken.approve({ stakeToken, \_spender, \_value, onSent, onSuccess, onFailed })

Allows the `_spender` the ability to spend up to `_value` worth of the specified `stakeToken` for the `msg.sender`. This transaction is used to allow another person to perform actions with your Stake Tokens on your behalf. This transaction will spawn an `Approval` event which will record a log of the owner's address (`msg.sender`), `_spender` address, and `_value` amount approved.

#### augur.api.StakeToken.buy({ stakeToken, \_attotokens, onSent, onSuccess, onFailed })

This transaction is used to purchase Stake Tokens using [REP](#rep). The Stake Token's [Market](#market) must be in a [Dispute Round](#dispute-round) and Stake Tokens can only be purchased during the [Reporting Phase](#reporting-phase) of a [Reporting Window](#reporting-window). Stake Tokens have a 1:1 ratio to REP, which means 100 attoREP is equal to 100 attoStakeTokens, so the `msg.sender` also needs to have at least `_attotokens` worth of REP to complete this transaction. This transaction will spawn a `Transfer` event which will record a log of the from address (`msg.sender`), to address (`stakeToken`), and the amount of `_attotokens` purchased.

#### augur.api.StakeToken.migrateLosingTokens({ stakeToken, onSent, onSuccess, onFailed })

This transaction should be called, though it's not required, once on each Stake Token that isn't the Winning Stake Token once a [Market](#market) has [Finalized](#finalized-market). This method will migrate the [REP](#rep) used to purchase the non-winning Stake Token to the Winning Stake Token so the REP can be redistributed to the Reporters who correctly staked REP on the Winning [Payout Set](#payout-set). If this isn't called, then whatever REP is staked into this Stake Token will remain staked into the Stake Token indefinitely. This function will fail to migrate REP to the winning Stake Token if the Market isn't finalized, the Market caused a Fork, or if This Stake Token represents the Winning Payout Set and is therefor not a losing token.

#### augur.api.StakeToken.redeemDisavowedTokens({ stakeToken, \_reporter, onSent, onSuccess, onFailed })

In the event of a [Fork](#fork), all [Markets](#market), except for the [Forked Market](#forked-market), that haven't been [Finalized](#finalized-market) are placed into a pending state until the [Fork Period](#fork-period) concludes and the Fork is Resolved. During this time, any Stake Tokens purchased for these pending Markets are considered to be "Disavowed" and are able to be refunded for [REP](#rep) back into the [Parent Universe](#parent-universe) using this transaction. This is an important tool because REP holders are expected to Migrate their REP to a [Child Universe](#child-universe) and they need to be able to reclaim their REP to do so. Once the Fork is resolved, all pending markets that had a [Designated Report](#designated-report) or [Initial Report](#initial-report) submitted immediately start [Dispute Round Phase](#dispute-round-phase). This will fail if the Stake Token hasn't been "Disavowed", or if this is used on a Stake Token belonging to the Forked Market. This transaction will spawn a `Transfer` event which will record the from address (`stakeToken`), to address (`_reporter`), and the amount of REP transferred.

#### augur.api.StakeToken.redeemForkedTokens({ stakeToken, onSent, onSuccess, onFailed })

This transaction is used to migrate [REP](#rep) that is in a [Forked Market](#forked-market). Unlike `redeemDisavowedTokens`, which only works on Disavowed [Markets](#markets) and withdraws the REP to the [Parent Universe](#parent-universe), this transaction only works on the Forked Market and the REP is withdrawn to the [Child Universe](#child-universe) corresponding to the Stake Token's [Payout Set](#payout-set). This transaction will spawn a `Transfer` event which will record a log of the from address (the Child Universe's REP contract), to address (`msg.sender`), and the amount of REP transferred.

#### augur.api.StakeToken.redeemWinningTokens({ stakeToken, _forgoFees, onSent, onSuccess, onFailed })

This transaction is used to redeem [REP](#rep) from a winning Stake Token on a [Finalized Market](#finalized-market). Calling this method will withdraw REP based on the amount of REP staked into the Stake Token by the `msg.sender` as well as a portion of the REP redistributed from losing Stake Tokens. It's recommended that [Reporters](#reporters) make sure `migrateLosingTokens` was called one time on each of the losing Stake Tokens for this [Market](#market) if they wish to claim the a portion of the REP staked in those losing Tokens. This transaction will only succeed if the Market is Finalized, the `stakeToken` is the winning Stake Token, and this isn't a [Forked Market](#forked-market). Due to the fact that Markets can sometimes take months to Finalize (and, therefore, the fees can take months to claim), the parameter `forgoFees` can be set to true if the user would like to redeem his or her Stake Tokens immediately without having to wait. This transaction will spawn a `Transfer` event which will record a log of the from address (`stakeToken`), to address (`msg.sender`), and the amount of `REP` transferred.

#### augur.api.StakeToken.transfer({ stakeToken, \_to, \_value, onSent, onSuccess, onFailed })

The transfer transaction is used to transfer a Stake Token's ownership from the `msg.sender` to the `_to` address provided. This transaction will fail if the `msg.sender` doesn't own any Stake Tokens at the `stakeToken` address provided, or if the `_value` provided is greater than the number of Stake Tokens owned by the `msg.sender`. This transaction will spawn a `Transfer` event which will record a log of the `msg.sender` address, `_to` address, and `_value` amount transferred.

#### augur.api.StakeToken.transferFrom({ stakeToken, \_from, \_to, \_value, onSent, onSuccess, onFailed })

Unlike transfer, in transferFrom you specify a `_from` value as the owner of the Stake Token who will be giving up their ownership to the `_to` address provided. This allows Stake Tokens to be completely transferrable and therefor the Staked [REP](#rep) contained is also still transferable even while staked on a [Report](#report). This transaction will fail if the `msg.sender` doesn't have approval of the `_from`, if the `_from` address doesn't own any Stake Tokens, or if the `_value` provided is more than the Stake Tokens owned by the `_from` address. This transaction will also spawn a `Transfer` event which will record a log of the `_from` address, `_to` address, and `_value` amount transferred.

#### augur.api.StakeToken.withdrawInEmergency({ stakeToken, onSent, onSuccess, onFailed })

If a critical bug or vulnerability is found in Augur, the development team can put it the system into a [halted](#developer-mode) state until the issue is resolved. In such instances, most regularly-used functions in Augur's backend will become unuseable until the system is returned to its normal state. When this happens, users can call the `withdrawInEmergency` function to withdraw their Stake Tokens and convert them into Reputation Tokens.

This transaction will fail if Augur is not currently in a halted state.

It returns true if the Stake Tokens were successfully withdrawn and converted to Reputation Tokens. (NOTE: The return value cannot be obtained reliably when calling externally.)

Trade Tx API
---------------------
```javascript
// Trade Contract Transaction API Examples:
var _market = "0x7e8e07364ccde43ba5159537404924e86ca53c92";
var _outcome = "1";
var _fxpAmount = "10000000000000000000"; // 10.0
var _fxpPrice = "500000000000000000"; // 0.5
var _tradeGroupId = "0x0000000000000000000000000000000000000000000000000000000000000003";

augur.api.Trade.publicBuy({
  _market: _market,
  _outcome: _outcome,
  _fxpAmount: _fxpAmount,
  _fxpPrice: _fxpPrice,
  _tradeGroupId: _tradeGroupId,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Trade.publicSell({
  _market: _market,
  _outcome: _outcome,
  _fxpAmount: _fxpAmount,
  _fxpPrice: _fxpPrice,
  _tradeGroupId: _tradeGroupId,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Trade.publicTrade({
  _direction: '1',
  _market: _market,
  _outcome: _outcome,
  _fxpAmount: _fxpAmount,
  _fxpPrice: _fxpPrice,
  _tradeGroupId: _tradeGroupId,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Trade.publicTakeBestOrder({
  _direction: '2',
  _market: _market,
  _outcome: _outcome,
  _fxpAmount: _fxpAmount,
  _fxpPrice: _fxpPrice,
  _tradeGroupId: _tradeGroupId,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
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
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### [Trade Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/Trade.sol)

#### augur.api.Trade.publicBuy({ \_market, \_outcome, \_fxpAmount, \_fxpPrice, \_tradeGroupId, onSent, onSuccess, onFailed })

The `publicBuy` transaction is used to purchase shares for a specified `_market` and `_outcome`. The amount of shares you wish to purchase is `_fxpAmount` denoted in attoshares and the price point you would like to buy at is `_fxpPrice` denoted in attotokens of the `_market`'s denomination token. This transaction will take orders off the order book that can be filled with this request, otherwise this transaction will create a new order to buy `_fxpAmount` of shares at `_fxpPrice`. The `_tradeGroupId` is an optional argument used by the Augur UI and can be left `undefined`. This transaction returns `1` if this order was filled completely, or if this order can't be filled immediately an order will be created and the `_orderId` of that new order will be returned.

#### augur.api.Trade.publicSell({ \_market, \_outcome, \_fxpAmount, \_fxpPrice, \_tradeGroupId, onSent, onSuccess, onFailed })

The `publicSell` transaction is used to purchase shares for a specified `_market` and `_outcome`. The amount of shares you wish to purchase is `_fxpAmount` denoted in attoshares and the price point you would like to sell at is `_fxpPrice` denoted in attotokens of the `_market`'s denomination token. This transaction will take orders off the order book that can be filled with this request, otherwise this transaction will create a new order to sell `_fxpAmount` of shares at `_fxpPrice`. The `_tradeGroupId` is an optional argument used by the Augur UI and can be left `undefined`. This transaction returns `1` if this order was filled completely, or if this order can't be filled immediately an order will be created and the `_orderId` of that new order will be returned.

#### augur.api.Trade.publicTrade({ \_direction, \_market, \_outcome, \_fxpAmount, \_fxpPrice, \_tradeGroupId, onSent, onSuccess, onFailed })

The `publicTrade` transaction is works exactly like `publicBuy` or `publicSell` however a direction must be specified this time. The `_direction` must be either `1` for buying or `2` for selling. This transaction returns `1` if this order was filled completely, or if this order can't be filled immediately an order will be created and the `_orderId` of that new order will be returned.

#### augur.api.Trade.publicTakeBestOrder({ \_direction, \_market, \_outcome, \_fxpAmount, \_fxpPrice, \_tradeGroupId, onSent, onSuccess, onFailed })

The `publicTakeBestOrder` transaction will work very similarly to `publicTrade` except it will not create an order if the request can't be filled. The `_direction` must be either `1` for buying or `2` for selling. This transaction returns the fixed point amount not filled by the order, so `0` for a completely filled order, some other number if this request could only be partially filled.

TradingEscapeHatch Tx API
---------------------
```javascript
// TradingEscapeHatch Contract Transaction API Examples:
var _market = "0x465407364ccde43ba5159537404924e86ca53aaa";

augur.api.TradingEscapeHatch.claimSharesInUpdate({
  _market: _market,
  onSent: function (result) { console.log(result) },
  onSuccess: function (result) { console.log(result) },
  onFailed: function (result) { console.log(result) }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320540,
  callReturn: "0",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x7ab9bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1aefd1",
  input: "0x3659bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1ae7ac",
  nonce: "0xff8",
  r: "0x3db124c7a0ae32094e0afdcd020d51fb930cdc3877066433b910e9257c3232eb"
  s: "0x543251c0fb374e535c61c88b913816f791edba101517921e473384706ddd0256"
  timestamp: 1501003200,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
#### augur.api.TradingEscapeHatch.claimSharesInUpdate({ \_market, onSent, onSuccess, onFailed })

If Augur needs to be [halted](#developer-mode) by the development team (for example, if a vulnerability is discovered), calling this function on a specific [Market](#market) will withdraw the user's funds from that Market and return them to the user's address. This transaction will fail if Augur is not in a halted state.

Universe Tx API
---------------------
```javascript
// Universe Contract Transaction API Examples:
var _extraInfo = { 
  "resolutionSource": "http://www.spacex.com", 
  "tags": [ "SpaceX", "spaceflight" ], 
  "longDescription": "SpaceX hit a big milestone on Friday with NASA confirming on Friday that the Elon Musk-led space cargo business will launch astronauts to the International Space Station by 2017." 
};
augur.api.Universe.createBinaryMarket({
  _endTime: 1546329600,
  _feePerEthInWei: 1193046,
  _denominationToken: "0x74e88699f5d33f516500c3d9a2430f5e6ffb0689",
  _designatedReporterAddress: "0x01114f4bda09ed6c6715cf0baf606b5bce1dc96a",
  _topic: "space",
  _description: "Will SpaceX successfully complete a manned flight to the International Space Station by the end of 2018?",
  _extraInfo: JSON.stringify(_extraInfo),
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { 
    console.log("onSuccess result:", result);
    // Call `augur.createMarket.getMarketFromCreateMarketReceipt` to retrieve new market's address
  },
  onFailed: function (result) { console.log("onFailed result:", result); },
  tx: { 
    to: "0x6eabb9367012c0a84473e1e6d7a7ce39a54d77bb",
    value: 10000000006000000, 
    gas: augur.constants.CREATE_BINARY_MARKET_GAS
  }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x8125127e415c1a242046f8ebbb036ebdefaa013ec697d8691da43727cd311155",
  blockNumber:1362465,
  callReturn: null,
  from: "0x40485264986740c8fb3d11e814bd94cf86012d29",
  gas: "0x5b8d80",
  gasFees: "0.02179245429087362",
  gasPrice: "0x1ce4eb7ee",
  hash: "0xc0583954449b26e2036212929620674bb1e7bafb904c4fae551297dd036ee769",
  input: "0x593...000",
  nonce: "0x1e",
  r: "0x9b013bbb8fda1635b419ff001fd57f651f648a1b4041c08d6506a13ff9d9ca21"
  s: "0x5b6358e1c1d71d233a659478b175dd3c51577889e17965546897275cb723ed39"
  timestamp: 1512491243,
  to: "0x6eabb9367012c0a84473e1e6d7a7ce39a54d77bb",
  transactionIndex: "0x1",
  v: "0x2c",
  value: "0x2386f2701c8d80"
}

var _extraInfo = { 
  "resolutionSource": "http://www.espn.com", 
  "tags": ["college football", "football"], 
  "outcomeNames": ["Georgia", "Florida"], 
  "longDescription": "" 
};
augur.api.Universe.createCategoricalMarket({
  _endTime: 1540875600,
  _feePerEthInWei: 1193046,
  _denominationToken: "0x74e88699f5d33f516500c3d9a2430f5e6ffb0689",
  _designatedReporterAddress: "0x01114f4bda09ed6c6715cf0baf606b5bce1dc96a",
  _outcomes: ["outcome1","outcome2"],
  _topic: "sports",
  _description: "Who will win the University of Georgia vs. University of Florida football game in 2018?",
  _extraInfo: JSON.stringify(_extraInfo),
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { 
    console.log("onSuccess result:", result);
    // Call `augur.createMarket.getMarketFromCreateMarketReceipt` to retrieve new market's address
  },
  onFailed: function (result) { console.log("onFailed result:", result); },
  tx: { 
    to: "0x6eabb9367012c0a84473e1e6d7a7ce39a54d77bb",
    value: 10000000006000000,
    gas: augur.constants.CREATE_CATEGORICAL_MARKET_GAS
  }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x8125127e415c1a242046f8ebbb036ebdefaa013ec697d8691da43727cd311155",
  blockNumber:1362465,
  callReturn: null,
  from: "0x40485264986740c8fb3d11e814bd94cf86012d29",
  gas: "0x632ea0",
  gasFees: "0.021696129637146186",
  gasPrice: "0x1ce4eb7ee",
  hash: "0xb97f69a404805e81396a8fcc383b95edcb4a6fe6d17e0a33c994f05734a1cac6",
  input: "0x593...000",
  nonce: "0x1e",
  r: "0x9b013bbb8fda1635b419ff001fd57f651f648a1b4041c08d6506a13ff9d9ca21"
  s: "0x5b6358e1c1d71d233a659478b175dd3c51577889e17965546897275cb723ed39"
  timestamp: 1512491243,
  to: "0x6eabb9367012c0a84473e1e6d7a7ce39a54d77bb",
  v: "0x2c",
  value: "0x2386f2701c8d80"
}

var _extraInfo = { 
  "resolutionSource": "http://forecast.weather.gov", 
  "tags": ["San Francisco", "weather"], 
  "longDescription": "" 
};
augur.api.Universe.createScalarMarket({
  _endTime: 1530507600,
  _feePerEthInWei: 1193046,
  _denominationToken: "0x74e88699f5d33f516500c3d9a2430f5e6ffb0689",
  _designatedReporterAddress: "0x01114f4bda09ed6c6715cf0baf606b5bce1dc96a",
  _minPrice: -10,
  _maxPrice: 120,
  _numTicks: 10,
  _topic: "temperature",
  _description: "High temperature (in degrees Fahrenheit) in San Francisco, California, on July 1, 2018",
  _extraInfo: JSON.stringify(_extraInfo),
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { 
    console.log("onSuccess result:", result);
    // Call `augur.createMarket.getMarketFromCreateMarketReceipt` to retrieve new market's address
  },
  onFailed: function (result) { console.log("onFailed result:", result); },
  tx: { 
    to: "0x6eabb9367012c0a84473e1e6d7a7ce39a54d77bb",
    value: 10000000006000000,
    gas: augur.constants.CREATE_SCALAR_MARKET_GAS
  }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x8125127e415c1a242046f8ebbb036ebdefaa013ec697d8691da43727cd311155",
  blockNumber:1362465,
  callReturn: null,
  from: "0x40485264986740c8fb3d11e814bd94cf86012d29",
  gas: "0x5b8d80",
  gasFees: "0.021695431576204446",
  gasPrice: "0x1ce4eb7ee",
  hash: "0xb97f69a404805e81396a8fcc383b95edcb4a6fe6d17e0a33c994f05734a1cac6",
  input: "0x593...000",
  nonce: "0x1e",
  r: "0x9b013bbb8fda1635b419ff001fd57f651f648a1b4041c08d6506a13ff9d9ca21"
  s: "0x5b6358e1c1d71d233a659478b175dd3c51577889e17965546897275cb723ed39"
  timestamp: 1512491243,
  to: "0x6eabb9367012c0a84473e1e6d7a7ce39a54d77bb",
  v: "0x2c",
  value: "0x2386f2701c8d80"
}

augur.api.Universe.getOrCacheDesignatedReportNoShowBond({
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320554,
  callReturn: "0",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0x2fd618",
  gasFees: "0.00102297557556486",
  gasPrice: "0x5b793ccec",
  hash: "0x7ab9bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1aefd1",
  input: "0x3659bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1ae7ac",
  nonce: "0xff8",
  r: "0x3db124c7a0ae32094e0afdcd020d51fb930cdc3877066433b910e9257c3232eb"
  s: "0x543251c0fb374e535c61c88b913816f791edba101517921e473384706ddd0256"
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Universe.getOrCacheDesignatedReportStake({
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320555,
  callReturn: "0",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x7ab9bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1aefd1",
  input: "0x3659bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1ae7ac",
  nonce: "0xff8",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Universe.getOrCacheMarketCreationCost({
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320556,
  callReturn: "0",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x7ab9bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1aefd1",
  input: "0x3659bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1ae7ac",
  nonce: "0xff8",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Universe.getOrCacheReportingFeeDivisor({
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320557,
  callReturn: "0",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x7ab9bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1aefd1",
  input: "0x3659bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1ae7ac",
  nonce: "0xff8",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

augur.api.Universe.getOrCacheTargetReporterGasCosts({
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0xdc5e31404be698a6866fbe7d7cf435a2c6fab7deb3acf41d6c9bb981a2630fed",
  blockNumber: 1330897,
  callReturn: "6000000",
  from: "0x40485264986740c8fb3d11e814bd94cf86012d29",
  gas: "0x2fd618",
  gasFees: "0.00081266",
  gasPrice: "0x4a817c800",
  hash: "0x1af308e9ea415f321f099b36bae21753bd5227812451694e75b3604aea87bf92",
  input: "0x39d26051",
  nonce: "0x0",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1512018567,
  to: "0xfc6bba7d664e8cc242857f069e9fb1e7c25ecb44",
  transactionIndex: "0x1",
  v: "0x2c",
  value: "0x0"
}

augur.api.Universe.getOrCacheValidityBond({
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320558,
  callReturn: "0",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x7ab9bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1aefd1",
  input: "0x3659bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1ae7ac",
  nonce: "0xff8",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}

var _parentPayoutDistributionHash = "0x7ab9bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1aefd1";
augur.api.Universe.getOrCreateChildUniverse({
  _parentPayoutDistributionHash: _parentPayoutDistributionHash,
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320559,
  callReturn: "0",
  from: "0xa47eb7af47b8722c3100b49c256a94c742bb26b6",
  gas: "0xb10d2",
  gasFees: "0.005827878",
  gasPrice: "0x430e23400",
  hash: "0x7ab9bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1aefd1",
  input: "0x3659bde926dfb9d1bbee93c07cbcf7d11ea4b995fa8f72d88d8322336d1ae7ac",
  nonce: "0xff8",
  r: "0x706eb8aee04faf21fd6da949f946df5fa118da35a619560af9b81fc05054715c",
  s: "0x60d3125aa3b1daabd37618fb80232037cdaef9c87a8d262826880f0e57fdb0d3",
  timestamp: 1501003133,
  to: "0xa7f3659c53820346176f7e0e350780df304db179",
  v: "0x2c",
  value: "0x0"
}
```
<!-- TODO: Add sections to create*Market functions explaining what conditions will cause transaction to fail; add calls to augur.createMarket.getMarketFromCreateMarketReceipt in market creation examples -->
#### augur.api.Universe.createBinaryMarket({ \_endTime, \_feePerEthInWei, \_denominationToken, \_designatedReporterAddress, \_topic, \_description, \_extraInfo, onSent, onSuccess, onFailed, tx })

Creates a new [Binary Market](#binary-market). After the transaction has completed successfully, the Market address can be obtained by calling `augur.createMarket.getMarketFromCreateMarketReceipt`.

#### Parameters:

* `_endTime` - Unix timestamp for the [End Time](#end-time) of the [Market](#market). 
* `_feePerEthInWei` - [Creator Fee](#creator-fee) (in Wei) that is collected for every 1 ETH worth of [Shares](#share) [Settled](#settlement). 
* `denominationToken` - Address of the token the Market is denominated in. Currently, Markets are only denominated in ETH (i.e., the Cash contract in Augur's smart contracts), but Augur is expected to support other tokens in the future. 
* `_designatedReporterAddress` - String containing the address of the [Designated Reporter](#designated-reporter). 
* `_topic` - Market [Topic](#topic). 
* `_description` - Description of the Market event. 
* `_extraInfo` - Stringified JSON object containing the following keys:
  * `resolutionSource` - Source that should be referenced when determining the [Outcome](#outcome) of a Market.
  * `tags` - Keywords used to tag the Market (maximum of 2).
  * `longDescription` - Additional information not included in `description`.
* `tx` - JSON object containing the following keys:
  * `to` - Address of the Universe contract.
  * `value` - Number of Wei required to create the Market. This can be obtained by calling `augur.createMarket.getMarketCreationCost` and multiplying the `etherRequiredToCreateMarket` value that's returned by 10<sup>18</sup>.
  * `gas` - Amount of gas required to execute the transaction. This can be obtained from the constant `augur.constants.CREATE_BINARY_MARKET_GAS`.

<!-- #### Notes: Transaction will fail if: the sender does not have enough ETH/REP to pay for the [Validity Bond](#validity-bond), [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond), & [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond), `_endTime` has already passed, `_feesPerEthInWei` is less than 0 or greater than/equal to 0.5 ETH (5 * 10^18), `_designatedReporterAddress` is the null address (0x0000000000000000000000000000000000000000), the length of `_description` is not greater than 0 bytes, `value` in the `tx` object is not enough to cover the Market's Validity Bond and the estimated gas cost for the target amount of reporters to report. -->

#### augur.api.Universe.createCategoricalMarket({ \_endTime, \_feePerEthInWei, \_denominationToken, \_designatedReporterAddress, \_numOutcomes, \_topic, \_description, \_extraInfo, onSent, onSuccess, onFailed, tx })

Creates a new [Categorical Market](#categorical-market). After the transaction has completed successfully, the Market address can be obtained by calling `augur.createMarket.getMarketFromCreateMarketReceipt`.

#### Parameters:

* `_endTime` - Unix timestamp for the [End Time](#end-time) of the [Market](#market). 
* `_feePerEthInWei` - [Creator Fee](#creator-fee) (in Wei) that is collected for every 1 ETH worth of [Shares](#share) [Settled](#settlement). 
* `denominationToken` is the token the Market is denominated in. Currently, Markets are only denominated in ETH (i.e., the Cash contract in Augur's smart contracts), but Augur is expected to support other tokens in the future. 
* `_designatedReporterAddress` - Address of the [Designated Reporter](#designated-reporter). 
* `_numOutcomes` - Total possible outcomes for the Market event. 
* `_topic` - Market [Topic](#topic). 
* `_description` - Description of the Market event. 
* `_extraInfo` - Stringified JSON object. (See the explanation above for more details.)
  * `resolutionSource` - Source that should be referenced when determining the [Outcome](#outcome) of a Market.
  * `tags` - Keywords used to tag the Market (maximum of 2).
  * `longDescription` - Additional information not included in `description`.
  * `outcomeNames` - Names for possible Outcomes of the Market. (Only used when creating a [Categorical Market](#categorical-market).)
* `tx` - JSON object containing the following keys:
  * `to` - Address of the Universe contract.
  * `value` - Number of Wei required to create the Market. This can be obtained by calling `augur.createMarket.getMarketCreationCost` and multiplying the `etherRequiredToCreateMarket` value that's returned by 10<sup>18</sup>.
  * `gas` - Amount of gas required to execute the transaction. This can be obtained from the constant `augur.constants.CREATE_CATEGORICAL_MARKET_GAS`.

#### augur.api.Universe.createScalarMarket({ \_endTime, \_feePerEthInWei, \_denominationToken, \_designatedReporterAddress, \_minPrice, \_maxPrice, \_numTicks, \_topic, \_description, \_extraInfo, onSent, onSuccess, onFailed, tx })

Creates a new [Scalar Market](#scalar-market). After the transaction has completed successfully, the Market address can be obtained by calling `augur.createMarket.getMarketFromCreateMarketReceipt`.

* `_endTime` - Unix timestamp for the [End Time](#end-time) of the [Market](#market). 
* `_feePerEthInWei` - [Creator Fee](#creator-fee) (in Wei) that is collected for every 1 ETH worth of [Shares](#share) [Settled](#settlement). 
* `denominationToken` is the token the Market is denominated in. Currently, Markets are only denominated in ETH (i.e., the Cash contract in Augur's smart contracts), but Augur is expected to support other tokens in the future. 
* `_designatedReporterAddress` - Address of the [Designated Reporter](#designated-reporter). 
* `_minPrice` - [Minimum Display Price](#minimum-display-price) for the Market.
* `_maxPrice` - [Maximum Display Price](#maximum-display-price) for the Market.
* `_numTicks` - [Number of Ticks](#number-of-ticks) for the Market.
* `_topic` - Market [Topic](#topic). 
* `_description` - Description of the Market event. 
* `_extraInfo` - Stringified JSON object. (See the explanation above for more details.)
  * `resolutionSource` - Source that should be referenced when determining the [Outcome](#outcome) of a Market.
  * `tags` - Keywords used to tag the Market (maximum of 2).
  * `longDescription` - Additional information not included in `description`.
* `tx` - JSON object containing the following keys:
  * `to` - Address of the Universe contract.
  * `value` - Number of Wei required to create the Market. This can be obtained by calling `augur.createMarket.getMarketCreationCost` and multiplying the `etherRequiredToCreateMarket` value that's returned by 10<sup>18</sup>.
  * `gas` - Amount of gas required to execute the transaction. This can be obtained from the constant `augur.constants.CREATE_SCALAR_MARKET_GAS`.

#### augur.api.Universe.getOrCacheDesignatedReportNoShowBond({ onSent, onSuccess, onFailed })

Returns the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) for [Markets](#market) in the [Universe](#universe), priced in [AttoREP](#atto-prefix). If the value of the Designated Report No-Show REP Bond for the current [Reporting Window](#reporting-window) has not already been cached in the Universe contract, this function will cache it.

#### augur.api.Universe.getOrCacheDesignatedReportStake({ onSent, onSuccess, onFailed })

Returns the amount of stake in [AttoREP](#atto-prefix) that the [Designated Reporter](#designated-reporter) must put up when submitting a [Designated Report](#designated-report) in the [Universe](#universe). If this value for the current [Reporting Window](#reporting-window) has not already been cached in the Universe contract, this function will cache it.

#### augur.api.Universe.getOrCacheMarketCreationCost({ onSent, onSuccess, onFailed })

Returns the estimated amount of [AttoETH](#atto-prefix) required to create a [Market](#market) in the [Universe](#universe). The amount returned by this function is equivalent to the sum of `augur.api.Universe.getOrCacheValidityBond()` and `augur.api.Universe.getOrCacheTargetReporterGasCosts()`. If the values of the [Validity Bond](#validity-bond) and the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) for the current [Reporting Window](#reporting-window) have not already been cached in the Universe contract, this function will cache them.

#### augur.api.Universe.getOrCacheReportingFeeDivisor({ onSent, onSuccess, onFailed })

Returns the number by which the total payout amount for a [Market](#market) is divided in order to calculate the [Reporting Fee](#reporting-fee). If this value for the current [Reporting Window](#reporting-window) has not already been cached in the Universe contract, this function will cache it.

#### augur.api.Universe.getOrCacheTargetReporterGasCosts({ onSent, onSuccess, onFailed })

Returns the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) in [AttoETH](#atto-prefix) that is paid to the [First Reporter](#first-reporter) in the event of a [Designated Report](#designated-report) no-show, or refunded to the [Market Creator Mailbox](#market-creator-mailbox) if the [Designated Reporter](#designated-reporter) does report. The amount returned by this function will typically be well above the actual cost to create a Market, just to ensure the Market creation will succeed. If the Designated Report No-Show Gas Bond for the current [Reporting Window](#reporting-window) has not already been cached in the Universe contract, this function will cache it.

#### augur.api.Universe.getOrCacheValidityBond({ onSent, onSuccess, onFailed })

Returns the amount the [Market Creator](#market-creator) must pay for the [Validity Bond](#validity-bond), denominated in [AttoETH](#atto-prefix), when creating a [Market](#market). If the Validity Bond for the current [Reporting Window](#reporting-window) has not already been cached in the Universe contract, this function will cache it. (This amount will be refunded to the Market Creator if the [Final Outcome](#final-outcome) of the Market is not invalid.)

#### augur.api.Universe.getOrCreateChildUniverse({ \_parentPayoutDistributionHash, onSent, onSuccess, onFailed })

Returns a [Child Universe](#child-universe) corresponding to the specified `_parentPayoutDistributionHash`. If the Child Universe does not already exist, it will be created. This transaction will fail if the Universe does not have a [Forked Market](#forked-market).
