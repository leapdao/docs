---
title: Use Ethers.js
---

You can use Ethers.js to communicate with Leap Network.

You will need LeapProvider. LeapProvider is extending JsonRpcProvider from Ether.js, so you can use it everywhere in your ethers code like a normal provider.

# Creating provider

```js
const LeapProvider = require('leap-provider');
const leapProvider = new LeapProvider('https://testnet-node.leapdao.org');
```

# Create a Wallet or connect to existing one

This will allow you to send transactions:

```js
const LeapProvider = require('leap-provider');
const leapProvider = new LeapProvider('https://testnet-node.leapdao.org');

 // can be passed in a Wallet
const leapWallet = new ethers.Wallet(privKey, leapProvider);

 // or connected to existing wallet
const otherLeapWallet = Wallet.createRandom();
otherLeapWallet.connect(leapProvider);
```

Once you have an instance of `leapWallet` you are free to use it as a normal Ether.js Wallet. Below are just a few examples.

# Send transaction to Leap Network

```js

 // create some LeapTransaction
const tx = Tx.transfer(
  ...
);

 // tx should be signed
tx.signAll(wallet.privateKey);

 // send via provider
const resp = await leapWallet.provider.sendTransaction(tx);

 // wait for inclusion, you will get a proper TransactionReceipt once tx is included in a block
const receipt = await resp.wait();
console.log(receipt);
```

# Get LEAP balance

```js
const balance = await leapProvider.getBalance(wallet.address).then(res => Number(res));
```

# Get token balance

```js
const token = new ethers.Contract(tokenAddr, erc20abi, leapProvider);
const balance = await token.balanceOf(someAddr);
```

# Getting UTXOS

```js
// All UTXOs of a given color for a given address:
await leapProvider.getUnspent(ownerAddress, color);

// All UTXOs of a given token for a given address:
await leapProvider.getUnspent(ownerAddress, tokenAddress);

// All UTXOs of a given color:
await leapProvider.getUnspent(null, color);

// All UTXOs for a given address (all colors):
await leapProvider.getUnspent(ownerAddress);

// All UTXOs in the network (may be heavy)
await leapProvider.getUnspent();
```
