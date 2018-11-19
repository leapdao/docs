---
title: Send transfer transaction
---

1. [Create a web3 instance](use-web3.md)

        import Web3 from 'web3';
        import { Tx, helpers } from 'leap-core';

        const web3 = helpers.extendWeb3(new Web3('https://testnet-1.leapdao.org'));

2. Read UTXOs list

        const utxo = await web3.getUnspent(accountAddr);

3. Use `leap-core` helpers to build inputs

        const inputs = helpers.calcInputs(
          utxo,
          accountAddr,
          amount, // amount to send
          color // your token color
        );

4. Use `leap-core` helpers to build outputs

        const outputs = helpers.calcOutputs(
          utxo,
          inputs,
          accountAddr,
          to, // destination address
          amount, // amount to send
          color // your token color
        );

5. Build transaction object

        const tx = Tx.transfer(inputs, outputs);

6. Sign transaction. `leap-core` supports two methods: `sign(privKeys: string[])` and `signWeb3(web3: Web3)`

    - `sign(privKeys: string[])` expects array of private keys as an argument. Length of array should be equal to length of inputs. Also, you can use `signAll` in case in need one key for all inputs

            tx.signAll(privateKey);

    - `signWeb3(web3: Web3)` expects web3 instance (MetaMask for example) as an argument

            await tx.signAll(privateKey);

7. Send transaction

        await web3.eth.sendSignedTransaction(tx.toRaw())