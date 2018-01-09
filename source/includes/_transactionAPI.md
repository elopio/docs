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
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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

Augur Tx API
----------------------------
```javascript
// Augur Transaction API Examples:
augur.api.Augur.createGenesisUniverse({
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
#### [Augur Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/Augur.sol)

#### augur.api.Augur.createGenesisUniverse({ onSent, onSuccess, onFailed })

This function allows users to create a new [Genesis Universe](#genesis-universe) if, for example, they wish to create a [Universe](#universe) to compete with another existing Universe. Whenever a new Genesis Universe is created, it does not initially contain any [Markets](#market) or [REP](#rep) supply. In order to add a supply of REP, users must migrate [Legacy REP](#legacy-rep) from the [Legacy REP smart contract](https://github.com/AugurProject/augur-core/blob/master/source/contracts/LegacyReputationToken.sol) into the [Reputation Token smart contract](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/ReputationToken.sol) for the new Genesis Universe using the function `augur.api.ReputationToken.migrateFromLegacyReputationToken`. (NOTE: The return value cannot be obtained reliably when calling externally.)

Cancel Order Tx API
----------------------------
```javascript
// Cancel Order Transaction API Examples:
var _orderId = "0x7ca90ca9118db456d87e3d743b97782a857200b55039f7ffe8de94e5d920f870";

augur.api.CancelOrder.cancelOrder({
  _orderId: _orderId,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
#### [Cancel Order Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/CancelOrder.sol)

#### augur.api.CancelOrder.cancelOrder({ \_orderId, onSent, onSuccess, onFailed })

The `cancelOrder` transaction is used to cancel and refund an existing [Order](#order) on the [Order Book](#order-book) with the `_orderId` specified as a 32-byte hexadecimal string. To get the order ID for a specific order, call the function `augur.api.Order.getOrderId`.

This function will fail if:
- `msg.sender` isn't the owner of the Order.
- `_orderId` is undefined.

Returns: true if the Order was successfully canceled, or false otherwise. (NOTE: The return value cannot be obtained reliably when calling externally.)

Claim Trading Proceeds Tx API
------------------------------
```javascript
// Claim Trading Proceeds Transaction API Examples:
var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

var _shareHolder = "0x5678ff3e9ce1c0459b309fac6dd4e69229b91567";
augur.api.ClaimTradingProceeds.claimTradingProceeds({
  _market: _market,
  _shareHolder: _shareHolder,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
#### [Claim Trading Proceeds Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/ClaimTradingProceeds.sol)

#### augur.api.ClaimTradingProceeds.claimTradingProceeds({ \_market, \_shareHolder, onSent, onSuccess, onFailed })

The `claimTradingProceeds` transaction attempts to collect trading profits from outstanding [Shares](#share) in [Finalized Market](#finalized-market) `_market` owned by `_shareHolder`. 

This transaction will fail if:
  - `_market` has not been Finalized and passed the [Post-Finalization Waiting Period](post-finalization-waiting-period).

Returns: true if the transaction was executed without any errors, or false otherwise.

Complete Sets Tx API
-----------------------------
```javascript
// Complete Sets Contract
var _market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";
var _amount = "50000000000000000000"; // 50.0

augur.api.CompleteSets.publicBuyCompleteSets({
  _market: _market,
  _amount: _amount,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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

This transaction will attempt to purchase `_amount` worth of [Shares](#share) in all [Outcomes](#outcome) for a specified `_market`. This transaction will fail if the `msg.sender` doesn't have enough of the `_market`'s denomination token to be able to afford `_amount` Shares in all Outcomes, or if the `_amount` is not a number between 1 and 2<sup>254</sup>. When successful, this transaction will spawn a `CompleteSets` event which will record the `msg.sender`, `_market`, type (buy), `_amount` purchased, number of Outcomes in the `_market`, `marketCreatorFee`, and the `reportingFee`. During a buy, the `marketCreatorFee` and `reportingFee` will be `0` because no fees are paid for purchasing Shares, only selling/settling Shares.

#### augur.api.CompleteSets.publicSellCompleteSets({ \_market, \_amount, onSent, onSuccess, onFailed })

This transaction will attempt to sell `_amount` worth of Shares in all Outcomes for a specified `_market`. This transaction will fail if the `msg.sender` doesn't own `_amount` of Shares in all Outcomes for the `_market`, or if the `_amount` is not a number between 1 and 2<sup>254</sup>. When successful this transaction will spawn a `CompleteSets` event which will record the `msg.sender`, `_market`, type (sell), `_amount` sold, number of Outcomes in the `_market`, `marketCreatorFee` paid for selling the Shares, and the `reportingFee` paid for selling the Shares.

Create Order Tx API
--------------------------
```javascript
// Make Order Transaction API Examples:
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
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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

This transaction will create a new [Order](#order) on the [Order Book](#order-book) for [Market](#market) `_market` trading on [Outcome](#outcome) `_outcome`. The required fields besides `_market` and `_outcome` are the `_type` of Order (1 for a bid, 2 for an ask), amount of [Shares](#share), `_attoshares`, denoted in [attoshares](#atto-prefix), and the `_displayPrice` for the Order. Optional params include `_betterOrderId`, `_worseOrderId`, `_tradeGroupId`, and the callbacks. The `_betterOrderId` and `_worseOrderId` are Order IDs of Orders already on the Order Book which should be better and worse than the order this transaction is intending to create. The `_tradeGroupId` is a field used by the Augur UI to group transactions and can be left blank.

This transaction will fail if:
  - `_type` is not a valid value of 1 or 2.
  - `_attoshares` is less than 0.
  - `_market` is undefined.
  - `_outcome` is less than 0 or greater than the total number of Outcomes for `_market`.
  - `_displayPrice` is below the `_market`'s minimum `_displayPrice` or above the `market`'s maximum `_displayPrice`.

Returns: the Order ID of the newly created Order, as a 32-byte hexadecimal value.

Dispute Crowdsourcer Tx API
----------------------------
```javascript
// Dispute Crowdsourcer Transaction API Examples:
var disputeCrowdsourcer = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";

augur.api.DisputeCrowdsourcer.fork({
  disputeCrowdsourcer: disputeCrowdsourcer,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0x89c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
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

var _redeemer = "0x55d6eaefcfaf7ea1e17c4768a554d57800699ae4";
augur.api.DisputeCrowdsourcer.redeem({
  disputeCrowdsourcer: disputeCrowdsourcer,
  _redeemer: _redeemer,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0xaac8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
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

augur.api.DisputeCrowdsourcer.withdrawInEmergency({
  disputeCrowdsourcer: disputeCrowdsourcer,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0xbbc8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
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
```
#### [Dispute Crowdsourcer Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/DisputeCrowdsourcer.sol)

#### augur.api.DisputeCrowdsourcer.fork({ disputeCrowdsourcer, onSent, onSuccess, onFailed }) 

Causes a [Child Universe](#child-universe) to be created for the [Outcome](#outcome) of the specified [Dispute Crowdsourcer](#dispute-crowdsourcer) and migrates the [REP](#rep) in the Crowdsourcer to the Child Universe. This function can be called only on the Crowdsourcers of a [Forked Market](#forked-market), and it can be called at any time after the [Fork](#fork) has begun (including after the [Market](#market) has been [Finalized](#finalized-market)). Once this function is called, `augur.api.DisputeCrowdsourcer.redeem` may be called by users who [Staked](#dispute-stake) on the Dispute Crowdsourcer's Outcome to redeem their Staked REP and collect any [Reporting Fees](#reporting-fee) (in ETH) that they are owed.

Returns: true if the function successfully completed without any errors, or false otherwise. (NOTE: The return value cannot be obtained reliably when calling externally.)

#### augur.api.DisputeCrowdsourcer.redeem({ disputeCrowdsourcer, \_redeemer, onSent, onSuccess, onFailed }) 

This function can be called only when a [Market](#market) in the [Dispute Crowdsourcer's](#dispute-crowdsourcer) [Universe](#universe) has [Forked](#fork) and `augur.api.Market.disavowCrowdsourcers` (in the case of Dispute Crowdsourcers of non-[Forked Markets](#forked-market)) or `augur.api.DisputeCrowdsourcer.fork` (in the case of Dispute Crowdsourcers of Forked Markets) has been called. 

When called on Dispute Crowdsourcers of non-Forked Markets, it will redeem any [REP](#rep) that `_redeemer` [Staked](#dispute-stake) on that Crowdsourcer, as well as any [Reporting Fees](#reporting-fee) (in Ether) that `_redeemer` is owed, to the Universe containing the Forked Market.

When called on Dispute Crowdsourcers of a Forked Market, it will redeem any REP that `_redeemer` Staked on that Crowdsourcer, as well as any Reporting Fees (in Ether) that `_redeemer` is owed, to the [Child Universe](#child-universe) for the [Outcome](#outcome) of that Crowdsourcer.

This function can be called on Dispute Crowdsourcers that have filled their [Dispute Bond](#dispute-bond), as well as ones that have not, provided that either `augur.api.Market.disavowCrowdsourcers` or `augur.api.DisputeCrowdsourcer.fork` has already been called. When a [Fork](#fork) occurs, all non-Forked Markets will have their [Tentative Outcome](#tentative-outcome) reset to the Outcome submitted in the [Initial Report](#initial-report) and be put back in the [Waiting for the Next Fee Window to Begin Phase](#waiting-for-the-next-fee-window-to-begin-phase).

Returns: true if `redeemer`'s Staked REP was redeemed without errors, or false otherwise. (NOTE: The return value cannot be obtained reliably when calling externally.)

#### augur.api.DisputeCrowdsourcer.withdrawInEmergency({ disputeCrowdsourcer, onSent, onSuccess, onFailed }) 

If a critical bug or vulnerability is found in Augur, the Augur development team can put Augur into a [halted](#developer-mode) state until the issue is resolved. In such instances, most regularly-used functions in Augur's backend will become unuseable until the system is returned to its normal state. When this happens, users can call the `withdrawInEmergency` function to withdraw the [REP](#rep) they [Staked](#dispute-stake) on the [Dispute Crowdsourcer's](#dispute-crowdsourcer) [Outcome](#outcome). Calling this function will not redeem any [Reporting Fees](#reporting-fee) because the total amount of Reporting Fees is not known until the end of the [Fee Window](#fee-window).

This transaction will fail if Augur is not currently in a halted state.

Returns: true if the REP was successfully withdrawn from the Dispute Crowdsourcer without any errors, or false otherwise. (NOTE: The return value cannot be obtained reliably when calling externally.)

```javascript
// Fee Window Transaction API Examples:
var feeWindow = "0xe5d6eaefcfaf7ea1e17c4768a554d57800699ea4";

var _attotokens = 100;
augur.api.FeeWindow.buy({
  feeWindow: feeWindow,
  _attotokens: _attotokens,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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

var _sender = "0x8886eaefcfaf7ea1e17c4768a554d57800699888";
augur.api.FeeWindow.redeem({
  feeWindow: feeWindow,
  _sender: _sender,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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

augur.api.FeeWindow.withdrawInEmergency({
  feeWindow: feeWindow,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
#### [Fee Window Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/FeeWindow.sol)

#### augur.api.FeeWindow.buy({ feeWindow, \_attotokens, onSent, onSuccess, onFailed })

Purchases the number of [Participation Tokens](#participation-token) specified by the integer `_attotokens`. 

This transaction will fail if:

- `_attotokens` is <= 0.
- Reporting is not currently active in the [Fee Window](#fee-window).
- All markets have not been [Finalized](#finalized-market) in the Fee Window.

These Participation Tokens can later be redeemed once the Fee Window is no longer active using the function `augur.api.FeeWindow.redeem`.

Returns: true if the Participation Tokens were successfully purchased. (NOTE: The return value cannot be obtained reliably when calling externally.)

#### augur.api.FeeWindow.redeem({ feeWindow, \_sender, onSent, onSuccess, onFailed })

Converts any [Participation Tokens](#participation-token) `_sender` has in the specified [Fee Window](#fee-window) to [Reputation Tokens](#rep), and gives the user any [Reporting Fees](#reporting-fee) he or she is owed in Ether. The parameter `_sender` is the Ethereum address of the user redeeming the Participation Tokens, as a hexadecimal string.

Returns: true if the Participation Tokens were withdrawn and converted to Reputation Tokens without any errors. If `_sender` does not have any Participation Tokens or Fee Tokens to be redeemed, this function will still return true, as long as no errors occured. (NOTE: The return value cannot be obtained reliably when calling externally.)

#### augur.api.FeeWindow.withdrawInEmergency({ feeWindow, onSent, onSuccess, onFailed })

If a critical bug or vulnerability is found in Augur, the Augur development team can put Augur into a [halted](#developer-mode) state until the issue is resolved. In such instances, most regularly-used functions in Augur's backend will become unuseable until the system is returned to its normal state. When this happens, users can call the `withdrawInEmergency` function to withdraw their Participation Tokens and convert them into Reputation Tokens.

This transaction will fail if Augur is not currently in a halted state.

Returns: true if the Participation Tokens were successfully withdrawn from the Fee Window and converted to Reputation Tokens. (NOTE: The return value cannot be obtained reliably when calling externally.)

Fill Order Tx API
--------------------------
```javascript
// Fill Order Transaction API Examples:
var _orderId = "0xea2c7476e61f5e2625e57df17fcce74741d3c2004ac657675f686a23d06e6091";
var _amountFillerWants = "10000000000000000000"; // 10.0
var _tradeGroupId = "0x0000000000000000000000000000000000000000000000000000000000000002";

augur.api.FillOrder.publicFillOrder({
  _orderId: _orderId,
  _amountFillerWants: _amountFillerWants,
  _tradeGroupId: _tradeGroupId,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
#### [Fill Order Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/FillOrder.sol)

#### augur.api.FillOrder.publicFillOrder({ fillOrder, \_orderID, \_amountFillerWants, \_tradeGroupID, onSent, onSuccess, onFailed })

Given an `_orderId` and the amount a [Filler](#order-filler) wants as `_amountFillerWants` denoted in [attoshares](#atto-prefix), this transaction will attempt to fill the [Order](#order) specified. If the `_amountFillerWants` is enough to fill the Order completely, then the Order will be removed from the [Order Book](#order-book), otherwise it will be adjusted to only include the remaining amount after filling the `_amountFillerWants` value that the [Filler](#order-filler) requests. This transaction requires `_orderId` and `_amountFillerWants` are defined. The [Creator](#order-creator) of the order specified by `_orderId` cannot be the `msg.sender` of this transaction. This transaction will return the fixed point amount remaining of the Order specified by `_orderId` after being filled, if it's completely filled this will return `0`. The `_tradeGroupId` is an optional value that is used by the Augur UI and can be left `undefined`.

Initial Reporter Tx API
----------------------
```javascript
// Initial Reporter Transaction API Examples:
var initialReporter = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.InitialReporter.fork({
  initialReporter: initialReporter,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0x7778f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2bfd",
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

augur.api.InitialReporter.redeem({
  initialReporter: initialReporter,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0x8768f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2abc",
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

var _newOwner = "0x9998ff3e9ce1c0459b309fac6dd4e69229b91777";
augur.api.InitialReporter.transferOwnership({
  initialReporter: initialReporter,
  _newOwner: _newOwner,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0x8768f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2abc",
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

augur.api.InitialReporter.withdrawInEmergency({
  initialReporter: initialReporter,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0x1238f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2aaa",
  blockNumber: 320490,
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
#### [Initial Reporter Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/InitialReporter.sol)

#### augur.api.InitialReporter.fork({ initialReporter, onSent, onSuccess, onFailed })

Causes a [Child Universe](#child-universe) to be created for the [Outcome](#outcome) of the [Initial Report](#initial-report) and migrates the [REP](#rep) [Staked](#dispute-stake) by the [Initial Reporter](#initial-reporter) to the Child Universe. This function can be called only on the `initialReporter` contract of a [Forked Market](#forked-market), and it can be called at any time after the [Fork](#fork) has begun (including after the [Market](#market) has been [Finalized](#finalized-market)). Once this function is called, `augur.api.InitialReporter.redeem` may be called to transfer the REP Staked on the Initial Report's Outcome (and the [No-Show Gas Bond](#no-show-gas-bond), in Ether, if applicable) to the Initial Reporter of the Forked Market.

Returns: true if the function successfully completed without any errors, or false otherwise. (NOTE: The return value cannot be obtained reliably when calling externally.)

#### augur.api.InitialReporter.redeem({ initialReporter, onSent, onSuccess, onFailed })

Redeems the [REP](#rep) that the [Designated Reporter](#designated-reporter) or [First Public Reporter](#first-public-reporter) Staked on the [Outcome](#outcome) of the [Initial Report](#initial-report). If the First Public Reporter submitted the Initial Report, this function will also redeem the [No-Show Gas Bond](#no-show-gas-bond) in Ether.

Returns: true if the REP (and, in the case of First Public Reporters, Ether) was successfully redeemed for the [Initial Reporter](#initial-reporter) without any errors, or false otherwise. (NOTE: The return value cannot be obtained reliably when calling externally.)

#### augur.api.InitialReporter.transferOwnership({ initialReporter, \_newOwner, onSent, onSuccess, onFailed })

The `transferOwnership` transaction will change the owner of `initialReporter` from the existing owner to the Ethereum address hexadecimal string `_newOwner`. 

This function will fail if:
  - `msg.sender` isn't the owner of `initialReporter`. 

Returns: true if the transaction executed without any errors, or false otherwise.

#### augur.api.InitialReporter.withdrawInEmergency({ initialReporter, onSent, onSuccess, onFailed })

If a critical bug or vulnerability is found in Augur, the Augur development team can put Augur into a [halted](#developer-mode) state until the issue is resolved. In such instances, most regularly-used functions in Augur's backend will become unuseable until the system is returned to its normal state. When this happens, [Initial Reporters](#initial-reporter) can call the `withdrawInEmergency` function to withdraw the [REP](#rep) they [Staked](#dispute-stake) on the [Initial Report](#dispute-crowdsourcer) [Outcome](#outcome), as well as the [No-Show Gas Bond](no-show-gas-bond) (in cases where the [First Public Reporter](#first-public-reporter) submitted the Initial Report instead of the [Designated Reporter](#designated-reporter)).

This transaction will fail if Augur is not currently in a halted state.

Returns: true if the REP and was successfully withdrawn from `initialReporter` contract without any errors, or false otherwise. (NOTE: The return value cannot be obtained reliably when calling externally.)

Mailbox Tx API
----------------------
```javascript
// Mailbox Transaction API Examples:
var mailbox = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

augur.api.Mailbox.transferOwnership({
  mailbox: mailbox,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
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
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
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
#### [Mailbox Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Mailbox.sol)

#### augur.api.Mailbox.transferOwnership({ mailbox, \_newOwner, onSent, onSuccess, onFailed })

The `transferOwnership` transaction will change the current [Market Creator Mailbox](#market-creator-mailbox) owner to the specified `_newOwner`.

This function will fail if:
  - `msg.sender` isn't the owner of `mailbox`. 

Returns: true if the transaction executed without any errors, or false otherwise.

#### augur.api.Mailbox.withdrawEther({ mailbox, onSent, onSuccess, onFailed })

Transfers all ETH in the [Market Creator Mailbox](#market-creator-mailbox) to the Market Creator's address.  

This transaction will fail if:
  - `msg.sender` isn't the owner of the specified `mailbox`. 

Returns: true if the transaction executed without any errors, or false otherwise.

#### augur.api.Mailbox.withdrawTokens({ mailbox, \_token, onSent, onSuccess, onFailed })

Transfers all tokens of type `_token` in the [Market Creator Mailbox](#market-creator-mailbox) to the Market Creator's address.  

This transaction will fail if:
  - `msg.sender` isn't the owner of the specified `mailbox`. 

Returns: true if the transaction executed without any errors, or false otherwise.

Market Tx API
----------------------
```javascript
// Market Transaction API Examples:
var market = "0x9368ff3e9ce1c0459b309fac6dd4e69229b91a42";

var _payoutNumerators = [ 0, 1000 ];
var _invalid = false;
var _amount = 100;
augur.api.Market.contribute({
  market: market,
  _payoutNumerators: _payoutNumerators,
  _invalid: _invalid,
  _amount: _amount,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
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

augur.api.Market.disavowCrowdsourcers({
  market: market,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320491,
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

var _payoutNumerators = [ 0, 1000 ];
var _invalid = false;
augur.api.Market.doInitialReport({
  market: market,
  _payoutNumerators: _payoutNumerators,
  _invalid: _invalid,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320492,
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

augur.api.Market.finalize({
  market: market,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320493,
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

augur.api.Market.finalizeFork({
  market: market,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320494,
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

augur.api.Market.migrateThroughOneFork({
  market: market,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
});
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320496,
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

augur.api.Market.withdrawInEmergency {
  market: market,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
};
// example onSuccess output:
{
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320499,
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

#### augur.api.Market.contribute({ market, \_payoutNumerators, \_invalid, \_amount, onSent, onSuccess, onFailed })

Contributes `_amount` [REP](#rep) to the [Dispute Crowdsourcer](#dispute-crowdsourcer) represented by [Payout Set](#payout-set) `_payoutNumerators` and `_invalid` in order to help [Challenge](#challenge) the [Market](#market) `market`'s [Tentative Outcome](#tentative-outcome). If the amount of REP in the Dispute Crowdsourcer plus `amount` is greater than the total REP required to fill the [Dispute Bond](#dispute-bond), this function will only contribute the remaining amount of REP required to fill the Dispute Bond on behalf of the caller.

`_payoutNumerators` is an array representing the [Payout Set](#payout-set) for the Market, and `_invalid` is a boolean value for whether the Market is [Invalid](#invalid-outcome). (For information about what the Payout Set should look like for an Invalid Market, refer to the [Invalid Outcome glossary entry](#invalid-outcome).)

This function will fail if:
  - The Market is in a [Fee Window](#fee-window) that is not active.
  - The [Outcome](#outcome) specified by `_payoutNumerators` and `_invalid` is already the Tentative Outcome of the Market.
  - `_invalid` is true and the Numerators in `_payoutNumerators` are not all the same value.

Returns: true if the function executed without any errors, or false otherwise.

#### augur.api.Market.disavowCrowdsourcers({ market, onSent, onSuccess, onFailed })

This function may only be called on non-[Forked Markets](#forked-market) in the event that another [Market](#market) in the same [Universe](#universe) [Forks](#fork). Calling it will "disavow" all [Dispute Crowdsourcers](#dispute-crowdsourcer) of the Market, meaning the Market's [Tentative Outcome](#tentative-outcome) will be reset back to the [Outcome](#outcome) of the [Initial Report](#initial-report), and all [REP](#rep) [Staked](#dispute-stake) in the Crowdsourcers will be redeemable by users who contributed to them using the function `augur.api.DisputeCrowdsourcer.redeem`. 

This function will fail if:
  - There is not a Forked Market in `market`'s Universe.
  - `market` is a Forked Market.

Returns: true if the function executed without any errors, or false otherwise.

#### augur.api.Market.doInitialReport({ market, \_payoutNumerators, \_invalid, onSent, onSuccess, onFailed })

Submits an [Initial Report](#initial-report) for [Market](#market) `market` with [Payout Set](#payout-set) `_payoutNumerators` as an array and `_invalid` as a boolean value for whether the Market is [Invalid](#invalid-outcome).

This function will fail if:
  - The Market's event end time has not passed.
  - The caller of the function is not the [Designated Reporter](#designated-reporter) and the [Designated Reporting Phase](#designated-reporting-phase) has not ended.
  - `_invalid` is true and the Numerators in `_payoutNumerators` are not all the same value. (For information about what the Payout Set should look like for an Invalid Market, refer to the [Invalid Outcome glossary entry](#invalid-outcome).)

Returns: true if the function executed without any errors, or false otherwise.

#### augur.api.Market.finalize({ market, onSent, onSuccess, onFailed })

This function will [Finalize](#finalized-market) the [Market](#market) `market`, meaning it will set the winning [Payout Distribution Hash](#payout-distribution-hash) for the Market, redistribute [REP](#rep) Staked on non-winning [Outcomes](#outcome) to REP holders who Staked on the winning Outcome, and distribute the [Validity Bond](#validity-bond) based on whether the Market resolved as [Invalid](#invalid-outcome). Then, once the [Post-Finalization Waiting Period](post-finalization-waiting-period) has elapsed, users can [Settle](#settlement) their [Shares](#share).

This function will fail if:

  - The [Initial Report](#initial-report) has not been submitted.
  - The [Fee Window](#fee-window) has not ended.
  - `market` is a Forked Market.

Returns: true if the function executed without any errors, or false otherwise.

#### augur.api.Market.finalizeFork({ market, onSent, onSuccess, onFailed })

This function will [Finalize](#finalized-market) the [Forked Market](#forked-market) `market`, meaning it will set the winning [Payout Distribution Hash](#payout-distribution-hash) for the Market. Then, once the [Post-Finalization Waiting Period](post-finalization-waiting-period) has elapsed, users can [Settle](#settlement) their [Shares](#share).

This function will fail if:
  
  - `market` is not a Forked Market.

Returns: true if the function executed without any errors, or false otherwise.

#### augur.api.Market.migrateThroughOneFork({ market, onSent, onSuccess, onFailed })

This transaction attempts to migrate the [Market](#market) into a winning [Child Universe](#child-universe) from a [Forked](#fork) [Parent Universe](#parent-universe). When a Fork occurs, there is a [Fork Period](#fork-period) that occurs wherein [REP](#rep) holders migrate their REP to the [Universe](#universe) they want to continue in. Once the Fork Period ends, the Child Universe with the most REP migrated to it will be declared the Winning Universe. Calling `migrateThroughOneFork` attempts to move the Market from a Parent Universe to the Winning Universe after it's been decided. This method will throw if the Fork Period isn't over, so there is no Winning Universe to migrate to yet. 

Returns: `1` if `market` was successfully migrated and `0` if no migration was required.

#### augur.api.Market.transferOwnership({ market, \_newOwner, onSent, onSuccess, onFailed })

The `transferOwnership` transaction will change the current [Market](#market) owner to the specified `_newOwner`. This is used to transfer ownership of a Market from one address to another. This transaction will fail if the `msg.sender` isn't the owner of the specified `market`. 

#### augur.api.Market.withdrawInEmergency({ market, onSent, onSuccess, onFailed })

If a critical bug or vulnerability is found in Augur, the development team can put it the system into a [halted](#developer-mode) state until the issue is resolved. In such instances, most regularly-used functions in Augur's backend will become unuseable until the system is returned to its normal state. When this happens, users can call the `withdrawInEmergency` function to withdraw their Reputation Tokens from a particular Market.

This transaction will fail if:
  - Augur is not currently in a halted state.

Returns: true if the Reputation Tokens were successfully withdrawn from the Market. (NOTE: The return value cannot be obtained reliably when calling externally.)

Reputation Token Tx API
--------------------------------
```javascript
// Reputation Token Transaction API Examples:
var reputationToken = "0x2a73cec0b62fcb8c3120bc80bdb2b1351c8c2d1e";
var _spender = "0xea674fdde714fd979de3edf0f56aa9716b898ec8";
var _from = "0x1a05071893b764109f0bbc5b75d78e3e38b69ab3";
var _attotokens = "100000000000000000000";

augur.api.ReputationToken.approve({
  reputationToken: reputationToken,
  _spender: _spender,
  _value: _attotokens,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  _attotokens: _attotokens,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  _value: _attotokens,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  _value: _attotokens,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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

Returns: true if the transaction executed without any errors, or false otherwise.

#### augur.api.ReputationToken.migrateFromLegacyReputationToken({ reputationToken, onSent, onSuccess, onFailed })

This function will migrate [Legacy REP](#legacy-rep) tokens from the Legacy REP contract owned by `msg.sender` to the `reputationToken` provided. `msg.sender` will add whatever `msg.sender`'s balance was for the legacy rep contract to the `reputationToken` contract.

Returns: true if the transaction executed without any errors, or false otherwise.

#### augur.api.ReputationToken.migrateOut({ reputationToken, \_destination, \_attotokens, onSent, onSuccess, onFailed })

This function migrates a `_reporter`'s [REP](#rep) (denoted as [attoREP](#atto-prefix) in `_attotokens`) from one `reputationToken` address to another (`_destination`). The `msg.sender` of this transaction must be the `_reporter` provided or the `msg.sender` must be approved to spend `_attotokens` amount of REP for the `_reporter` provided.

Returns: true if the transaction executed without any errors, or false otherwise.

#### augur.api.ReputationToken.transfer({ reputationToken, \_to, \_value, onSent, onSuccess, onFailed })

If the `msg.sender` of the `transfer` transaction has enough of `reputationToken` to be able to transfer `_value` (denoted in attotokens) worth to the `_to` address and `_value` is a number between 1 and 2<sup>254</sup> then this transaction will send `_value` (in attotokens) worth of `reputationToken` to the specified `_to` address from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address (`msg.sender`), `_to` address, and `_value` amount transferred.

Returns: true if the transaction executed without any errors, or false otherwise.

#### augur.api.ReputationToken.transferFrom({ reputationToken, \_from, \_to, \_value, onSent, onSuccess, onFailed })

If the `_from` address of the `transferFrom` transaction has enough of `reputationToken` to be able to transfer `_value` (denoted in attotokens) worth to the `_to` address, `_value` is a number between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `_value` worth of `reputationTokens` for the `_from` address then this transaction will send `_value` worth of `reputationToken` to the specified `_to` address from the `_from` address. This transaction will spawn a `Transfer` event which will record the `_from` address, `_to` address, and `_value` (in attotokens) amount transferred.

Returns: true if the transaction executed without any errors, or false otherwise.

Share Token Tx API
---------------------------
```javascript
// Share Token Transaction API Examples:
var shareToken = "0xa22c79a48f51df6d0863821bd1dd2c5d6f511bc5";
var _spender = "0x01f50356c280cd886dd058210937160c73700a4b";
var _from = "0x1a05071893b764109f0bbc5b75d78e3e38b69ab3";
var _attotokens = "100000000000000000000";

augur.api.ShareToken.approve({
  shareToken: shareToken,
  _spender: _spender,
  _value: _attotokens,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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

Allows the `_spender` the ability to spend up to `_value` worth of the specified `shareToken` for the `msg.sender` of this `approve` transaction. This transaction will spawn an `Approval` event which will record the owner address (`msg.sender`), `_spender`, and `_value` denoted in [attotokens](#atto-prefix) approved.

Returns: true if the transaction executed without any errors, or false otherwise.

#### augur.api.ShareToken.transfer({ shareToken, \_to, \_value, onSent, onSuccess, onFailed })

If the `msg.sender` of the `transfer` transaction has enough of `shareToken`s to be able to transfer `_value` worth to the `_to` address and `_value` is a valid number between 1 and 2<sup>254</sup> then this transaction will send `_value` worth of `shareToken` to the specified `_to` address from the `msg.sender`. This transaction will spawn a `Transfer` event which will record the from address (`msg.sender`), `_to` address, and `_value` amount transferred denoted in [attotokens](#atto-prefix).

Returns: true if the transaction executed without any errors, or false otherwise.

#### augur.api.ShareToken.transferFrom({ shareToken, \_from, \_to, \_value, onSent, onSuccess, onFailed })

If the `_from` address of the `transferFrom` transaction has enough of `shareToken` to be able to transfer `_value` worth to the `_to` address, `_value` is a valid number between 1 and 2<sup>254</sup>, and the `msg.sender` has the approval to spend at least `_value` worth of `shareToken` for `_from` address then this transaction will send `_value` worth of `shareToken` to the specified `_to` address from the `_from` address. This transaction will spawn a `Transfer` event which will record the `_from` address, `_to` address, and `_value` amount transferred denoted in [attotokens](#atto-prefix).

Returns: true if the transaction executed without any errors, or false otherwise.

Trade Tx API
---------------------
```javascript
// Trade Transaction API Examples:
var _market = "0x7e8e07364ccde43ba5159537404924e86ca53c92";
var _outcome = "1";
var _fxpAmount = "10000000000000000000"; // 10.0
var _price = "500000000000000000"; // 0.5
var _betterOrderId = "0xea2c7476e61f5e2625e57df17fcce74741d3c2004ac657675f686a23d06e6091";
var _worseOrderId = "0xed42c0fab97ee6fbde7c47dc62dc3ad09e8d3af53517245c77c659f7cd561426";
var _tradeGroupId = "0x0000000000000000000000000000000000000000000000000000000000000003";

augur.api.Trade.publicBuy({
  _market: _market,
  _outcome: _outcome,
  _fxpAmount: _fxpAmount,
  _price: _price,
  _betterOrderId: _betterOrderId,
  _worseOrderId: _worseOrderId,
  _tradeGroupId: _tradeGroupID,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  _price: _price,
  _betterOrderId: _betterOrderId,
  _worseOrderId: _worseOrderId,
  _tradeGroupId: _tradeGroupId,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  _price: _price,
  _betterOrderId: _betterOrderId,
  _worseOrderId: _worseOrderId,
  _tradeGroupId: _tradeGroupId,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
  _price: _price,
  _betterOrderId: _betterOrderId,
  _worseOrderId: _worseOrderId,
  _tradeGroupId: _tradeGroupId,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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

#### augur.api.Trade.publicBuy({ \_market, \_outcome, \_fxpAmount, \_price, \_betterOrderId, \_worseOrderId, \_tradeGroupId, onSent, onSuccess, onFailed })

The `publicBuy` transaction is used to purchase [Shares](#share) for a specified `_market` and `_outcome`. The amount of Shares to purchase is `_fxpAmount`, denoted in [attoshares](#atto-prefix), and the price point to buy at is `_price`, denoted in attotokens of the `_market`'s denomination token. This transaction will take [Orders](#order) off the [Order Book](#order-book) that can be filled with this request, otherwise this transaction will create a new Order to buy `_fxpAmount` of Shares at `_price`. In order to optimize the process of putting the Order on the Order Book, `_betterOrderId` and `_worseOrderId` must be specified as 32-byte hexadecimal strings. These are the orders with the next best/next worse price after `_price`, and their IDs can be obtained by calling `augur.trading.getBetterWorseOrders`. The `_tradeGroupId` is an optional argument used by the Augur UI and can be left `undefined`. 

Returns: `1` if this Order was filled completely, or if this Order can't be filled immediately, an Order will be created and the `_orderId` of that new Order will be returned.

#### augur.api.Trade.publicSell({ \_market, \_outcome, \_fxpAmount, \_price, \_betterOrderId, \_worseOrderId, \_tradeGroupId, onSent, onSuccess, onFailed })

The `publicSell` transaction is used to purchase [Shares](#share) for a specified `_market` and `_outcome`. The amount of Shares to purchase is `_fxpAmount`, denoted in [attoshares](#atto-prefix), and the price point  to sell at is `_price`, denoted in attotokens of the `_market`'s denomination token. This transaction will take [Orders](#order) off the [Order Book](#order-book) that can be filled with this request, otherwise this transaction will create a new Order to sell `_fxpAmount` of Shares at `_price`. In order to optimize the process of sorting the new Order on the Order Book, `_betterOrderId` and `_worseOrderId` must be specified as 32-byte hexadecimal strings. These are the orders with the next best/next worse price after `_price`, and their IDs can be obtained by calling `augur.trading.getBetterWorseOrders`. The `_tradeGroupId` is an optional argument used by the Augur UI and can be left `undefined`. 

Returns: `1` if this Order was filled completely, or if this Order can't be filled immediately, an Order will be created and the `_orderId` of that new Order will be returned.

#### augur.api.Trade.publicTrade({ \_direction, \_market, \_outcome, \_fxpAmount, \_price, \_betterOrderId, \_worseOrderId, \_tradeGroupId, onSent, onSuccess, onFailed })

The `publicTrade` transaction works like `publicBuy` or `publicSell`; however a direction must be specified. The `_direction` must be either `1` for buying or `2` for selling. 

Returns: `1` if this order was filled completely, or if this order can't be filled immediately an order will be created and the `_orderId` of that new order will be returned.

#### augur.api.Trade.publicTakeBestOrder({ \_direction, \_market, \_outcome, \_fxpAmount, \_price, \_betterOrderId, \_worseOrderId, \_tradeGroupId, onSent, onSuccess, onFailed })

The `publicTakeBestOrder` transaction works similarly to `augur.api.Trade.publicTrade`, except it does not create an [Order](#order) if the request can't be filled. The `_direction` must be either `1` for buying or `2` for selling. 

Returns: the fixed point amount not filled by the order, so `0` for a completely Filled Order, some other number if this request could only be partially filled.

TradingEscapeHatch Tx API
---------------------
```javascript
// TradingEscapeHatch Transaction API Examples:
var _market = "0x465407364ccde43ba5159537404924e86ca53aaa";

augur.api.TradingEscapeHatch.claimSharesInUpdate({
  _market: _market,
  onSent: function (result) { console.log(result); },
  onSuccess: function (result) { console.log(result); },
  onFailed: function (result) { console.log(result); }
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
#### [Trading Escape Hatch Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/trading/TradingEscapeHatch.sol)

#### augur.api.TradingEscapeHatch.claimSharesInUpdate({ \_market, onSent, onSuccess, onFailed })

If Augur needs to be [halted](#developer-mode) by the development team (for example, if a vulnerability is discovered), calling this function on [Market](#market) `_market` will cash out the caller's [Shares](#share) to Ether for that Market and return them to the caller's Ethereum address. 

This transaction will fail if:
  - Augur is not in a halted state.

Returns: true if , or false otherwise.

Universe Tx API
---------------------
```javascript
// Universe Transaction API Examples:
var universe = "0x777407364ccde43ba5159537404924e86ca53444";

var _extraInfo = { 
  "resolutionSource": "http://www.spacex.com", 
  "tags": [ "SpaceX", "spaceflight" ], 
  "longDescription": "SpaceX hit a big milestone on Friday with NASA confirming on Friday that the Elon Musk-led space cargo business will launch astronauts to the International Space Station by 2017." 
};
augur.api.Universe.createBinaryMarket({
  universe: universe,
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
  universe: universe,
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

var _parentPayoutNumerators = [ 0, 1000 ];
var _parentInvalid = false;
augur.api.Universe.createChildUniverse({
  universe: universe,
  _parentPayoutNumerators: _parentPayoutNumerators,
  _parentInvalid: _parentInvalid,
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

var _extraInfo = { 
  "resolutionSource": "http://forecast.weather.gov", 
  "tags": ["San Francisco", "weather"], 
  "longDescription": "" 
};
augur.api.Universe.createScalarMarket({
  universe: universe,
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
  universe: universe,
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
  universe: universe,
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
  universe: universe,
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
  universe: universe,
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
  universe: universe,
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
  universe: universe,
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

augur.api.Universe.getOrCreateCurrentFeeWindow({
  universe: universe,
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

var _timestamp = 1401003133;
augur.api.Universe.getOrCreateFeeWindowByTimestamp({
  universe: universe,
  _timestamp: _timestamp,
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320560,
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

augur.api.Universe.getOrCreateFeeWindowForForkEndTime({
  universe: universe,
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320561,
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

augur.api.Universe.getOrCreateNextFeeWindow({
  universe: universe,
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320562,
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

augur.api.Universe.getOrCreatePreviousFeeWindow({
  universe: universe,
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320563,
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

var _reportingParticipants = [ ];
var _feeWindows = [ ];
augur.api.Universe.redeemStake({
  universe: universe,
  _reportingParticipants: _reportingParticipants,
  _feeWindows: _feeWindows,
  onSent: function (result) { console.log("onSent result:", result); },
  onSuccess: function (result) { console.log("onSuccess result:", result); },
  onFailed: function (result) { console.log("onFailed result:", result); }
});
// example onSuccess output:
onSuccess result: {
  blockHash: "0x38c8f12c226b8829ae493da94a730d6c149bf9a0578aac151f43028032ea2efb",
  blockNumber: 320564,
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
#### [Universe Contract Code](https://github.com/AugurProject/augur-core/blob/master/source/contracts/reporting/Universe.sol)
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

#### augur.api.Universe.createCategoricalMarket({ \_endTime, \_feePerEthInWei, \_denominationToken, \_designatedReporterAddress, \_outcomes, \_topic, \_description, \_extraInfo, onSent, onSuccess, onFailed, tx })

Creates a new [Categorical Market](#categorical-market). After the transaction has completed successfully, the Market address can be obtained by calling `augur.createMarket.getMarketFromCreateMarketReceipt`.

#### Parameters:

* `_endTime` - Unix timestamp for the [End Time](#end-time) of the [Market](#market). 
* `_feePerEthInWei` - [Creator Fee](#creator-fee) (in Wei) that is collected for every 1 ETH worth of [Shares](#share) [Settled](#settlement). 
* `denominationToken` is the token the Market is denominated in. Currently, Markets are only denominated in ETH (i.e., the Cash contract in Augur's smart contracts), but Augur is expected to support other tokens in the future. 
* `_designatedReporterAddress` - Address of the [Designated Reporter](#designated-reporter). 
* `_outcomes` (Array.&lt;string>) Array of names for all possible outcomes for the Market event. 
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

#### augur.api.Universe.createChildUniverse({ \_parentPayoutNumerators, \_parentInvalid, onSent, onSuccess, onFailed })

Returns Ethereum contract address corresponding the [Child Universe](#child-universe) with [Payout Set](#payout-set) `_parentPayoutNumerators`, as an array of integers, and `_parentInvalid`, as a boolean value. If the Child Universe does not already exist, it will be created. 

This transaction will fail if:
  - The Universe does not have a [Forked Market](#forked-market).
  - `_parentInvalid` is true and the Numerators in `_parentPayoutNumerators` are not all the same value. (For information about what the Payout Set should look like for an Invalid [Market](#market), refer to the [Invalid Outcome glossary entry](#invalid-outcome).)

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

#### augur.api.Universe.getOrCacheDesignatedReportNoShowBond({ universe, onSent, onSuccess, onFailed })

Returns the [Designated Report No-Show REP Bond](#designated-report-no-show-rep-bond) for [Markets](#market) in the [Universe](#universe), priced in [attoREP](#atto-prefix). If the value of the Designated Report No-Show REP Bond for the current [Fee Window](#fee-window) has not already been cached in the Universe contract, this function will cache it.

#### augur.api.Universe.getOrCacheDesignatedReportStake({ universe, onSent, onSuccess, onFailed })

Returns the amount of stake in [attoREP](#atto-prefix) that the [Designated Reporter](#designated-reporter) must put up when submitting a [Designated Report](#designated-report) in the [Universe](#universe). If this value for the current [Fee Window](#fee-window) has not already been cached in the Universe contract, this function will cache it.

#### augur.api.Universe.getOrCacheMarketCreationCost({ universe, onSent, onSuccess, onFailed })

Returns the estimated amount of [attoETH](#atto-prefix) required to create a [Market](#market) in the [Universe](#universe). The amount returned by this function is equivalent to the sum of `augur.api.Universe.getOrCacheValidityBond()` and `augur.api.Universe.getOrCacheTargetReporterGasCosts()`. If the values of the [Validity Bond](#validity-bond) and the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) for the current [Fee Window](#fee-window) have not already been cached in the Universe contract, this function will cache them.

#### augur.api.Universe.getOrCacheReportingFeeDivisor({ universe, onSent, onSuccess, onFailed })

Returns the number by which the total payout amount for a [Market](#market) is divided in order to calculate the [Reporting Fee](#reporting-fee). If this value for the current [Fee Window](#fee-window) has not already been cached in the Universe contract, this function will cache it.

#### augur.api.Universe.getOrCacheTargetReporterGasCosts({ universe, onSent, onSuccess, onFailed })

Returns the [Designated Report No-Show Gas Bond](#designated-report-no-show-gas-bond) in [attoETH](#atto-prefix) that is paid to the [First Public Reporter](#first-public-reporter) in the event of a [Designated Report](#designated-report) no-show, or refunded to the [Market Creator Mailbox](#market-creator-mailbox) if the [Designated Reporter](#designated-reporter) does report. The amount returned by this function will typically be well above the actual cost to create a Market, just to ensure the Market creation will succeed. If the Designated Report No-Show Gas Bond for the current [Fee Window](#fee-window) has not already been cached in the Universe contract, this function will cache it.

#### augur.api.Universe.getOrCacheValidityBond({ universe, onSent, onSuccess, onFailed })

Returns the amount the [Market Creator](#market-creator) must pay for the [Validity Bond](#validity-bond), denominated in [attoETH](#atto-prefix), when creating a [Market](#market). If the Validity Bond for the current [Fee Window](#fee-window) has not already been cached in the Universe contract, this function will cache it. (This amount will be refunded to the Market Creator if the [Final Outcome](#final-outcome) of the Market is not invalid.)

#### augur.api.Universe.getOrCreateCurrentFeeWindow({ universe, onSent, onSuccess, onFailed })

Returns the Ethereum contract address, as a hexadecimal string, of the [Fee Window](#fee-window) that is currently active in the specified [Universe](#universe) `universe`. If the contract address for the Fee Window does not exist yet, this function will create it.

#### augur.api.Universe.getOrCreateFeeWindowByTimestamp({ universe, _timestamp, onSent, onSuccess, onFailed })

Returns the Ethereum contract address, as a hexadecimal string, of the active [Fee Window](#fee-window) at the Unix timestamp `_timestamp` in the specified [Universe](#universe) `universe`. If the contract address for the Fee Window does not already exist, this function will create it.

#### augur.api.Universe.getOrCreateFeeWindowForForkEndTime({ universe, onSent, onSuccess, onFailed })

Returns the Ethereum contract address, as a hexadecimal string, of the [Fee Window](#fee-window) starting at the end of the current [Fork](#fork) in the [Universe](#universe) `universe`. If the contract address for the Fee Window does not already exist, this function will create it.

#### augur.api.Universe.getOrCreateNextFeeWindow({ universe, onSent, onSuccess, onFailed })

Returns the Ethereum contract address, as a hexadecimal string, of the [Fee Window](#fee-window) that will be active after the current Fee Window ends in the specified [Universe](#universe) `universe`. If the contract address for the Fee Window does not exist yet, this function will create it.

#### augur.api.Universe.getOrCreatePreviousFeeWindow({ universe, onSent, onSuccess, onFailed })

Returns the Ethereum contract address, as a hexadecimal string, of the [Fee Window](#fee-window) that was active just before the current Fee Window in the specified [Universe](#universe) `universe`. If the contract address for the Fee Window does not exist yet, this function will create it.

#### augur.api.Universe.redeemStake({ universe, _reportingParticipants, _feeWindows, onSent, onSuccess, onFailed })

Calls the `redeem` function for all Ethereum contract addresses in the arrays `_reportingParticipants` and `_feeWindows` using the caller's address as the redeemer. `_reportingParticipants` can contain Ethereum contract addresses for [DisputeCrowdsourcers](#dispute-crowdsourcer-tx-api), [InitialReporters](#initial-reporter-tx-api), or both, as hexadecimal strings. `_feeWindows` can only contain the Ethereum contract addresses of [Fee Windows](#fee-window). This function is intended as easy way to redeem Stake in multiple [Dispute Crowdsourcers](#dispute-crowdsourcer), [Initial Reports](#initial-report), and Fee Windows at once.

Returns: true if the function executed without any errors.