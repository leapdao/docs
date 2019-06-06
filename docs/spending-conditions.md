---
title: Spending Conditions
---

Spending conditions are scripts providing the ability to apply specific rules and conditions to the transfer of funds. To understand spending conditions, we recap [Pay-to-Script-Hash](https://en.bitcoin.it/wiki/Pay_to_script_hash) (P2SH) transactions for Bitcoin: P2SH is a transaction type in Bitcoin which allows transactions to be sent to a script hash instead of sending to a public key. Bitcoins locked under P2SH can only be transferred by providing a script matching the script hash. In addition, the transaction needs to carry data which makes the script evaluate to true.

With these transaction types Bitcoin locks funds into programmable conditions. Transactions with the right input data can fulfill such conditions, and hence unlock the funds to lock them again under new conditions. This is how funds move through time on the Bitcoin blockchain.

Programmable conditions can be created with EVM bytecode, and the use of such conditions to govern the spending of funds gives them their name - "spending conditions". Spending conditions are smart-contract-like scripts. Yet, unlike smart contract spending conditions shall not affect arbitrary state. This would make the [exit game for any spending condition unfeasible](https://ethresear.ch/t/why-smart-contracts-are-not-feasible-on-plasma/2598). Rather, the output of spending conditions execution should not be affected by storage or other transfers. The inputs and outputs of the transaction holding the spending condition are the only permitted side effects.

Spending condition example: [SpendingCondition.sol](https://github.com/leapdao/spending-conditions/blob/master/contracts/SpendingCondition.sol)

<iframe width="560" height="315" src="https://www.youtube.com/embed/cB5T0buF8GI"
style="margin-bottom: 20px" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

If you are interested to contribute to the development of spending conditions, please have a look at the [white paper](https://docs.google.com/document/d/1vStTjqvqZGyiI5AVtpwCIMlHFnzC_4bbixsCfs27-M8) and [join the community](https://leapdao.org).