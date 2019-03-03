---
title: How to use
---

# Using Docker
You can use `docker-compose up` in the root folder of the `leap-node` repository for a convienent setup,
or just `docker run quay.io/leapdao/leap-node` if you want to quickly spin-up a node.

Example commands:
  - `docker run quay.io/leapdao/leap-node --help`
  - `docker run -t -e DEBUG=* -p 1000:1000 quay.io/leapdao/leap-node --rpcport=1000 --config=http://node1.testnet.leapdao.org:8645`
  - `docker run -it --entrypoint sh quay.io/leapdao/leap-node` Overwrite `ENTRYPOINT` to `sh`, creates a container and drops into a shell.
  - `DEBUG='leap*' docker-compose up` (needs docker-compose.yml)
  - `docker-compose run --entrypoint sh leap-node` Overwrite `ENTRYPOINT` to `sh`, creates a container and drops into a shell.
  - `docker-compose exec leap-node sh` Drops into a shell in the running leap-node container.

Example docker-compose.yml file:
```
version: '3'

services:
  leap-node:
    restart: unless-stopped
    build:
      context: .
      dockerfile: Dockerfile
    image: quay.io/leapdao/leap-node:latest
    volumes:
      - leap-node:/root
    ports:
      # rpc
      - "8645:8645"
      # ws
      - "8646:8646"
      # proxy_app
      - "26659:26659"
      # p2p
      - "46691:46691"
    environment:
      - "DEBUG=${DEBUG}"
    stdin_open: true
    tty: true
    deploy:
      replicas: 1
      resources:
        limits:
          memory: 2048M
        reservations:
          memory: 512M

volumes:
  leap-node:
```

# Without Docker
## Prerequisite

- Node.js 8+
- build-essential
- Python 2.X (required by node-gyp, only for building)

## Install

`npm install leap-node -g` or `yarn global add leap-node`

## Run

`leap-node [ARGS] --config=path-to-config.json`

### Available cli arguments

- `no-validators-updates` — disabling validators set updates (default: false)
- `port` — tx endpoint port (default: 3000)
- `rpcaddr` — host for http RPC server (default: localhost)
- `rpcport` — port for http RPC server (default: 8645)
- `wsaddr` — host for websocket RPC server (default: localhost)
- `wsport` — port for websocket RPC server (default: 8646)
- `p2pPort` — port for p2p connection (default: random)
- `config` — path to config file
- `version` — print version of the node

### Config file options

- `bridgeAddr` — leap bridge contract address
- `rootNetwork` — ethereum provider url
- `genesis` — genesis string
- `network` — network id
- `peers` — array of peers

### Config presets

Dev config file: `leap-node --network=testnet-zeta`

Testnet config file: N/A

Mainnet config file: N/A
