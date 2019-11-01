---
title: Plasma Bridge
---


Normally, before you can transact on a Leap Network you would need to deposit your root chain tokens to the plasma chain. To get back your tokens on a root chain you need to start an exit process. Both processes are facilitated by a set of root chain contracts which we call Plasma Bridge. These contracts are the cornerstone of Plasma framework security as they reside on the root chain (e.g. Ethereum Mainnet) and in general cannot be tampered with by potentially malicious operator.

## Finding the right ExitHandler contract

For deposits and exits you will be interacting with ExitHandler contract. You can find its address in the [network config](https://docs.leapdao.org/json-rpc/web3.plasma/#plasma_getconfig).

[ExitHandler ABI](https://github.com/leapdao/leap-node/blob/master/src/abis/exitHandler.js). Make sure you use the correct version of the ABI, check the corresponding version in the [network config](https://docs.leapdao.org/json-rpc/web3.plasma/#plasma_getconfig).

## Deposit

Technically, a deposit is done by locking root chain tokens in a Plasma Bridge contract. Once your tokens are in a Plasma Bridge, Leap Network node will create a Deposit transaction which will "mint" you a corresponding amount of the same tokens on plasma.

To deposit tokens you need:

1. Make sure the token you want to deposit is [registered](own-token.md) on the network
2. Approve the required amount of token to be pulled from your account by ExitHandler contract. That's a normal ERC20's `approve` transaction.
3. Call `depositBySender` function of the ExitHandler contract.
4. In a few root chain blocks you deposit will be available on Plasma

## Exit

To exit your tokens back to the root chain you need to call `startExit` function of the ExitHandler contract.

The call is tricky. You will need to supply:

- an inclusion proof for the youngest input of the exiting transaction
- an inclusion proof the exiting transaction itself
- an exit stake (0.1 ETH as of now)

Once succeed, your exit is registered. You will get your tokens on the root chain in a 7 days as per normal Plasma procedure.

⚠️IMPORTANT: make sure your dapp is not using exited UTXO after you've submitted your exit. There is a certain delay between the on-chain `startExit` transaction and Exit transaction on plasma. If you spend exited transaction before Exit tx on plasma is created, your exit becomes invalid and may be challenged by anyone.
