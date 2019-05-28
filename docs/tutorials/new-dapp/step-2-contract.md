Implement Game Contract
---  
Spending conditions are scripts providing the ability to apply specific rules and conditions to the transfer of funds. 
You can get more details in [previous sections of this documentation](../../spending-conditions.md)  

Create new Solidity file inside `server/contracts` folder and call it `WordGame.sol`.  
Setup your pragma line and import IERC20 interface from openzeppelin library:
```solidity
pragma solidity ^0.5.2;
import "openzeppelin-solidity/contracts/token/ERC20/IERC20.sol";
```

Now let's define our contract and some constants we will use later
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

For the sake of simplicity our contract will have a single method **roundResult**, which will process player answer and 
transfer funds according to it.
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
```

Full listing of contract, sans comment, you can find below:
```solidity
pragma solidity ^0.5.2;
import "openzeppelin-solidity/contracts/token/ERC20/IERC20.sol";

contract WordGame {
	bytes32 constant ROUND_ID = 0x1337133713371337133713371337133713371337133713371337133713371337;
	bytes32 constant ANSWER = 0x1234123412341234123412341234123412341234123412341234123412341234;
	address constant TOKEN_ADDR = 0x1222222222222222222222222222222222222222;
	address constant PLAYER = 0x1333333333333333333333333333333333333333;
	address constant HOUSE = 0x1444444444444444444444444444444444444444;

	function roundResult(bytes32 _playerAnswer, bytes32 _roundId) public {
		require(_roundId == ROUND_ID, "wrong round ID");
		IERC20 token = IERC20(TOKEN_ADDR);
		uint balance = token.balanceOf(address(this));

		if (_playerAnswer == ANSWER) {
			token.transfer(PLAYER, balance);
		} else {
			token.transfer(HOUSE, balance);
		}
	}

	function cancel() public {
		IERC20 token = IERC20(TOKEN_ADDR);
		uint balance = token.balanceOf(address(this));
		token.transfer(HOUSE, balance);
	}
}
```

Compile Contract
---
First of all you will need to add some minor changes to `truffle-config.js` file. Open the file in your editor,
scroll to `compilers` section, uncomment version line and set it to 0.5.2 (same as we are using in contracts)
```json
  compilers: {
    solc: {
       version: "0.5.2",
       ...    
```

Now we need to compile our contract. Open terminal inside of your server folder and call:
```bash
truffle compile
```
This will create a new folder "build" with three files in it - `IERC20.json`, `Migrations.json` and `WordGame.json`  

