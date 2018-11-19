---
title: Use Web3
---

You can use web3.js for communications with `leap-node`. See [JSON RPC docs](../json-rpc/overview.md)

Here is an example how to create a Web3 instance:

```es6
import Web3 from 'web3';
import { helpers } from 'leap-core';

const web3 = helpers.extendWeb3(new Web3('https://testnet-1.leapdao.org'));

// Now you can use leap-specific methods

// reads utxo for all colors
web3.getUnspent(accountAddr).then(utxo => {
  console.log(utxo);
});

// reads an array of registered erc20 tokens
web3.getColors(false).then(erc20Tokens => {
  console.log(erc20Tokens);
});

// reads array of registered erc721 tokens
web3.getColors(true).then(erc721Tokens => {
  console.log(erc721Tokens);
});

// reads balance for the first registred token
web3.getBalance(accountAddr).then(balance => {
  console.log(balance);
});
```
