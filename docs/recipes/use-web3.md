---
title: Use Web3
---

You can use web3.js for communications with `leap-node`. See [JSON RPC docs](../json-rpc/overview.md)

Here is an example how to create a Web3 instance:

# Create instance

```es6
import Web3 from 'web3';
import { helpers } from 'leap-core';

const web3 = helpers.extendWeb3(new Web3('https://testnet-node.leapdao.org'));
// Now you can use leap-specific methods
```

# Get registered tokens

```es6
// reads an array of registered erc20 tokens
web3.getColors(false).then(erc20Tokens => {
  console.log(erc20Tokens);
});

// reads array of registered erc721 tokens
web3.getColors(true).then(erc721Tokens => {
  console.log(erc721Tokens);
});

// reads array of registered erc1948/NST tokens
web3.getColors(true).then(erc721Tokens => {
  console.log(erc721Tokens);
});
```

# Get token balance

```es6
// reads balance for the first registred token
web3.getBalance(accountAddr).then(balance => {
  console.log(balance);
});
```

# Getting UTXOs

```js
// All UTXOs of a given color for a given address:
web3.getUnspent(ownerAddress, color).then(utxos => {
  console.log(utxos);
});

// All UTXOs of a given token for a given address:
web3.getUnspent(ownerAddress, tokenAddress).then(utxos => {
  console.log(utxos);
});

// All UTXOs of a given color:
web3.getUnspent(null, color).then(utxos => {
  console.log(utxos);
});

// All UTXOs for a given address (all colors):
web3.getUnspent(ownerAddress).then(utxos => {
  console.log(utxos);
});

// All UTXOs in the network (may be heavy)
web3.getUnspent().then(utxos => {
  console.log(utxos);
});
```
