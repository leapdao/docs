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

Let node run and download all the blocks till the current tip of the chain. You can read the current blockheight from the [testnet block explorer](https://testnet.leapdao.org/explorer).

## Becoming a Validator

To become a validator, walk through the following steps:

**1. Copy**
Validator address: 0x...
Validator ID: 0x...

**2. Fund the address**

fund the address with Ether

**3. Request Validator Slot**

Submit data to this form:
[Google Form](https://docs.google.com/forms/d/e/1FAIpQLSdQtc5LoEyWkc5-86SOLW3xK8cRNwuByC7SIrA9MdWeAiuBZw/viewform)

Let node run and wait for confirmation from LeapDAO. In the meantime you can join our #validator channel on Slack (http://join.leapdao.org/)