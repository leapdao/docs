# The Token economy

> *"While fidelity bonds ensure accuracy towards the chain, we need to create further incentives around data availability and discourage halting. By only allowing staking using a token specific to the Plasma chain, one is able to ensure that there is incentive to continue operating, as the value of the token is derived from the net present discounted value of all future returns from staking. Consequently, network failures reduce the value of the tokens being held and individual actors have heavy incentive to act in the best interest towards the network's continued operation."*

[page 37 - plasma.io by Vitalik and Joseph](https://plasma.io/plasma.pdf "Plasma paper")

For this reason, LeapDAO creates a token economy with an infinite token supply, [reflexive inflation through staked supply](https://ethresear.ch/t/riss-reflexive-inflation-through-staked-supply/3633) and Leap token burning for transaction fees. The Leap token is used for:
	
* bond validators into PoS.
* for payment of transaction fees on the Leap Network.
* continuously minted to pay period rewards.

When inflating the token supply to pay validators, value is redistributed from all participants to the validators. How can this redistribution be minimized while still paying enough to keep the chain secure?

![Token economy](/img/val-img2.jpg "Token economy")

At the launch of the network, the inflation will be relatively high (inflation cap). It is assumed that validators will keep accumulating and staking tokens for future returns. Once the total amount of tokens staked exceeds 50% of supply, the block-rewards start decreasing. Block-rewards continue decreasing until the first validators are finding the validation not worth their costs and unstake from the chain. Somewhere in the right half of the graph, a market equilibrium is found where only the efficient validators can make a profit.

** => Validators enter into the competition and market equilibrium is established. **

Furthermore, we can pick a point on the graph, let’s say 80% and discuss the effects of token price on the validator market.

**Token price increases** - Validators are making more profits and more tokens are staked to participate in validation. The equilibrium moves to the right. This continues until some validators lose their profit margins and have to unbond. By using a quadratic function, the curve gets ever steeper, preventing that all tokens move into staking - leaving none for the real economy.

**Token price decreases** - Least effective validators go out of business, and have to unbond, decreasing the supply staked and thus driving up inflation. The equilibrium moves to the left.

With this method LeapDAO lets the market regulate the price for staking. Using the % of staked supply as an indicator of token value allows creating a reflexive rate of token issuance depending on the value of the Token as well as the activity on the chain.
