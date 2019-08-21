Leap transaction

```
import { Tx, Outpoint, Input, Output } from 'leap-core';

const prevTxHash = '0x00';
const prevTxOutputIndex = 0;
const depositId = 0;

const inputs = [
  // Transfer input
  new Input(
    new Outpoint(prevTxHash, prevTxOutputIndex)
  ),
  // Deposit input
  new Input(depositId)
];

const value = 100;
const address = '0x4436373705394267350Db2C06613990D34621d69'; // recipient address
const color = 0; // LEAP token
const outputs = [
  new Output(value, address, color),
];

const transfer = Tx.transfer(inputs, outputs);
```

## Methods