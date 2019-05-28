Scaffold server
---  
Let's create a new folder and name it, for example `leap-word-game`:

```bash
mkdir leap-word-game
```
Let's move inside of it and make two more folders
```bash
cd leap-word-game
mkdir universal client
```

If you haven't installed Express in previous step you can do it now by running:  
```bash
npm install express-generator -g
```


Next let's init our express server:
```bash
express server
```

This will create a new folder called `server` with express scaffold inside of it. Let's go inside of it and install project
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
In order to allow our server to accept cross-origin requests from front-end client we need to activate CORS handling.
We will use corser Express middleware for this. Edit `app.js` file in the root of `server` folder
```javascript
// On line 6 add corser to list of imported modules
var corser = require('corser');

// Then activate it on line 17 after app.use(logger('dev'));
app.use(corser.create());
```

Tools
---
To use wallet on server side we will need to have one and get some funds into it.
You can [skip this step](#set-environment-variables)

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
<a name="set-environment-variables"></a>

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
In root folder of our project go to `universal` and create new file `config.js`. We will use it to provide token address, RPC url and wallet mnemonic for our server.
In this tutorial we gonna use LEAP token and RPC on staging server. Changing it to testnet will be just a matter of updating
RPC and TOKEN_ADDRESS variables
```javascript
//const RPC_URL = "https://testnet-node1.leapdao.org"; // Testnet RPC
//const TOKEN_ADDRESS = "0xD2D0F8a6ADfF16C2098101087f9548465EC96C98"; // Testnet LEAP

const RPC_URL = "https://staging-testnet.leapdao.org/rpc"; // Staging RPC
const TOKEN_ADDRESS = "0x0666eBbF26CDE07EA79FeCAe15e5f18394EBC149"; // Staging LEAP

module.exports = {
  RPC_URL,
  TOKEN_ADDRESS,
};
```

Next step we will create our smart contract - which we would further refer as "spending condition" - and compile it.



