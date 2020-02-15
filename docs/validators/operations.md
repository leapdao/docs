# Validator operations

* [Keep validator account funded with ether](#keep-validator-account-funded-with-ether)
* [Publishing heartbeats](#publishing-heartbeats)
    * [Heartbeat challenge game](#heartbeat-challenge-game)
* [Stay updated to the latest node version](#stay-updated-to-the-latest-node-version)

## Keep validator account funded with ether

## Publishing heartbeats

Upon registration each validator receives a heartbeat token on plasma. Validators are supposed to spend their heartbeat tokens to themselves periodically to signal their liveness. There is a `minimumPulse` governance parameter in Operator contract which specifies how often the heartbeat should happen. E.g. `minimumPulse` of 3 means that each validator should spend their heartbeat token at least once every three periods.

### Heartbeat challenge game

Everyone can challenge the validator didn't push their heartbeat within `minimumPulse` periods by calling `PoaOperator.challengeBeat` function (attaching a stake of `PoaOperator.exitStake` size). The validator will need to respond to the challenge in time defined by `casChallengeDuration` governance parameter. Validator responds by calling `PoaOperator.respondBeat` function providing inclusion proof for his heartbeat tx and the `period walk proof` from the challenged period till the period with heartbeat tx. `Period walk proof` is essentially a chain of consecutive period roots. For a validator to win the challenge game, his period walk proof should not be longer than `minimumPulse`. If validator is not responding in time, everyone can call `PoaOperator.timeoutBeat` and challenged slot will be emptied, validator slashed (not yet implemented) and challenger will get his stake back.

**Example 1 (validator wins challenge game):**

1. Current period tip is X and `minimumPulse` is 3.
2. Challenger claims that there were no heartbeat for slot 1
3. Validator responds to challenge submitting:
    - a valid set of consecutive period roots X'' → X' → X
    - a valid proof that heartbeat tx for slot 1 was included in a period X''

   In result, challenge is removed and validator is awarded challenger stake.

**Example 2 (validator didn't respond on time):**

1. Current period tip is X and `minimumPulse` is 3.
2. Challenger claims that there were no heartbeat for slot 1
3. After `casChallengeDuration` someone calls a `timeoutBeat`: challenge is removed, validator got slashed (not yet implemented) and removed from the slot. Challenger gets his stake back and gets part of the validator stake (not yet implemented).

**Example 3 (validator responds with longer period walk proof):**

1. Current period tip is X and `minimumPulse` is 3.
2. Challenger claims that there were no heartbeat for slot 1
3. Validator responds to challenge submitting:
    - a valid set of consecutive period roots X''' → X'' → X' → X
    - a valid proof that heartbeat tx for slot 1 was included in a period X'''

   In result, challenge response deemed invalid and challenge remains active

## Stay updated to the latest node version