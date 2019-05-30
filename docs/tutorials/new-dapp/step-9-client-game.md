Create ABIs
---
Inside `client/src` create new folder `abis` and make 3 files inside of it `erc20.js`, `wordGame.js` and `index.js`
You can grab contents for `erc20.js` and `wordGame.js` from JSON artefacts in `server/build/contracts` folder 
(look for corresponding file and copy value of "abi"field) or copy-paste from here:  
```javascript
const erc20Abi = [
  {
    "anonymous": false,
    "inputs": [
      {
        "indexed": true,
        "name": "from",
        "type": "address"
      },
      {
        "indexed": true,
        "name": "to",
        "type": "address"
      },
      {
        "indexed": false,
        "name": "value",
        "type": "uint256"
      }
    ],
    "name": "Transfer",
    "type": "event"
  },
  {
    "anonymous": false,
    "inputs": [
      {
        "indexed": true,
        "name": "owner",
        "type": "address"
      },
      {
        "indexed": true,
        "name": "spender",
        "type": "address"
      },
      {
        "indexed": false,
        "name": "value",
        "type": "uint256"
      }
    ],
    "name": "Approval",
    "type": "event"
  },
  {
    "constant": true,
    "inputs": [],
    "name": "totalSupply",
    "outputs": [
      {
        "name": "",
        "type": "uint256"
      }
    ],
    "payable": false,
    "stateMutability": "view",
    "type": "function"
  },
  {
    "constant": true,
    "inputs": [
      {
        "name": "account",
        "type": "address"
      }
    ],
    "name": "balanceOf",
    "outputs": [
      {
        "name": "",
        "type": "uint256"
      }
    ],
    "payable": false,
    "stateMutability": "view",
    "type": "function"
  },
  {
    "constant": false,
    "inputs": [
      {
        "name": "recipient",
        "type": "address"
      },
      {
        "name": "amount",
        "type": "uint256"
      }
    ],
    "name": "transfer",
    "outputs": [
      {
        "name": "",
        "type": "bool"
      }
    ],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "constant": true,
    "inputs": [
      {
        "name": "owner",
        "type": "address"
      },
      {
        "name": "spender",
        "type": "address"
      }
    ],
    "name": "allowance",
    "outputs": [
      {
        "name": "",
        "type": "uint256"
      }
    ],
    "payable": false,
    "stateMutability": "view",
    "type": "function"
  },
  {
    "constant": false,
    "inputs": [
      {
        "name": "spender",
        "type": "address"
      },
      {
        "name": "amount",
        "type": "uint256"
      }
    ],
    "name": "approve",
    "outputs": [
      {
        "name": "",
        "type": "bool"
      }
    ],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "constant": false,
    "inputs": [
      {
        "name": "sender",
        "type": "address"
      },
      {
        "name": "recipient",
        "type": "address"
      },
      {
        "name": "amount",
        "type": "uint256"
      }
    ],
    "name": "transferFrom",
    "outputs": [
      {
        "name": "",
        "type": "bool"
      }
    ],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
  }
]
export default erc20Abi;
```
```javascript
const wordGameAbi = [
  {
    "constant": false,
    "inputs": [
      {
        "name": "_playerAnswer",
        "type": "bytes32"
      },
      {
        "name": "_roundId",
        "type": "bytes32"
      }
    ],
    "name": "roundResult",
    "outputs": [],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "constant": false,
    "inputs": [],
    "name": "cancelRound",
    "outputs": [],
    "payable": false,
    "stateMutability": "nonpayable",
    "type": "function"
  }
];
export default wordGameAbi;
```
Now export those from `index.js`
```javascript
export { default as erc20Abi } from "./erc20";
export { default as wordGameAbi } from "./wordGame";
```

Create Wallet instance
---
Add new file `wallet.js` inside `client/src`. We will export instance of web3 wallet
```javascript
import Web3 from "web3";
const wallet = window.ethereum ? new Web3(window.ethereum) : null;

export default wallet;
```

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
  // Here we will continue implementing our logic
  };

// Wait for content to be loaded
document.addEventListener("DOMContentLoaded", () => {
  main();
});
```
After that 'Start Application' let's add some code to grab DOM elements
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

Congratulations!
---
Now you should have a working dApp working on Leap network.  
We haven't covered cancelRound functionality, since you can do it in different ways. 
But it should be trivial if you simply follow the code we wrote to resolve round.
