---
layout: docs-content
title: Compound.js SDK Documentation
permalink: /compound-js/
docs_namespace: compound-js

## Element ID: In-page Heading
sidebar_nav_data:
  compoundjs: Compound.js
  constructor: Constructor
  ethereum-read: Ethereum Read
  ethereum-trx: Ethereum Trx
---

# Compound.js

## Introduction

[Compound.js](https://www.npmjs.com/package/@compound-finance/compound-js) is a JavaScript SDK for Ethereum and the Compound Protocol. It wraps around Ethers.js, which is its only dependency. It is designed for both the web browser and Node.js.

The SDK is currently in open beta. **Use at your own risk.**

For bugs reports and feature requests, either create an issue in the [GitHub repository](https://github.com/compound-finance/compound-js) or send a message in the Development channel of the [Compound Discord](https://compound.finance/discord).

## Constructor

Creates an instance of the Compound.js SDK.

- `[provider]` (Provider \| string) Optional Ethereum network provider. Defaults to Ethers.js fallback mainnet provider.
- `[options]` (object) Optional provider options.
- `RETURN` (object) Returns an instance of the Compound.js SDK.

```js
var compound = new Compound(window.ethereum); // web browser

var compound = new Compound('http://127.0.0.1:8545'); // HTTP provider

var compound = new Compound(); // Uses Ethers.js fallback mainnet (for testing only)

var compound = new Compound('goerli'); // Uses Ethers.js fallback (for testing only)

// Init with private key (server side)
var compound = new Compound('https://mainnet.infura.io/v3/_your_project_id_', {
  privateKey: '0x_your_private_key_', // preferably with environment variable
});

// Init with HD mnemonic (server side)
var compound = new Compound('mainnet' {
  mnemonic: 'clutch captain shoe...', // preferably with environment variable
});
```

## Ethereum Methods

These methods facilitate interactions with the Ethereum blockchain.

## Ethereum Read

This is a generic method for invoking JSON RPC's `eth_call` with Ethers.js. Use this method to execute a smart contract's constant or non-constant member without using gas. This is a read-only method intended to read a value or test a transaction for valid parameters. It does not create a transaction on the block chain.

- `address` (string) The Ethereum address the transaction is directed to.
- `method` (string) The smart contract member in which to invoke.
- `[parameters]` (any[]) Parameters of the method to invoke.
- `[options]` (CallOptions) Options to set for `eth_call`, optional ABI (as JSON object), and Ethers.js method overrides. The ABI can be a string of the single intended method, an array of many methods, or a JSON object of the ABI generated by a Solidity compiler.
- `RETURN` (Promise&lt;any&gt;) Return value of the invoked smart contract member or an error object if the call failed.

```js
const cEthAddress = Compound.util.getAddress(Compound.cETH);

(async function() {

  const srpb = await Compound.eth.read(
    cEthAddress,
    'function supplyRatePerBlock() returns (uint256)',
    // [], // [optional] parameters
    // {}  // [optional] call options, provider, network, plus Ethers.js "overrides"
  );

  console.log('cETH market supply rate per block:', srpb.toString());

})().catch(console.error);
```

## Ethereum Trx

This is a generic method for invoking JSON RPC's `eth_sendTransaction` with Ethers.js. Use this method to create a transaction that invokes a smart contract method. Returns an Ethers.js `TransactionResponse` object.

- `address` (string) The Ethereum address the transaction is directed to.
- `method` (string) The smart contract member in which to invoke.
- `[parameters]` (any[]) Parameters of the method to invoke.
- `[options]` (CallOptions) Options to set for `eth_sendTransaction`, (as JSON object), and Ethers.js method overrides. The ABI can be a string optional ABI of the single intended method, an array of many methods, or a JSON object of the ABI generated by a Solidity compiler.
- `RETURN` (Promise&lt;any&gt;) Returns an Ethers.js `TransactionResponse` object or an error object if the transaction failed.

```js
const oneEthInWei = '1000000000000000000';
const cEthAddress = '0x4ddc2d193948926d02f9b1fe9e1daa0718270ed5';
const provider = window.ethereum;

(async function() {
  console.log('Supplying ETH to the Compound Protocol...');

  // Mint some cETH by supplying ETH to the Compound Protocol
  const trx = await Compound.eth.trx(
    cEthAddress,
    'function mint() payable',
    [],
    {
      provider,
      value: oneEthInWei
    }
  );

  // const result = await trx.wait(1); // JSON object of trx info, once mined

  console.log('Ethers.js transaction object', trx);
})().catch(console.error);
```
