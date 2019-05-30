Test Game Flow
---
Go to `server/test/integration` folder and add new file `mnemonics.js` to it.
> *Avoid adding `mnemonics.js` file to your repository as it might contain crucial information about one of your wallets*
```javascript
const config = require("../../config");
const { WALLET_MNEMONIC } = config;

const PLAYER_MNEMONIC = "== PUT GENERATED MNEMONIC HERE ==";

module.exports = {
  HOUSE_MNEMONIC: WALLET_MNEMONIC,
  PLAYER_MNEMONIC
};
```

Now run `node tools/generateWallet.js` and after execution of script is finished, paste clipboard contents inside of quotes on **line 4** of `mnemonics.js` file.

Then create another file `gameMock.js`
Import necessary modules:
```javascript
// Ethers will be used to control wallets
const ethers = require("ethers");

// Import wallet mnemonics that we created earlier
const { HOUSE_MNEMONIC, PLAYER_MNEMONIC } = require('./mnemonics');

// Import RPC client
const rpcClient = require("../../rpcClient");
const { TOKEN_ADDRESS } = require('../../../universal/config');

// Utility
const { showLog } = require("../../utils/generic");
const requestFaucet = require('../../utils/faucet');

// Game logic
const { generateNewRound, finishRound } = require('../../game');
```

Define helper method that will check house and player balances and request faucet if any of them have zero balance.
It will expect **balanceOf** method and addresses of player and house: 
```javascript
async function checkBalances(balanceOf, { playerAddress, houseAddress }) {
  const initialHouseBalance = await balanceOf(houseAddress);
  const initialPlayerBalance = await balanceOf(playerAddress);
  showLog({ playerAddress, houseAddress }, "Addresses");
  showLog(
    { house: initialHouseBalance, player: initialPlayerBalance },
    "Initial Balances"
  );

  if (parseInt(initialPlayerBalance) === 0) {
    console.log("Player Balance is zero, requesting faucet...");
    const faucetResponse = await requestFaucet(playerAddress, 0);
    showLog({ faucetResponse }, "Player Faucet");
  }

  if (parseInt(initialHouseBalance) === 0) {
    console.log("House Balance is zero, requesting faucet...");
    const faucetResponse = await requestFaucet(houseAddress, 0);
    showLog({ faucetResponse }, "Player Faucet");
  }
}
```

Let's implement main function now:
```javascript
async function main() {
  const { getTokenContract, getTokenColor, getBalance } = rpcClient;
  const tokenContract = await getTokenContract(TOKEN_ADDRESS);
  const tokenColor = await getTokenColor(TOKEN_ADDRESS);

  // Get carried function to check token balance
  const balanceOf = getBalance(TOKEN_ADDRESS);

  // Restore house wallet from mnemonic and get private key and address
  const houseWallet = new ethers.Wallet.fromMnemonic(HOUSE_MNEMONIC);
  const houseAddress = houseWallet.address;
  const housePrivateKey = houseWallet.privateKey;

  // Restore player wallet from mnemonic and get private key and address
  const playerWallet = new ethers.Wallet.fromMnemonic(PLAYER_MNEMONIC);
  const playerAddress = playerWallet.address;
  const playerPrivateKey = playerWallet.privateKey;

	// Now let's check if any of them have zero balance and request faucet in that case
  await checkBalances(balanceOf, { playerAddress, houseAddress });

  process.exit(0);
}

main();
```

Now generate new round data. Add following to line 58 (before process.exit(0))
```javascript
const roundBet = 100000000;
const round = await generateNewRound({
  houseWallet,
  playerAddress,
  roundBet,
  tokenAddress: TOKEN_ADDRESS
});
const { roundScript, codeBuffer, ...roundData } = round;
const { answer, roundId, roundAddress, roundBalance } = roundData;
showLog(roundData, "Round generated");
```

You can try to run our game mock to ensure that everything is working so far
```bash
$ node test/integration/gameMock.js
```
After code finish it's execution you will see three blocks **Addresses**, **Initial Balances** and **Round Generated** 
with corresponding data inside of them. Something similar to this:
```text
---------------

ADDRESSES

{ playerAddress: '0xF9f6607C019CcE7222815C04C303099527FF4A38',
  houseAddress: '0xaC39b311DCEb2A4b2f5d8461c1cdaF756F4F7Ae9' }

---------------


---------------

INITIAL BALANCES

{ house: BigNumber { _hex: '0x060eafdc2b0f4e' },
  player: BigNumber { _hex: '0x9610ccd2a500' } }

---------------

   🕐 Waiting for balance change. Seconds passed: 2
   ✅ Balance changed: 5890000

---------------

ROUND GENERATED

{ words: [ 'darkness', 'person', 'occur', 'simple' ],
  answer: 'darkness',
  roundId:
   '0x3135353932303833373731353200000000000000000000000000000000000000',
  roundAddress: '0x615c3ae02381421c8bc3715def3bebfcc1cd8e37',
  roundBalance: '5890000' }

---------------
```


```javascript
const { makeTransfer, tokenBalanceChange } = rpcClient;
const playerTransfer = await makeTransfer(
  {
    from: playerAddress,
    to: roundAddress,
    color: tokenColor,
    amount: roundBet,
    privateKey: playerPrivateKey
  }
);

const roundBalanceAfterPlayer = await tokenBalanceChange({
  contract: tokenContract,
  address: roundAddress
});

showLog({
  roundBalance,
  roundBalanceAfterPlayer
},'Round Balance Change');
```

Launch the script again to see if betting is working properly
```bash
$ node test/integration/gameMock.js
```

In the end of the output you shall see the following, indicating that roundAddress got both of the bets
```text

---------------

ROUND BALANCE CHANGE

{ roundBalance: '6000000',
  roundBalanceAfterPlayer: '105890000'
  }

---------------

```

Now finish the round by adding the code to **line 103** and executing `node test/integration/gameMock.js` one more time:
```javascript
const roundEnd = await finishRound({
    word: answer,
    houseWallet,
    tokenAddress: TOKEN_ADDRESS,
    round,
    roundBet
  });

  showLog({ roundEnd }, "Receipt");

  // Let's wait for tokenBalance change before we fetch receipt
  const newBalance = await tokenBalanceChange({
    contract: tokenContract,
    address: roundAddress
  });

  const { getReceipt } = rpcClient;
  const { receiptHash } = roundEnd;
  const receipt = await getReceipt(receiptHash);
  
  // We can check "to" field of the receipt. If it's equal playerAddress then
  // we know that player won this round, otherwise - house
  const roundWinner =
    receipt.to.toLowerCase() === playerAddress.toLowerCase()
      ? "Player"
      : "House";
  
  console.log(`Winner is ${roundWinner}`);
```

This will conclude integration test.  
In the next chapter we will create new route with 2 endpoints.
