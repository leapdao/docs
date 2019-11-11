# Introduction

## How the  Leap Network works - Validator offering 

LeapDAO is a decentralized autonomous organisation behind the Leap Network creating a scalable second layer solution for the Ethereum Blockchain. The Holacracy managed DAO has over 200 members world wide developing and maintaining the Network, to host a various dapp use cases by increasing the capacity of Ethereum as well as widening the scope of possible Dapps in the Ethereum ecosystem.

>“We develop Plasma Leap, a scaling technology to deliver 
>scalability as global public utility to the Ethereum main-net.”

The Leap Network creates scalability through a plasma layer two solution with proof of stake. Scalability is achieved through the hashing of XXXXX Transactions in one Block with a Block Time of 2 - 4 seconds on the Plasma Chain. Then the maximal amount of 32 Blocks get stamped on the Ethereum main - net every 1 - 2 min. (the Blocktime of Ethereum). This makes it possible to have an awesome scalability with 1000 transactions per second and less than 4 sec. per Transactions. 

Every Validator earns one Period reward during one Epoch. The length of the one Epoch depends on the amount of Validator slots. The Order is chosen randomly after each Epoch

The Leap Network will be secured by the Staking of Leap tokens through the Validators. To understand the Leap Network Architecture we will explain some of the key components of the economic design. 

**LEAP** - The token used for staking the Plasma Chain. LEAP also pay the transaction fees on the Plasma chain and reward validators for validating (proposing new blocks).

**Stake** - An amount of LEAP token locked in the Plasma contract for a specific slot. The stake is used as a collateral to vouch for the correctness of submitted block.

**Validator Node** - A software that can be downloaded and run to interact with the Plasma chain. Validator nodes participate in the consensus protocol by submitting block headers to the Plasma contract on the Ethereum network. 

**Validators** - Individuals and organizations operating nodes and having stakes.

**Slashing** - A penalty for validators if invalid blocks are committed.

**Epoch** - A period of x blocks on the Plasma chain. After each epoch slots can change ownership and the tax rate is increased or decreased. Per epoch each slot will become a slot leader at least once.

**Slot** - A slot represents the right to propose a block at least once in each Epoch. Slots are constantly auctioned to the highest bidder. 

**Block Reward** - Is a set of newly minted tokens that the validator includes as a coinbase transactions in the block that it proposes. The reward decreases according to the reduction schedule.

**Tax** - A fee which is charged on the stake for each EPOCH. The tax depends on the collective performance of the validators.

**Inflation Cap** - Maximum yearly grow of total tokens supply assuming every period in a year will receive maximum block reward. 



# Network Architekture 

## LeapDAO Proof of Stake

The staking Validators in the Leap Network have to secure their Validator spot through a Slot Auction. This means they bet on a empty Slot with Leap token to become the Validator of this slot. When a Validator owns a Slot it is designated as active. A Validator keeps that Slot as long as he doesn’t get slashed, which means he makes a mistake like haven’t been online, used a old software or hasn’t enough ETH to pay the transaction fee. The second possible case to los the Slot is when another Validator is betting on this Slot. When it happens the Slot status changes to Auctioned, which means both Validator betting on the Slot till the highest bet wins. This staking of Leap tokens guarantees that the best Validators run the system which leads to a secure and fast network. It also solves the “nothing-at-stake” problem due to the fact that multiple staking becomes very expensive for an Validator

When a Validator has a Slot they have to stay online and submit per epoch one Block. The order of the Block submission is chosen randomly every epoch. After the submission of a Block the Validator get their reward.

# Leap Network Roadmap 

The Leap Network is currently a single operator plasma and has the goal to become a Token staked Plasma chain. For a smooth transition of the Leap Network, our community created a road map to grow out of the single operator plasma to a multi operator plasma in the test net and end in the token staked live net. The Roadmap has 3 Milestones:

1. **Subsidy (DAI)** - Incentivisation of team members and bounty hunters through bounties to launch a Validator.

2. **Game of Stake** - An Staking game to secure a Validator Spot on the testnet 

3. **Validator OfferinG** - Offering Validators Leap tokens to participate in an continuously ongoing staking auction for a Validator slot. 

{ GRAFIK ROAD MAP - PICTURE JOHBA VALIDATOR CHANNEL }	
