Callbacks
=========

```javascript
/**
* Check account 0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b's cash balance.
*/
const address = "0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b";
// synchronous
const money = augur.api.Cash.balanceOf({ address });
// money = 9999.495000000000000001

// asynchronously
augur.api.Cash.balanceOf({ address }, balance => console.log(balance));
// prints 9999.495000000000000001

/**
* Send 50 cash to 0xdb719489b265c4221cfd78852a616a9bbf9168fa. Because this
* submits a transaction to the network, it can only be done asynchronously. It also needs to be signed.
*/
// the privateKey buffer of the sending account
const privateKey = <Buffer ...>;

augur.api.Cash.transfer({
  _signer: privateKey,
  to: "0xdb719489b265c4221cfd78852a616a9bbf9168fa",
  value: 50,
  onSent: sent => console.log(sent),
  onSuccess: success => console.log(success),
  onFailed: failed => console.log(failed)
});
// Printed almost immediately; the callReturn field is the transfer method's
// return value. If the transaction was sent successfully, callReturn will
// be the amount of cash sent. txHash (the transaction hash) uniquely
// identifies this transaction, and can be used to look it up.
Sent: {
  txHash: '0xd5743b5b3bfdec989692b69d68fd8c979120e82cd199fdd80cff36dba42befbe',
  callReturn: '50'
}

// Printed after about 12 seconds, when the transaction is detected to have a
// non-null blockHash value.
Success: {
  nonce: '0x6f',
  blockHash: '0x2f10d8cdb8b2746a5ed24f4021caffae3f25abca297622984c2baa69db0ac50f',
  blockNumber: '0x2c58',
  transactionIndex: '0x0',
  from: '0x05ae1d0ca6206c6168b42efcd1fbe0ed144e821b',
  to: '0xdb719489b265c4221cfd78852a616a9bbf9168fa',
  value: '0x0',
  gas: '0x2fd618',
  gasPrice: '0x11102181f5e',
  input: '0x86744558000000000000000000000000db719489b265c4221cfd78852a616a9bbf9168fa0000000000000000000000000000000000000000000000000000000000000032',
  callReturn: '50',
  txHash: '0xd5743b5b3bfdec989692b69d68fd8c979120e82cd199fdd80cff36dba42befbe'
}
```
Augur API methods that submit transactions to the network (using the `eth_sendTransaction` RPC) take three callbacks as part of the parameters object:

- onSent: fires when the transaction has been broadcast to the network.
- onSuccess: fires when the transaction has been successfully incorporated into a block.
- onFailed: fires if the RPC request returns an error.

The `onSent`, `onSuccess`, and `onFailed` callbacks are optional.  If `onSent` is not included, then the transaction will run in synchronous (blocking) mode; if it is included, then the transaction will be run in asynchronous mode.  If the `onSuccess` callback is supplied, augur.js will issue an `eth_getTransaction` request upon receipt of every new block, until the transaction record contains a non-null `blockHash` value, or the transaction record itself returns `null` (indicating that the transaction has failed).

Augur API methods that perform read-only RPC requests that do not submit transactions to the network (using the `eth_call` RPC) take a single optional callback as their last parameter. Like `sendTransaction` requests, call requests can be synchronous or asynchronous: if a callback is supplied, the request will be made asynchronously.

<aside class="warning">Synchronous HTTP RPC is generally not recommended, especially if augur.js is running in the browser.  Synchronous RPC requests block the main JavaScript thread, which essentially freezes the browser!</aside>
