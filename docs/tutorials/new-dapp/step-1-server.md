Scaffold server
---  
Let's create a new folder and name it, for example, **leap-word-game**:

```bash
mkdir leap-word-game
cd leap-word-game
```
If you haven't installed Express in previous step you can do it now by running:
```bash
npm install express-generator -g
```

Next let's init our express server:
```bash
express server
```

This will create a new folder called server with express scaffold inside of it. Let's go inside of it and install project
dependencies:
```bash
cd server
npm install
```

Wait until installation is finished. After that you will need to install several additional packages:  
- **dotenv** will be used to inject environmental variables using .env file  
- **got** will handle our http-request for faucet  
- **corser** to handle CORS request from our front-end  
- **ethereumjs-util** will provide **ripemd160** method to create contract address  
- **leap-core** supplies methods and data structures to interact with Leap network  
- **random-words** will generate us random words for each round  
- **clipboardy** for handy clipboard methods  
- **openzeppelin-solidity** for IERC20 contract  
- **truffle** to compile our smart contracts  

```bash
npm install dotenv got ethereumjs-util ethers leap-core random-words corser
npm install -dev clipboardy openzeppelin-solidity
npm install -g truffle nodemon
```

Init truffle part (agree to install inside non-empty folder). This will create two folders "contracts" and "migrations"
```bash
truffle init
```

Let's spin up server to ensure that it's working
```bash
nodemon start
```
You can access index page at [http://localhost:3000](http://localhost:3000)

Add corser
---  
```javascript
// Add corser to imports
const corser = require("corser");

// Then activate it after app.set('view engine', 'jade');
app.use(corser.create());
```

Tools
---
In order to use wallet on server side we will need to have one and get some funds into it.
Let's create folder `tools` and put `generateWallet.js` file into it:
```javascript
const ethers = require('ethers/index');
const clipboardy = require('clipboardy/index');

async function main() {
  let wallet = ethers.Wallet.createRandom();
  clipboardy.writeSync(wallet.mnemonic);
  console.log("Mnemonic:", wallet.mnemonic);
  console.log("We also wrote put in your clipboard. You welcome :)");
}

main();
```

Open terminal inside `server` folder and run:
```bash
node tools/generateWallet
```

Script will create new mnemonic, output it to screen and also put it into your clipboard.
Create new `.env` file in the root of `server` folder and put contents of your clipboard after `WALLET_MNEMONIC` variable:

```dotenv
WALLET_MNEMONIC="PUT_GENERATED_MNEMONIC_HERE"
```

***
***Security Note:*** *Don't ever put your .evn file in repository as it contains crucial information, which can be used to
transfer ALL your wallet funds without your knowledge. Keep this somewhere safe. You will be able to setup this variables
later, when you deploy your server*

***
We will add another tool to transfer tokens from one account to another at later stage.

Config
---
Create new file `config.js` that will be used to provide token address, RPC url and wallet mnemonic for our server.
In this tutorial we gonna use LEAP token.
```javascript
const dotenv = require("dotenv");
dotenv.config();

//const RPC_URL = "https://testnet-node1.leapdao.org"; // Testnet RPC
//const TOKEN_ADDRESS = "0xD2D0F8a6ADfF16C2098101087f9548465EC96C98"; // Testnet LEAP

const RPC_URL = "https://staging-testnet.leapdao.org/rpc"; // Staging RPC
const TOKEN_ADDRESS = "0x0666eBbF26CDE07EA79FeCAe15e5f18394EBC149"; // Staging LEAP

// Get WALLET_MNEMONI from .env file
const { WALLET_MNEMONIC } = process.env;

module.exports = {
  RPC_URL,
  TOKEN_ADDRESS,
  WALLET_MNEMONIC,
};
```

Next step we will create our smart contract - which we would further refer as "spending condition" - and compile it.



