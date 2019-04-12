---
title: "Leap web3 extension"
---

# plasma_unspent

Returns the list of UTXOs for a given address.

**Parameters**

`Address` - 20 Bytes - address to get UTXOs for.

```js
params: ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"]
```

**Returns**

- `Array` - Array of `Object`
  - `outpoint`: `String` - Hex-encoded binary of the outpoint
  - `output`: `Object` - Output Object
    - `address`: `Address` - 20 bytes - address of the output
    - `value`: `Quantity` - value of the output in PSC cents

**Example**

Request

```js
{
  "method": "plasma_unspent",
  "params": ["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],
  "id": 1,
  "jsonrpc": "2.0"
}
```

```bash
curl --data '{"method":"plasma_unspent","params":["0x407d73d8a49eeb85d32cf465507dd71d507100c1"],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8645
```

Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": [
    {
      "outpoint": "0x777777777777777777777777777777777777777777777777777777777777777700",
      "output": {
        "address": "0x853f43d8a49eeb85d32cf465507dd71d507100c1",
        "value": 8000000000,
        "color", 0
      }
    }
  ]
}
```

# plasma_getColor

Returns the color for a given token address. If the token is not registered on the network, throws `INVALID_PARAMS` exception.

**Parameters**

- `Address` - 20 Bytes - address of the token contract.

**Returns**

- `value`: `Number` - token color number in hex

**Example**

Request

```js
{
  "method": "plasma_getColor",
  "params": ["0x25e70D10AE0E481975aD8fA30f4e67653c444A05"],
  "id": 1,
  "jsonrpc": "2.0"
}
```

Response
```js
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "0x1"
}
```

# plasma_getColors

Returns tokens registered in the network. To get both ERC20 and NFT tokens seeparate calls will be needed.

**Parameters**

- `Boolean` - if true, returns registered NFT tokens, otherwise — registered ERC20 tokens.

**Returns**

- `Array` - Array of `Address`

**Example**

Request

```js
{
  "method": "plasma_getColors",
  "params": [false],
  "id": 1,
  "jsonrpc": "2.0"
}
```

Response
```js
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": [
        "0x258daf43d711831b8fd59137f42030784293e9e6",
        "0x25e70d10ae0e481975ad8fa30f4e67653c444a05"
    ]
}
```

# plasma_status

Returns network node status.

**Parameters**

no parameters

**Returns**

- `String` - network status. Possible values:
  - `ok` - node is fully sync and operating
  - `catching-up` - node is catching up with the Leap network.
  - `waiting-for-period` - node is waiting for a new period to appear on the root network.

**Example**

Request

```js
{
  "method": "plasma_status",
  "params": [],
  "id": 1,
  "jsonrpc": "2.0"
}
```

Response
```js
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": "ok"
}
```

# plasma_getConfig

Returns the current node configuration.

**Parameters**

no parameters

**Returns**

- `Object`
  - `bridgeAddr`: `Address` - address of the bridge contract on the root network
  - `rootNetwork`: `URL` - root network provider
  - `network`: `String` - Leap network name
  - `genesis`: `Object` - Genesis object from Tendermint
  - `peers`: `Array` - list of connected peers (in Tendermint notation)

**Example**

Request

```js
{
  "method": "plasma_getConfig",
  "params": [],
  "id": 1,
  "jsonrpc": "2.0"
}
```

Response
```js
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "bridgeAddr": "0x2ac21a06346f075cfa4c59779f85830356ea64f3",
        "rootNetwork": "https://rinkeby.infura.io",
        "network": "leapdao-testnet-zeta",
        "genesis": {
            "app_hash": "2436cd126aca5c6ef46b17a041de5b935396224ce9fc2b9cc1f1a60bf52e317e",
            "chain_id": "beta",
            "consensus_params": {
                "block_gossip_params": {
                    "block_part_size_bytes": "65536"
                },
                "block_size_params": {
                    "max_bytes": "22020096",
                    "max_gas": "-1",
                    "max_txs": "10000"
                },
                "evidence_params": {
                    "max_age": "100000"
                },
                "tx_size_params": {
                    "max_bytes": "10240",
                    "max_gas": "-1"
                }
            },
            "genesis_time": "2018-07-16T10:08:17.520479952Z",
            "validators": [
                {
                    "name": "",
                    "power": "10",
                    "pub_key": {
                        "type": "tendermint/PubKeyEd25519",
                        "value": "dkDWnZ7bIVksvfTMSZVupT5ZZW/C2LvRrj9Ce/Z9R/o="
                    }
                }
            ]
        },
        "peers": [
            "bdbd02c690d2b80216ff85946fb0f83945c4f0f2@node1.testnet.leapdao.org:46691"
        ]
    }
}
```

# plasma_getUnsignedTransferTx

Return a tranfer transaction without signature.

**Parameters**

- `from` - 20 Bytes - address from which token will be transfered.
- `to` - 20 Bytes - address token will be transfered to.
- `color` - `Number` - token id.
- `value` - `Number` - number of token to be transfered

```js
params: ["0xb8205608d54cb81f44f263be086027d8610f3c94", "0xd56f7dfcd2baffbc1d885f0266b21c7f2912020c", 1, 260]
```

**Returns**

- `Transaction` - generated transfer transaction without signature

**Example**

Request
```js
{
  "method": "plasma_getUnsignedTransferTx",
  "params": ["0xb8205608d54cb81f44f263be086027d8610f3c94", "0xd56f7dfcd2baffbc1d885f0266b21c7f2912020c", 1, 260],
  "id": 1,
  "jsonrpc": "2.0"
}
```
```bash
curl --data '{"method":"plasma_getUnsignedTransferTx","params":["0xb8205608d54cb81f44f263be086027d8610f3c94", "0xd56f7dfcd2baffbc1d885f0266b21c7f2912020c", 1, 260],"id":1,"jsonrpc":"2.0"}' -H "Content-Type: application/json" -X POST localhost:8645
```
Response
```js
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": { type: 3,
        hash:
         '0xb31b4b333120c4d844c1b5bee0a74b4661f542120a067b756edeb4ebea23b80c',
        inputs:
         [ { hash:
              '0xdcef54659fb1fbaac37f8ff76b9d66fe197c6b67351ecf4d9fcfd2321f1dd1d6',
             index: 2 },
           { hash:
              '0xdcef54659fb1fbaac37f8ff76b9d66fe197c6b67351ecf4d9fcfd2321f1dd1d6',
             index: 1 }
         ],
        outputs:
         [ { address: '0xd56f7dfcd2baffbc1d885f0266b21c7f2912020c',
             value: '260',
             color: 1 },
           { address: '0xb8205608d54cb81f44f263be086027d8610f3c94',
             value: '40',
             color: 1 }
         ]
    }

}
```