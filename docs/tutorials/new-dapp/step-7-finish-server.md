Create Wallet Module
---
Inside the root of `server` folder create new `wallet.js` file:
```javascript
const ethers = require('ethers');
const { WALLET_MNEMONIC } = require('./config');

// Here we would simply restore wallet from mnemonic phrase
const wallet = new ethers.Wallet.fromMnemonic(WALLET_MNEMONIC);

module.exports = wallet;
```

Route and Endpoints
---
Open `app.js` in the root of `server` folder. Delete userRouter on line 8 and
add new game router there:
```javascript
var gameRouter = require('./routes/wordGame');
```

Now replace code on line 25
```javascript
// Replace this
// app.use('/users', usersRouter);
// and add gameRouter
app.use('/game',gameRouter);
```

Crete new file inside `server/routes` folder and call it `wordGame.js`.  
Start by loading modules and defining variables. 
```javascript
const express = require("express");
const router = express.Router();

const { TOKEN_ADDRESS } = require("../../universal/config");
const rpcClient = require("../rpcClient");
const wallet = require("../wallet");

const { generateNewRound, finishRound } = require("../game");

// For the sake of simplicity we will store active rounds inside memory
// Please not that all data will be wiped out, when server is restarted.
// Don't hesitate to update this to DB solution - Redis, for example
const rounds = {};

// We will hardcode round bet size, but it can be easily added to a list
// of params we get from POST request
const ROUND_BET = 100000000;
```

Then create endpoint to start round
```javascript
// Endpoint to start round
router.post("/startRound", async (req, res) => {
  const { playerAddress } = req.body;
  const params = {
    houseWallet: wallet,
    playerAddress,
    tokenAddress: TOKEN_ADDRESS,
    roundBet: ROUND_BET
  };

  let round;
  try {
    round = await generateNewRound(params);
  } catch (e) {
    res.status(500).send({ message: e.message });
  }

  // Store round in memory
  rounds[playerAddress] = round;

  // Client will only need list of words and roundAddress
  const { roundAddress, words } = round;
  res.status(200).send({ roundAddress, words });
});
```
>*Please note that we don't try to cover all "bad" cases but try to at least cover some base cases, 
where server could throw and error*

Next is endpoint to finish round
```javascript
// Endpoint to finish round
router.post("/finishRound", async (req, res) => {
  const { playerAddress } = req.body;
  const round = rounds[playerAddress];
  if (!round) {
    res.status(500).send({ message: "Round does not exist" });
  }

  const { word } = req.body;
  const params = {
    word,
    round,
    houseWallet: wallet,
    roundBet: ROUND_BET,
    tokenAddress: TOKEN_ADDRESS
  };
  const roundStatus = await finishRound(params);
  
  // roundStatus will contain two fields
  // - receiptHash
  // - roundBalance
  res.status(200).send(roundStatus);
});
```

Don't forget to export router, otherwise you will get an error:
```javascript
module.exports = router;
```

Test with Postman
---
Open terminal inside `server` folder and run:
```bash
nodemon start
```
Then open Postman, go to `File/Import/Paste Raw Text` and paste following curl:
```bash
curl -X POST \
  http://localhost:3000/game/startRound \
  -d playerAddress=0xF9f6607C019CcE7222815C04C303099527FF4A38
```
You can replace playerAddress with your own or use provided. The outcome should be the same.
Press "Send" and you should get response similar to this
```json
{
	"roundAddress": "0x5d4258ccc46d6afd1234d187ee7fc472129a8988",
	"words": [
		"evening",
		"piece",
		"using",
		"boat"
	]
}
```
In order to ensure that finishRound endpoint is working, we need to make transfer from client.
Go to [https://staging.leapdao.org/wallet](https://staging.leapdao.org/wallet), activate and connect Metamask.
Then ensure you are on Rinkby network and make transfer of any amount to roundAddress that you got from calling startRound.


Do the same to check finishRound endpoint:
```bash
curl -X POST \
  http://localhost:3000/game/finishRound \
  -d 'playerAddress=0xF9f6607C019CcE7222815C04C303099527FF4A38&word=boat'
```
Again you can replace **playerAddress** and **word** to your own.
After you press "Send" the response would be similar to this: 
```json
{
	"receiptHash": "0xae765ed54ad2af182607c23a1328f89f05be56918cdcf66746f2a4c36c495679",
	"roundBalance": "10000106000000"
}
```
Seems that everything is working just fine and we can proceed implementing our front-end client.
