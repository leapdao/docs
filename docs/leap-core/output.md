Transaction output

```
import { Output } from 'leap-core';

const value = 100;
const address = '0x4436373705394267350Db2C06613990D34621d69'; // recipient address
const color = 0; // LEAP token
const data = '0x000'; // for spending conditions, optional

new Output(value, address, color, data);

// or pass an object

new Output({
  value,
  address,
  color,
  data,
});
```

## Methods

