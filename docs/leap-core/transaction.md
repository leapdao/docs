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

## Constructor

```ts
type Options = {
  depositId?: number;
  // service fields. You probably do not need to use them
  slotId?: number;
  tenderKey?: string;
  activationEpoch?: number;
};

constructor(type: Type, inputs: Input[] = [], outputs: Output[] = [], options: Options);
```

## Factories

- `static deposit(depositId: number, value: number, address: string, color: number, data: string): Tx<Type.DEPOSIT>`
- `static exit(input: Input): Tx<Type.EXIT>`
- `static transfer(inputs: Input[], outputs: Output[]): Tx<Type.TRANSFER>`
- `static transferFromUtxos(utxos: Unspent[], from: string, to: string, value: BigInt | number, color: number): Tx<Type.Transfer>` — creates a transfer tx from set of utxos (see [plasma_unspent RPC method](../json-rpc/web3.plasma.md#plasma_unspent))
- `static spendCond(inputs: Input[], outputs: Output[]): Tx<Type.SPEND_COND>`
- `static fromRaw(transaction: Buffer | string): Tx<any>`
- `static fromJSON(json: Object): Tx<any>`

## Methods

- `toJSON(): Object` — serialize tx into json
- `toRaw(): Buffer` — serialize tx into raw buffer (see [Data structures](../data-structures.md))
- `hex(): string` — same as `toRaw` but returns a string
- `hash(): string` — tx hash, transfer tx should be signed first
- `sign(privKeys: string[])` — sign transaction with mutltiple keys — one key per input
- `signAll(priveKey: string)` — sign transaction with one key
- `signWeb3(web3: Web3)` — sign transaction with Web3.js instance (MetaMask for example)

## Helpers

- `calcInputs(utxos: Unspent[], from: string, amount: BigInt | number, color: number): Input[]` — returns an array of inputs required to transfer some amount from given address
- `calcOutputs(utxos: Unspent[], inputs: Input[], from: string, amount: BigInt | number, color: number): Outputs[]` — calculates outputs for given utxos, inputs and amount — transfer output and leftovers back to sender