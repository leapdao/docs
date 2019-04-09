---
title: Spending Conditions Tutorial
---

# Overview

During the course of this short tutorial, we would deploy a [Spending Condition](https://docs.leapdao.org/spending-conditions/) to a LeapDAO testnet. It would allow you to retrace steps presented in this instructional [video](https://www.youtube.com/embed/cB5T0buF8GI).

In the course of the tutorial you will need:

- (Optional) Twitter account to get tokens.
- [Metamask](https://metamask.io/) to hold tokens and sign transactions.
- [Truffle](https://truffleframework.com/) or other way of compiling solidity code.
- [Node.js](https://nodejs.org/en/) to launch deployment script.

#### Getting LEAP tokens on testnet.

If you already have some LEAP tokens, feel free to skip this step.

1. Go to the [faucet](https://testnet.leapdao.org/faucet) page. You will need to have access to a twitter account for the next step.
2. As instructed on the faucet page, publish a tweet containing your **Ethereum address** and mention of **@leapDAO**.
3. Insert URL to your tweet on the faucet page, request tokens and go to [wallet](https://testnet.leapdao.org/wallet) page.
4. Connect metamask on Rinkeby network.

#### Spending Condition
In this part, we would take a look at an example spending condition and compile it.

1. Download `spending-conditions` [repository](https://github.com/leapdao/spending-conditions) or clone it from command line via `git clone https://github.com/leapdao/spending-conditions`. Solidity source code for the spending condition that we will interact with is located in `/Contracts/HashLockCondition.sol`.
2. Build the contract. Easiest way is to use `truffle compile` in root folder of the downloaded repository assuming you have [truffle](https://truffleframework.com/) installed. Look [here](https://truffleframework.com/docs/truffle/getting-started/installation) for notes on installing truffle framework.


#### Deployment

We will use a command-line script located at `tools/hashLockCondition.js` in `spending-conditions` repository to deploy our condition and interact with it. The source code of the script can be found [here](
 https://github.com/leapdao/spending-conditions/blob/master/tools/hashLockCondition.js).

The script takes two arguments. `hashLockCondition <token address> <message sender address>` which would replace `spenderAddr` and `tokenAddr` hardcoded in our Spending Condition. To deploy the script simply:

1. (Optional) You can set `RPC_URL` environmental variable like so `RPC_URL=https://testnet-node1.leapdao.org` to ensure your spending condition would be deployed to LeapDAO testnet. If `RPC_URL` is not set, the script should be pointed at LeapDAO testnet by default.
2. Execute the `tools/hashLockCondition.js` script. As first argument paste *Token contract address* displayed on your [wallet](https://testnet.leapdao.org/wallet) page. As second argument use your address displayed on wallet page as *My address*. The script will keep printing `Calling: plasma_unspent [<condition address>]` untill it receives transaction that we will sign in the next step.
3. Go to your leapDAO [wallet](https://testnet.leapdao.org/wallet) page and transfer some amount of LEAP tokens to the address that appears on your command line. You will need to sign transaction using Metamask browser plugin.

#### Verification

Once the spending condition has received your funds, you will see some raw input printed on command line and script will gracefully exit. The final step is to verify if script has indeed submitted both the spending condition and transaction to the LeapDAO testnet:

1. Once deployed spending condition receives transaction we signed in *deployment* step, raw transaction data would be printed to command line.
2. In order to see what happened once your transaction was submitted to testnet copy the hash following the string `transaction hash:` and paste it into LeapDAO block [explorer](https://testnet.leapdao.org/explorer).

If spending condition has succeeded (as it should in our example, assuming valid `<token address>` and `<message sender address>` were passed as arguments to the script) block explorer will show a page containing details of the transaction that was submitted.

If wrong argument (such as `<token address>` which does not exist on the blockchain) was passed to the script, then explorer would be unable to show transaction details and indicate this with message `No results found for your search`.
