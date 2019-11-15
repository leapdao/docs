## Set up a node

To connect to the network with your own node, run through the followings steps.

**Prerequisite:**

- Node.js 8+
- build-essential
- Python 2.X (required by node-gyp, only for building)
- A fully synced [Rinkeby Light node](https://www.rinkeby.io/#geth)

**1. Install**

`npm install leap-node -g` or `yarn global add leap-node`


**2. Configure and Start**

Create a file of the following config, named: `testnet.json`
```
{
  "rootNetwork": "http://localhost:8545",
  "exitHandlerAddr": "0x26a937302cc6A0A7334B210de06136C8C61BA885",
  "bridgeAddr": "0xC449D4CD1dEc611d8cA5Fd8167Bf46d6e6d345b9",
  "operatorAddr": "0xb3356900d56F39c79Bfdc2b625d15B1b5b9262a9",
  "rootNetworkId": 4,
  "network": "leap-testnet-theta",
  "networkId": 218508104,
  "eventsDelay": 2,
  "bridgeDelay": 6,
  "version": "v5.3.3",
  "genesis": {
    "genesis_time": "2019-05-23T12:23:22.546083464Z",
    "chain_id": "test-chain-H5Ijd9",
    "consensus_params": {
      "block": {
        "max_bytes": "22020096",
        "max_gas": "-1",
        "time_iota_ms": "1000"
      },
      "evidence": {
        "max_age": "100000"
      },
      "validator": {
        "pub_key_types": [
          "ed25519"
        ]
      }
    },
    "validators": [
      {
        "address": "4881A447F30F484F046859530083FD27C5F7A70B",
        "pub_key": {
          "type": "tendermint/PubKeyEd25519",
          "value": "Su7amMRUBc+MinqO026cuI3Q4jinwx0KJRdjXDp4Rao="
        },
        "power": "10",
        "name": ""
      }
    ],
    "app_hash": ""
  },
  "peers": [
    "cc30db6d116ca6ff440277c8e13e3ca253a774e7@sen1.testnet.leapdao.org:46691",
    "23d57231b620575c03f0e136fc37b5ec5812bb35@val.testnet.leapdao.org:46691",
    "deb472911a7a0ff3867b39c6ffa375ddbb42db5d@sen2.testnet.leapdao.org:46691",
    "5d27949fb42efc829bdc663f914fe0c069a96f2d@54.72.111.55:46691"
  ],
  "p2pPort": 46691,
  "nodeId": "cc30db6d116ca6ff440277c8e13e3ca253a774e7",
  "flagHeights": {
    "spend_cond_stricter_rules": 100000,
    "spend_cond_new_bytecode": 41000
  }
}
```

Use `config.json` as parameter in the following command:

`DEBUG=tendermint,leap-node* leap-node --config=config.json`

**3. Sync the Network**

Let node run and download all the blocks till the current tip of the chain. You can read the current block height from the [testnet block explorer](https://testnet.leapdao.org/explorer).

## Becoming a Validator

If a node launches for the first time, it automatically generates a validator key-pair. Once the node syncs with the tip of the chain successfully, it will check the validators slots, and compare the registered addresses with its own. If the node is not an active validator, it will display the `validator address` and `validator ID` as follows:

![Validator Console](/img/validatorTerminal.png "values to copy from console")


**1. Fund your validator address with Ether**

To be able to propose periods and receive rewards, validators need to submit transactions to the root network (Ethereum). In order to be able to pay the gas for period proposals, fund the `validator address` with Rinkeby Ether:

![Address on Validator Console](/img/validatorTerm.png "address to copy from console")

If you do not have Rinkeby Ether, you can try the following faucets to receive some:

- [rinkeby.io](https://www.rinkeby.io/#faucet)
- [faucet.metamask.io](https://faucet.metamask.io/)


**2. Apply as a validator**

You need to provide us your `validator address` and `validator ID` to get assigned to a validator slot. We also need your email address to inform you about the start of your validator.


[Fill the registration form](https://docs.google.com/forms/d/e/1FAIpQLSdQtc5LoEyWkc5-86SOLW3xK8cRNwuByC7SIrA9MdWeAiuBZw/viewform) by copying the following data:

```
Validator address: 0x...
Validator ID: 0x...
```

**3. Let the node run and join our Slack community**

[Join our Slack community](http://join.leapdao.org/) to get in touch with us and stay up to date about new releases & our validator launch on the mainnet.

Check out our `#validator` channel on Slack.
