Init Files
---
Create new folder inside `server` and call it `game`. Then inside our newly created `game` folder you need to 
make `masks.js`. We will put the same masks we were using previously in our WordGame contract
```javascript
const ROUND_ID = '1337133713371337133713371337133713371337133713371337133713371337';
const ANSWER = '1234123412341234123412341234123412341234123412341234123412341234';
const TOKEN_ADDR = '1222222222222222222222222222222222222222';
const PLAYER = '1333333333333333333333333333333333333333';
const HOUSE = '1444444444444444444444444444444444444444';

module.exports = {
  ROUND_ID,
  ANSWER,
  TOKEN_ADDR,
  PLAYER,
  HOUSE
};
```

Game Logic
---
Next create `index.js` file. It will hold all our game logic - creating new round, generating and deploying new contract
and supplying front-end with round results. Inside of `index.js` file require necessary modules:
```javascript
// This will be used to generate sets of words for each round
const getWords = require("random-words");

// Crypto modules
const ethers = require("ethers");
const { Tx, Input, Output } = require("leap-core");
const { ripemd160 } = require("ethereumjs-util");

// Compiled artefact and masks for our WordGame contract
const wordGame = require("../build/contracts/WordGame");
const { TOKEN_ADDR, ROUND_ID, PLAYER, HOUSE, ANSWER } = require("./masks");

// Generic and RPC utility methods
const { replaceAll, sliceZero, showLog } = require("../utils/generic");
const rpcClient = require("../rpcClient");
``` 

Round Start
---
Now we will create a new method called **generateNewRound** which will generate set of words, then select one of them. 
Then it will create new spending condition and deploy it using house wallet.
```javascript
const generateNewRound = async (
  { houseWallet, playerAddress, tokenAddress, roundBet = 100000000 }
) => {
  const { utils } = ethers;
  const {
    getTokenColor,
    getTokenContract,
    makeTransfer,
    tokenBalanceChange
  } = rpcClient;

  // House params
  const houseAddress = houseWallet.address;
  const housePrivateKey = houseWallet.privateKey;

  // Token Color
  const tokenColor = await getTokenColor(tokenAddress);
  const tokenContract = await getTokenContract(tokenAddress);
};
```
Next prepare round data
```javascript
// We gonna use timestamp for a nounce
const time = new Date().getTime().toString();
// And store it inside contract in bytes32 form
const roundId = utils.formatBytes32String(time);
```

```javascript
// Generate a set of words for new round
const words = getWords({ exactly: 4, maxLength: 8 });
// Pick one
const pick = Math.floor(Math.random() * 4);
const answer = words[pick];
// Convert it to bytes32 to be stored inside of our contract
const answerBytes32 = utils.formatBytes32String(answer);
```

Now we need to replace our masks with new data we just prepared
```javascript
// Replace contract details using predefined masks
let codeBuffer = wordGame.deployedBytecode;
codeBuffer = replaceAll(codeBuffer, ROUND_ID, sliceZero(roundId));
codeBuffer = replaceAll(codeBuffer, TOKEN_ADDR, sliceZero(tokenAddress));
codeBuffer = replaceAll(codeBuffer, ANSWER, sliceZero(answerBytes32));
codeBuffer = replaceAll(codeBuffer, PLAYER, sliceZero(playerAddress));
codeBuffer = replaceAll(codeBuffer, HOUSE, sliceZero(houseAddress));

// Calculate new address for spending condition
const roundScript = Buffer.from(codeBuffer.replace("0x", ""), "hex");
const roundBuffer = ripemd160(codeBuffer, false);
const roundAddress = `0x${roundBuffer.toString("hex")}`;
```

To deploy contract(spending condition) we need to fund the address/hash we've got from previous step:
```javascript
// Fund round condition
// Please note, that currently funding transaction need to be paid in LEAP
// set any number here we can get proper value here later
const gasFee = 6000000;

// Make transaction to fund gas cost
const gasTransaction = await makeTransfer(
  {
    privateKey: housePrivateKey,
    color: tokenColor,
    from: houseAddress,
    to: roundAddress,
    amount: gasFee
  }
);

// Wait until contract address will get funds and then return
const roundBalance = await tokenBalanceChange({
  contract: tokenContract,
  address: roundAddress,
  prevBalance: 0
});

// Let's return all the info we have about round so we can use it later
return {
  words,
  answer,
  roundId,
  roundAddress,
  roundScript,
  roundBalance,
  codeBuffer
};
```
Finish Round
---
```javascript
const finishRound = async (
  { word, houseWallet, tokenAddress, round, roundBet }
) => {
  const { utils } = ethers;
  const {
    getTokenColor,
    getTokenContract,
    getBalance,
    makeTransfer,
    tokenBalanceChange,
    getUnspentOutputs,
    checkCondition,
    sendRaw
  } = rpcClient;
  
  // Get access to data we got at the end of generateNewRound 
  const { codeBuffer, roundId, roundAddress } = round; 

  // House params
  const houseAddress = houseWallet.address;
  const housePrivateKey = houseWallet.privateKey;

  // Token Color
  const tokenColor = await getTokenColor(tokenAddress);
  const tokenContract = await getTokenContract(tokenAddress);
  const roundBalance = await getBalance(tokenAddress)(roundAddress);
```

At this point of time player will fund round from his side and now it's time to do the same for hous
```javascript

// Make new transaction from houseAddress to roundAddress
const houseTransaction = await makeTransfer(
  {
    privateKey: housePrivateKey,
    color: tokenColor,
    from: houseAddress,
    to: roundAddress,
    amount: roundBet
  }
);

// Wait for balance change 
const newRoundBalance = await tokenBalanceChange({
  contract: tokenContract,
  address: roundAddress,
  prevBalance: roundBalance
});
```

Optionally you can add logging to see that transfer was completed properly
```javascript
showLog({ 
	balance: roundBalance.toString(), 
	newBalance: newRoundBalance.toString()
}, 'Round Balance Change');
```

At this point of time our round address shall have 3 unspent outputs:  
- first one is gas funding
- second one - player bet  
- third one - house bet  

We will create spending conditions out of those 3 
```javascript
const roundScript = Buffer.from(codeBuffer.replace("0x", ""), "hex");
const utxos = await getUnspentOutputs(roundAddress, tokenColor);

// Create inputs from unspent outputs
const inputs = [
  new Input({
    prevout: utxos[0].outpoint,
    script: roundScript
  }),
  new Input({
    prevout: utxos[1].outpoint
  }),
  new Input({
    prevout: utxos[2].outpoint
  })
];
// Outputs can be empty at this point. We will update them after we check
// our spending condition for errors
const outputs = [];
const unlockTransaction = Tx.spendCond(inputs, outputs);

// Add Player Answer
const wordGameABI = new ethers.utils.Interface(wordGame.abi);
const answerBytes32 = ethers.utils.formatBytes32String(word);
const msgData = wordGameABI.functions.roundResult.encode([
  answerBytes32,
  roundId
]);

// Not that we are setting both script and msgData on first input
unlockTransaction.inputs[0].setMsgData(msgData);
unlockTransaction.signAll(housePrivateKey);
```
Next step would be to call checkCondition, cause we can't know which outputs we need to specify until we
actually execute spending condition. 
```javascript
const checkUnlockTransaction = await checkCondition(unlockTransaction);
showLog(checkUnlockTransaction, "Condition Check Result");

// If you set wrong message or mess up your contract code  you will get error
if (!checkUnlockTransaction.outputs) {
  showLog("Spending condition code or message data is wrong", "", "***");
  process.exit(1);
}
// In other case checkUnlockTransaction will have correct outputs
// We will use those to update our spending condition
for (let i = 0; i < checkUnlockTransaction.outputs.length; i++) {
  unlockTransaction.outputs[i] = new Output.fromJSON(
    checkUnlockTransaction.outputs[i]
  );
}
// ...and check it one more time
const checkUnlockingScriptNew = await checkCondition(unlockTransaction);
showLog(checkUnlockingScriptNew, "New Check");
const finalHash = await sendRaw(unlockTransaction);
return { receiptHash: finalHash, roundBalance: newRoundBalance };
};
```

