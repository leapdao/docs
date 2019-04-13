---
title: Connect to the testnet
---

1. Install `leap-node` package: `npm install leap-node -g`
2. Connect to testnet: `leap-node --config=http://node1.testnet.leapdao.org:8645`
3. (Optional) become a validator

## How to become a validator

1. Get some LEAP on the [faucet](https://testnet.leapdao.org/faucet).
2. Go to [testnet.leapdao.org](https://testnet.leapdao.org/stake).
3. Grab a slot. You will see your `validator address` and `validator id` in console after the node start.
4. Fill out the [validator registration form.](http://validator.leapdao.org)
5. Make sure your node is running robustly:
* It should be online 24/7.
* It should have tendermint P2P port reachable by other nodes.
* It doesn’t expose tendermint RPC port to public internet.
* Ideally you will need to have additional failover node with the same validator key running in readonly mode. So you can make updates to the machine and software without downtime. For more information, [see here.](https://github.com/leapdao/leap-node/issues/185#issuecomment-468921734)
6. Locate you validator keys. These are two: Ethereum address and tendermint key. Look in the logs or in the node files to find them.
7. Make sure you validator Ethereum address has some ether on it. It will be needed to submit periods to the root chain.
8. LeapDAO might ask you to provide an output of some tendermint RPC call, so we can make sure your node is well connected to other peers and synced up with the network.
9. Share you validator keys and the node key (`cat ~/.lotion/networks/<network name>/config/node_key.json` or grep logs for `ID_` string) with LeapDAO.

## How to register your token

1. Go to [testnet.leapdao.org/registerToken](https://testnet.leapdao.org/registerToken).
2. Put your token contract address.
3. Submit Form.

## Useful links

[Block explorer](https://testnet.leapdao.org/explorer)

[Plasma Wallet](https://testnet.leapdao.org/wallet)

[Network status](https://testnet.leapdao.org/status)
