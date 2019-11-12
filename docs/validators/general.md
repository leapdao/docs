
## Network Scalability

Scalability is achieved through the hashing of up to 4000 transactions in one block with a block time of 2 - 4 seconds on the Plasma chain. Then 32 Plasma-blocks get "anchored" on the Ethereum network every 1 - 2 min as Periods. The compression of many transactions into a single hash which gets submitted to the root chain enables the described scalability.

![Plasma Network](/img/val-img1.svg "Description of the Plasma Network")

Every Validator earns one Period reward during one Epoch. The length of the one Epoch depends on the number of Validator slots. The Order is chosen randomly after each Epoch.

The Leap Network will be secured by the Staking of Leap tokens through the Validators. To understand the Leap Network Architecture we will explain some of the key components of the economic design.

## Glossary

**LEAP** - The token used for staking the Plasma Chain. LEAP also pays the transaction fees on the Plasma chain and reward validators for proposing new periods.

**Stake** - An amount of LEAP token locked in the Plasma contract for a specific slot. The stake is used as a collateral to vouch for the correctness of submitted blocks. When a validator empties a slot the stake is returned.

**Validator Node** - A software that can be downloaded and run to interact with the Plasma chain. Validator nodes participate in the consensus protocol by submitting period headers to the Plasma contract on the Ethereum network.

**Validators** - Individuals and organizations operating nodes and having stakes.

**Slashing** - A penalty for validators if invalid blocks are committed or the validator is offline.

**Epoch** - A interval of x blocks on the Plasma chain. After each epoch slots can change ownership and the tax rate is increased or decreased. Per epoch each slot will become a slot leader at least once.

**Slot** - A slot represents the right to propose a block at least once in each Epoch. Slots are constantly auctioned to the highest bidder.

**Period Reward** - Is a set of newly minted tokens that the validator receives as a reward for submitting a period. The reward is adjusted by the network depending on the RISS model.

**Tax** - A fee which is charged on the period reward. The tax is a governance parameter of the network and is determined through token governance.

**Inflation Cap** - Maximum yearly growth of total tokens supply.

## Proof of Stake

The staking validators in the Leap Network have to secure their validator spot through a slot auction. This means they bet on an empty slot with LEAP token to become the validator of this slot. When a validator owns a slot it is designated as active and can submit periods to the network.

A Validator keeps a slot as long as it's stake isn't outbid by another validator. When outbidding occurs the slot status changes to auctioned. Both validators can continue betting on the slot for one epoch, after which the validator with the highest bet is activated. Validators are likely to be outbid when their stake is slashed.

Slashing happens when validators make a mistake like not beeing online, use an outdated software or don't maintain enough ETH to pay the transaction fee. This staking of LEAP tokens guarantees that slots are allocated to the most suited validators, resulting in a secure and fast network.

When a Validator has a slot they have to stay online and submit one period per epoch. The order of the period submission is chosen randomly every epoch. After the submission of multiple epochs, a validator can request payout for period rewards.

## Leap Network Roadmap 

The Leap Network currently operates in a single-operator setup and has the goal to become a token-staked multi-operator network. For a smooth transition of the network, a 3-steps roadmap has been proposed:

![Roadmap](/img/val-img3.svg "Roadmap")

1. **Testnet - Stage 1** - Incentivisation of new testnet validator through bounties.

2. **Testnet - Stage 2** - A period of gamified rewards to educate validators and compete on the testnet.

3. **Mainnet - Launch** - The best validators of the testnet receive genesis slots on the token-staked multi-operator network running on the mainnet.


