General Info
---
We will interact with Leap node via remote procedure call (RPC) protocol. 
You can read in details about it [here.](../../../json-rpc/overview/)  

___
TODO: Add inner links for each method
___

There are several methods we will use in this tutorial:  
- **plasma_getColor** - get integer id of token  
- **plasma_unspent** - get token balance  
- **eth_sendRawTransaction** - send raw transaction to register on chain  
- **eth_getTransactionByHash** - get transaction by hash  
- **eth_getTransactionReceipt** - get transaction receipt  
- **checkSpendingCondition** - check spending condition for errors  

Let's add those as constants to our `universal/config.js` file and update it's module exports
```javascript
// RPC Calls
const GET_COLOR = "plasma_getColor";
const GET_UNSPENT = "plasma_unspent";
const RAW_TX = "eth_sendRawTransaction";
const GET_TX = "eth_getTransactionByHash";
const GET_RECEIPT = "eth_getTransactionReceipt";
const CHECK_CONDITION = "checkSpendingCondition";

module.exports = {
  RPC_URL,
  TOKEN_ADDRESS,
  rpcMessages: {
    GET_COLOR,
    GET_UNSPENT,
    RAW_TX,
    GET_TX,
    GET_RECEIPT,
    CHECK_CONDITION
  }
};
```

RPC Class
---
In order to smooth interaction with RPC let's create utility class `RPC` and implement some methods on it.
Create new file in `universal` folder in root of your project and call it `rpc.js`

```javascript
const { rpcMessages } = require('./config');
const {
  GET_COLOR,
  GET_TX,
  GET_UNSPENT,
  RAW_TX,
  CHECK_CONDITION,
  GET_RECEIPT
} = rpcMessages;

class RPC{
  constructor({erc20Abi, leapCore, ethers, plasma}){
    // Dependency injection
    this.ethers = ethers;
    this.erc20Abi = erc20Abi;
    this.leapCore = leapCore;

    // RPC Provider
    this.plasma = plasma;

    // Caches for colors and contracts
    this.colors = {};
    this.contracts = {};

    // Bind methods to instance
    this.getTokenColor = this.getTokenColor.bind(this);
    this.getTokenContract = this.getTokenContract.bind(this);
    this.getBalance = this.getBalance.bind(this);
    this.getTransaction = this.getTransaction.bind(this);
    this.getUnspentOutputs = this.getUnspentOutputs.bind(this);
    this.makeTransfer = this.makeTransfer.bind(this);
    this.tokenBalanceChange = this.tokenBalanceChange.bind(this);
    this.checkCondition = this.checkCondition.bind(this);
    this.sendRaw = this.sendRaw.bind(this);
    this.getReceipt = this.getReceipt.bind(this);
  }
  
  // PUT IMPLEMENTATION OF METHODS HERE
}
```
Now let's implement those methods.  

**getTokenColor** method will return integer id for token address
```javascript
async getTokenColor(tokenAddress){
  const { plasma } = this;
  const storedColor = this.colors[tokenAddress];
  // Check if value is cached
  if (storedColor) {
    return storedColor;
  }
  const tokenColor = parseInt(await plasma.send(GET_COLOR, [tokenAddress], 16));
  this.colors[tokenAddress] = tokenColor;
  return tokenColor;
};
```

**getTokenContract** will return an instance of contract that we can interact with
```javascript
getTokenContract(tokenAddress){
  const { ethers, erc20Abi, plasma } = this;
  const storedContract = this.contracts[tokenAddress];
  if (storedContract) {
    return storedContract;
  }
  const tokenContract = new ethers.Contract(tokenAddress, erc20Abi, plasma);
  this.contracts[tokenAddress] = tokenContract;
  return tokenContract;
};
```

**getBalance** will return curried function to fetch token balance
```javascript
getBalance(tokenAddress){
  const { plasma } = this;
  const { getTokenContract } =this;
  return async function(address) {
    const contract = getTokenContract(tokenAddress, plasma);
    return await contract.balanceOf(address);
  };
};
```

**getTransaction** will fetch us transaction details, based on transaction hash
```javascript
async getTransaction(txHash){
  return this.plasma.send(GET_TX, [txHash]);
};
```

**getUnspentOutputs** will fetch for unspent outputs and map them to a format that plasma expecting
```javascript
async getUnspentOutputs(from, color){
  const { plasma, leapCore } = this;
  const { Output, Outpoint } = leapCore;
  const raw = await plasma.send(GET_UNSPENT, [from, color]);
  return raw.map(utxo => ({
    output: Output.fromJSON(utxo.output),
    outpoint: Outpoint.fromRaw(utxo.outpoint) // TODO check if we can use JSON
  }));
};
```

**makeTransfer** method will make a transfer using unspent outputs and sign transaction with provided private key
```javascript
async makeTransfer(options){
  const { plasma, leapCore } = this;
  const { getUnspentOutputs } = this;
  const { Tx } = leapCore;
  
  // You will need to provide an object as single argument
  const { from, to, color, amount, privateKey } = options;
  const utxos = await getUnspentOutputs(from, color, plasma);
  const rawTx = Tx.transferFromUtxos(utxos, from, to, amount, color)
    .signAll(privateKey)
    .hex();
  try {
    return await plasma.send(RAW_TX, [rawTx]);
  } catch (e) {
    console.log("Error during send");
    console.log(e.message);
  }
};
```
**tokenBalanceChange** will wait for balance change on the address and return new balance. 
We will use this method to ensure that transaction was added to the chain.
```javascript
async tokenBalanceChange(options){
  const {
    contract,
    address,
    prevBalance,
    showProgress = true,
    maxTries = 15
  } = options;

  let currentBalance;
  let tempBalance;

  if (prevBalance) {
    tempBalance = prevBalance.toString();
    currentBalance = prevBalance;
  } else {
    tempBalance = (await contract.balanceOf(address)).toString();
    currentBalance = tempBalance;
  }

  let i = 0;
  const logger = process && process.stdout ? process.stdout : console.log;
  do {
    i++;
    await new Promise(resolve => setInterval(resolve, 1000));
    currentBalance = (await contract.balanceOf(address)).toString();
    showProgress && logger(`\r   🕐 Waiting for balance change. Seconds passed: ${i}`);
  } while (currentBalance === tempBalance && i < maxTries);

  const formattedBalance = currentBalance.toString();
  showProgress && console.log(`\n   ✅ Balance changed: ${formattedBalance}`);

  return currentBalance;
};
```

Let's add some more proxy methods that will be helpful later
```javascript
  async checkCondition(condition) {
    const { plasma } = this;
    return await plasma.send(CHECK_CONDITION, [condition.hex()]);
  }
  async sendRaw(tx) {
    const { plasma } = this;
    return await plasma.send(RAW_TX, [tx.hex()]);
  }
  async getReceipt(hash) {
    const { plasma } = this;
    return await plasma.send(GET_RECEIPT, [hash]);
  }
```

Don't forget to export our class so it can be references from other modules
```javascript
module.exports = RPC;
```

RPC Client
---
Create new file in the root of `server` folder and call it `rpcClient.js`.  
Require necessary modules first
```javascript
const ethers = require('ethers');
const leapCore = require('leap-core');
const RPC = require('../universal/rpc');
const { RPC_URL, TOKEN_ADDRESS } = require('../universal/config');
```
Remember we've compiled IERC20 together with our contract? Now it's time to use it.  
Add following lines to the file:
```javascript
// Load ERC20 interface
let IERC20;
try {
  IERC20 = require("./build/contracts/IERC20");
} catch (e) {
  console.error(`Please run "truffle compile" first`);
  return;
}
const erc20Abi = IERC20.abi;

// We call JSON RPC provider plasma to distinguish it from other web3 providers
const plasma = new ethers.providers.JsonRpcProvider(RPC_URL);

// Now create instance of RPC client
const rpcClient = new RPC({ plasma, erc20Abi, ethers, leapCore});

// And export it for later use
module.exports = rpcClient;
```

Integration test
---
Now let's try this in real life.
Create new folder with name `integration` inside `server/test`.   
Make new file `getBalance.js` inside of it.

```javascript
const rpcClient = require('../../rpcClient');
const { TOKEN_ADDRESS } = require('../../../universal/config');

const main = async ()=>{
  // getTokenColor is one of the generic calls that should work
  const tokenColor = await rpcClient.getTokenColor(TOKEN_ADDRESS);
  
  // Create getter method for balance
  const balanceOf = await rpcClient.getBalance(TOKEN_ADDRESS);
  
  // Let's use some random address from https://staging.leapdao.org/explorer
  const balance = await balanceOf('0x4436373705394267350db2c06613990d34621d69');
  
  // Now let's output data we've got
  console.log({tokenColor, balance: balance.toString()});
};

main();
```

And try to run it:
```bash
node test/integration/getBalance.js
```

Output should be similar to this:
```json
{ tokenColor: 0, balance: '100000000000000' }
```

Our RPC class works as intended, let's start implementing our game.
