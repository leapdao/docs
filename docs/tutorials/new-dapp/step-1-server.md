Setup Server
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

Next step we will create our smart contract - which we would further refer as "spending condition" - and compile it.



