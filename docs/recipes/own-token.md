---
title: Use your own token
---

1. Go to [bridge-dev.leapdao.org/registerToken](https://bridge-dev.leapdao.org/registerToken/) and register your token address. After registration, you can use your token for deposits and transfers

2. Read your token color (using [extended web3](use-web3.md))

        const color = await web3.getColor(tokenAddr);

3. Read balance

        const contract = new web3.eth.Contract(erc20ABI, tokenAddr);
        const balance = await contract.methods.getBalance(accountAddr).call();

4. [Send transfers](send-transfer.md)

5. You can see your token transactions in a block explorer using the token address. Example: [/explorer/address/0x258DaF43D711831b8FD59137F42030784293e9E6](https://bridge-dev.leapdao.org/explorer/address/0x258DaF43D711831b8FD59137F42030784293e9E6)
