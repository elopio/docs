Accounts
========
```javascript
/**
 * Create an account using the augur.accounts.register method.
 */
 const password = "thisismysupersecurepassword";

 augur.accounts.register(password, account => console.log("Account:", account));

// output
account = {
  address: "0x0ab5f1a15f2462eba143ecc8e1733f44dfe019bf",
  derivedKey: <Buffer ...>,
  keystore: { /* ... */ },
  privateKey: <Buffer ...>
};

const keystore = account.keystore;

augur.accounts.login(keystore, password, account => console.log("Account:", account));

// output
account = {
  address: "0x0ab5f1a15f2462eba143ecc8e1733f44dfe019bf",
  derivedKey: <Buffer ...>,
  keystore: { /* ... */ },
  privateKey: <Buffer ...>
};

// Augur.js does not store any account data. Augur.js simply returns the important information. You can use the privateKey Buffer returned to sign your transactions.
```
Augur.js includes a trustless account management system. The purpose of the accounts system is to allow people to use Augur without needing to run an Ethereum node themselves, as running a full Ethereum node can be resource-intensive.

To use the account system, the user specifies a password. Everything else is done automatically for the user. The only requirement for the password is that it be at least 6 characters long.

A private key (+ derived public key and address) is automatically generated for the user.  A secret key derived from the password using PBKDF2, along with a random 128-bit initialization vector, is used to encrypt the private key (using AES-256). Nothing is stored by Augur.js, the account object will be returned to the callback provided or simply returned if no callback is provided.

The Augur UI will handle your account information for you, but if you are using Augur.js on it's own you will need to manage the account yourself. Augur.js doesn't sign any transactions for you if you aren't using the Augur UI.

If you want to use the Augur.js API directly, you will need to sign any transaction that will modify information on the blockchain (non-call transactions). All transactions take a `_signer` parameter which should be set to the sending account's `privateKey` Buffer or a function that will sign a transaction (hardware wallet).

<aside class="notice">Since the user's account key is an ordinary Ethereum private key, the user's key (and therefore their funds and Reputation) can be used with any Ethereum node. Therefore, although the accounts system is managed using an ordinary web server, since the user's funds are neither tied to nor controlled by our server, the accounts are still decentralized in the ways that (in our opinion) matter.</aside>
