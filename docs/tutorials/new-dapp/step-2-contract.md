Implement Game Contract
---  
Spending conditions are scripts providing the ability to apply specific rules and conditions to the transfer of funds. 
You can get more details in [previous sections of this documentation](../../spending-conditions.md)  

Create new Solidity file inside `server/contracts` folder and call it `WordGame.sol`.  
Open this file in your code editor. 
Setup your pragma and import IERC20 interface from openzeppelin library:
```solidity
pragma solidity ^0.5.0;
import "openzeppelin-solidity/contracts/token/ERC20/IERC20.sol";
```

Then define contract and some constants we will use later
```solidity
contract WordGame {
	// Here we will define several constants that we replace at later stage
	// Think about those as simple masks. You can use any combination of hexadecimal numbers
	bytes32 constant ROUND_ID = 0x1337133713371337133713371337133713371337133713371337133713371337;
	
	// We will store our answer as bytes32 representation of selected word
	bytes32 constant ANSWER = 0x1234123412341234123412341234123412341234123412341234123412341234;
	
	// You can process the same contract using different tokens, by replacing this address
	address constant TOKEN_ADDR = 0x1222222222222222222222222222222222222222;
	
	// Player and house addresses to receive funds, based on outcome of the round
	address constant PLAYER = 0x1333333333333333333333333333333333333333;
	address constant HOUSE = 0x1444444444444444444444444444444444444444;
	
	// We will implement contract methods here
}
```

For the sake of simplicity our contract will have two methods:
- **roundResult** which will process player answer and transfer funds according to it
- **cancelRound** will simply return balance of the contract back to house wallet  
```solidity
function roundResult(bytes32 _playerAnswer, bytes32 _roundId) public {
	// Check that player is trying to unlock funds for correct answer 
	require(_roundId == ROUND_ID, "wrong round ID");
	
	// We will need IERC20 interface to get balance of our contract
	IERC20 token = IERC20(TOKEN_ADDR);
	uint balance = token.balanceOf(address(this));

	// And then, if answer is correct, contract will transfer funds either to player or house 
	if (_playerAnswer == ANSWER) {
		token.transfer(PLAYER, balance);
	} else {
		token.transfer(HOUSE, balance);
	}
}

function cancelRound() public {
	IERC20 token = IERC20(TOKEN_ADDR);
	uint balance = token.balanceOf(address(this));
	token.transfer(HOUSE, balance);
}
```
> Full listing [WordGame.sol](https://github.com/MaxStalker/leap-word-game/blob/master/server/contracts/WordGame.sol) 

Compile Contract
---
Open terminal inside of your `server` folder and call:
```bash
truffle compile
```
This will create a new folder "build/contract" with three files in it - `IERC20.json`, `Migrations.json` and `WordGame.json`.
In next section we will use `IERC20.json` to get toke balance and later `WordGame.json` to deploy spending condition.   

