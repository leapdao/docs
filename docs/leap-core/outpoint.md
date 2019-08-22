Reference to transaction output

```
import { Outpoint } from 'leap-core';

const prevTxHash = '0x00';
const prevTxOutputIndex = 0;
new Outpoint(prevTxHash, prevTxOutputIndex);
```

## Methods