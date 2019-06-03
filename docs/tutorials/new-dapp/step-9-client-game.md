Game Interaction
---
Here we will cover the process of creating game flow and interaction with server
Create `index.js` file inside `client/src` folder. Then import some modules and define constants
```javascript
// Crypto modules
import Web3 from "web3";
import * as leapCore from "leap-core";
import * as ethers from "ethers";

// RPC class and config
import RPC from "../../universal/rpc";
import { TOKEN_ADDRESS, RPC_URL } from "../../universal/config";

// Utility Methods
import {
  showStep,
  turn,
  clearGameField,
  setGameField,
  hideGameField,
  showGameField,
  activateWallet,
  fetchPost
} from "./utility";

import wallet from "./wallet";
import { erc20Abi, wordGameAbi } from "./abis";

const { helpers, Tx } = leapCore;
export const GAME_SERVER = "http://localhost:3000/game";

// Create instance of RPC client
const plasma = new ethers.providers.JsonRpcProvider(RPC_URL);
const rpcClient = new RPC({ erc20Abi, leapCore, plasma, ethers });

// We also will need extended Web3 to sign transaction with wallet
const plasmaExtended = helpers.extendWeb3(new Web3(RPC_URL));

const {
  getBalance,
  getTokenColor,
  getTokenContract,
  tokenBalanceChange,
  getUnspentOutputs,
  makeTransferWeb3,
  sendRaw,
  getReceipt
} = rpcClient;
const balanceOf = getBalance(TOKEN_ADDRESS);
```

Now let's define method, which will handle round funding by player:
```javascript
const fundRound = async ({ wallet, account, round }) => {
  const { roundAddress } = round;
  
  // We will match value to the same we've specified on server
  const roundBet = 100000000;
  const color = await getTokenColor(TOKEN_ADDRESS);
  
  // Get unspent outputs
  const utxos = await plasmaExtended.getUnspent(account);
  
  // Calculate inputs and outputs
  const inputs = helpers.calcInputs(utxos, account, roundBet, color);
  const outputs = helpers.calcOutputs(
    utxos,
    inputs,
    account,
    roundAddress,
    roundBet,
    color
  );
  // Create new transaction from in/outputs
  const playerTransaction = Tx.transfer(inputs, outputs);
  
  // Sign it with wallet
  const signedTransaction = await playerTransaction.signWeb3(wallet);
  
  // Send to to the chain and return hash
  return await plasmaExtended.eth.sendSignedTransaction(signedTransaction.hex());
};
```
Next will be *main* method, which will be executed, when all content on the page is loaded
```javascript
const main = async () => {
  console.log("Start Application");
  
  // PUT YOUR CODE HERE
  
};

// Wait for content to be loaded
document.addEventListener("DOMContentLoaded", () => {
  main();
});
```
let's add some code to grab DOM elements. Inside the body of **main** function replace commented out line 
with following code:
```javascript
const allStepsDOM = document.querySelectorAll(".step");
const allSteps = [].slice.call(allStepsDOM);

const buttonsDOM = document.querySelectorAll("button");
const buttons = [].slice.call(buttonsDOM);

// Get token contract
const tokenContract = await getTokenContract(TOKEN_ADDRESS);

let account = null;

// Reset view to initial
showStep("intro");
```

Attach event listeners to buttons in every step
```javascript
buttons.forEach(button => {
    button.addEventListener("click", async () => {
      const parent = button.parentElement;
      const currentStep = parent.dataset.step;
      const progress = parent.querySelector(".progress");

      switch (currentStep) {
        case "intro": {
          showStep("connect-wallet");
          break;
        }

        case "connect-wallet": {
          turn("off", button);
          turn("on", progress);
          const accounts = await activateWallet();
          turn("off", progress);
          if (!accounts) {
            turn("on", button);
          } else {
            account = accounts[0]; // take first account
            console.log(account);
            showStep("game-rules");
          }
          break;
        }

        case "game-rules": {
          showStep("game-round");
          break;
        }

        // This is where our main loop is happening
        case "game-round": {
          // Hide buttons and game field
          turn("off", button);
          turn("on", progress);
          clearGameField();
          
					// Call endpoint to generate new round and deploy spending condition
          const endpoint = GAME_SERVER + "/startRound";
          const round = await fetchPost(endpoint, { playerAddress: account });
          const { words, roundAddress } = round;

					// Show round words
          setGameField(words);
          showGameField();
          turn("off", progress);

          const wordsButtonsDOM = document.querySelectorAll(".word-toast");
          const wordsButtons = [].slice.call(wordsButtonsDOM);
          console.log(wordsButtons.length);

					// ADD EVENT LISTENERS HERE
          break;
        }


        case "win":
        case "lose":
          showStep("game-round");
          break;

        default: {
          return;
        }
      }
    });
  });
```

Now we will attach even listeners to word buttons. Replace `// ADD EVENT LISTENERS HERE` with code below:
```javascript
wordsButtons.forEach(wordButton => {
  wordButton.addEventListener("click", async () => {
    // Pick a word from button clicked
    const selectedWord = wordButton.innerHTML;
    
    // Hide prorgress bar and game field
    const progress = parent.querySelector(".progress");
    hideGameField();
    turn("on", progress);
    
    const roundBalance = (await balanceOf(roundAddress)).toString();
    console.log("Round Balance:", roundBalance);
    
    // Fund spending condition with player wallet
    const paymentOptions = { wallet, account, round };
    const fundHash = await fundRound(paymentOptions);
    
    // Wait for balance change on round address
    const newRoundBalance = await tokenBalanceChange(
      {
        contract: tokenContract,
        address: roundAddress,
        prevBalance: roundBalance
      }
    );
    console.log("New Round Balance:", newRoundBalance);

		// Notify server that user paid his part of the bet and request
		// server to unlock round funds
    console.log("Requesting round result for ", roundAddress);
    const endpoint = GAME_SERVER + "/finishRound";
    const { receiptHash } = await fetchPost(endpoint, {
      playerAddress: account,
      word: selectedWord
    });

    const balanceChanged = await tokenBalanceChange({
      address: roundAddress,
      contract: tokenContract
    });
    console.log("Round added to the chain!");

		// Request receipt to discover who was the winner
    const receipt = await getReceipt(receiptHash);
    console.log(receipt);

		// If player is a receiver of the round funds, then he is the winner
    if (receipt.to.toLowerCase() === account.toLowerCase()){
      showStep("win");
    } else{
      showStep("lose");
    }

    // Reset frame
    turn('off', progress);
    turn('on', button);
  });
});
```
> [Full listing of index.js](https://github.com/MaxStalker/leap-word-game/blob/master/client/src/index.js)

Run the game
---
Now start the server by running in the `server` folder: 
```javascript
nodemon start
```
Execute following script in `client` folder:
```javascript
npm start
```

Congratulations!
---
Now you should have dApp working on Leap network.  
We haven't covered cancelRound functionality, since you can do it in different ways. 
But it should be trivial if you simply follow the code we wrote to resolve round.
