Input of UTXO transaction

```
import { Input, Outpoint } from 'leap-core';

// Transfer input
const prevTxHash = '0x00';
const prevTxOutputIndex = 0;
new Input(
  new Outpoint(prevTxHash, prevTxOutputIndex)
);


// Deposit input
const depositId = 0;
new Input(depositId);

// Spending condition input
tbd...

```

## Methods