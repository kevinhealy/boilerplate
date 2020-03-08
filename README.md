# sCrypt Project Boilerplate

## Quickstart
```
npm install
npm test
```

## How to write test for a sCrypt contract

The major steps to write a sCrypt test are exemplified by `tests/demo.scrypttest.js`.

1. import / require `scrypttest` libary. 

```
import { buildContractClass } from 'scrypttest';
```


2. Use the imported function `buildContractClass` to get a reflected contract, which has same properties and methods as defined in the specified sCrypt contract.

```
const Demo = buildContractClass(path.join(__dirname, '../contracts/demo.scrypt'));
```

3. Initialize the contract.

```
demo = new Demo(4, 7);
```

4. Write tests for the instantiated contract as you would do in Javascript.

```
expect(demo.unlock(4 + 7)).to.equal(true);
```

## How to run tests locally

### Run using sCrypt Extension
Run unit tests file within the editor/explorer context menu.

![Screenshot](https://raw.githubusercontent.com/wiki/scrypt-sv/boilerplate/extension_screenshots/run_test_demo.gif)

**Note:** The test files must be suffixed by `.scrypttest.js` or `.scrypttest.ts`, otherwise the "Run sCrypt Test" option would not appear in the menu.

### Run from console
Tests could also be run from the console by executing `npm test`, just like regular Javascript/TypeScript tests.

## How to run tests remotely on testnet
When your tests succeed locally, you can run them on testnet.

You'll first need a funded testnet address.

Create a testnet address here: https://bitcoinpaperwallet.com/bitcoinpaperwallet/generate-wallet.html?design=alt-testnet

You can then load that address using this faucet: https://faucet.bitcoincloud.net


1. Provide the private key of your funded testnet address in `tests/runontestnet.js`
```
const key = ''
```
2. `npm run testnet`
```
> scrypt_boilerplate@0.1.0 testnet ~/scrypt_boilerplate
> node tests/runontestnet.js

locking txid:      8d58ff9067f5fa893b5c695179559e108ebf850d0ce4fd1e42bc872417ffd424
unlocking txid:    c60b57e93551a6c52282801130649c6a97edcca5d2b28b8b4ae2afe0ee59bf79
Succeeded on testnet
```


## Understanding What Happens In a Transaction

[THIS MAY CONTAIN INNACURACIES WILL BE PROOFREAD and REFINED LATER and probably moved to a different file...]

In order to understand what sCrypt does you first need to understand the basics of how bitcoin transactions are made.

Each Bitcoin transaction is composed of UTXOs.

A UTXO (unspent transaction output) can be thought of as a one-time-use envelope that contains bitcoin.

Each UTXO is located at an address which itself can posses many UTXOs.

When a Bitcoin wallet wants to make a transaction, it gathers enough UTXOs (or envelopes of Bitcoin) from its one or many addresses to cover the cost of the transaction it wants to make.

So for example, if it wants to send 5 bitcoin, and it posesses 10 UTXOs (or envelopes) containing 2 bitcoin each.

It will gather 3 UTXOs to total 6 Bitcoin to make the transaction.  Notice that in this case it doesn’t have exact change so it will gather more than enough Bitcoin.

It then unlocks those UTXOs (as if it is unsealing the envelope) and then will repackage those Bitcoin into new envelopes metaphorically speaking, in order to make a transaction.


In this example, since it didn’t doesn’t have exact change, and Bitcoin must be packaged in UTXOs it will create two new envelopes for the transaction:

The first with 5 bitcoin addressed to the receiver

…and the second with 1 bitcoin addressed back to itself.


Those two envelopes are then sealed and handed over to the receiver, who then sends them to the miners to be validated.

The miners then check that it is a valid transaction and, if so, those envelopes or UTXOs are added to the public ledger.

The receiver now has 5 more bitcoin under its possession and the transaction is completed.


So how does sCrypt fit in?

Each envelope, or UTXO, is sealed with a locking script. 

and is unsealed with an unlocking script.


The locking script is typically (and was in the example above) p2pkh (sp?).

Meaning it takes it public address of the destination and locks it with that. [need to be explained better here]

So that the owner of that address can then run the unlocking script, using his private key as a parameter, to “unseal the envelope” for a new transaction.


However, the locking script does not have to be a p2pkey.

It can be a custom “smart contract” written to satisfy custom conditions.

These contracts are at a low level, written in ___ language.  That is not human readable.

So sCrypt is an abstraction that lets you write smart contract in human readable form that will then be compiled to this low level language.
