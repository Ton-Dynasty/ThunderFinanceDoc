---
description: >-
  Reward Calculation Formulas for Update Pool, User Deposit, Withdraw, and
  Harvest
---

# 🍯 Reward Formula

## Mathematical Form

The reward mechanism is designed to distribute a certain amount of tokens per block, adjusted by each pool's specific multiplier, which affects the proportion of total rewards that the pool receives. Here, we define a formula to calculate the rewards a user can earn from a specific pool over a given number of blocks.

$$
R = \frac{S\times B \times p }{T} \times \frac{L}{D}
$$

#### Variables

* $$S$$: Total token emission rate per block, i.e., the number of tokens distributed by the protocol per block, called as `rewardPerSecond` in our term.
* $$p$$: Reward multiplier for a specific pool, which determines the share of the total rewards that the pool receives.
* $$T$$: Total sum of reward multipliers for all pools.
* $$L$$: Amount of liquidity tokens the user has in a specific pool.
* $$D$$: Total amount of liquidity tokens in that pool.
* $$B$$: Number of blocks over which the rewards are calculated.

In this formula,

* &#x20;$$\frac{S \times B \times p}{T}$$ calculates the total rewards that the pool receives over the specified number of blocks.
* $$\frac{L}{D}$$ represents the proportion of the pool's liquidity that is provided by the user, determining their share of the pool's total rewards.

This approach ensures that rewards are distributed proportionally based on the amount of liquidity each user contributes relative to the total liquidity in the pool. This formula provides an overview of our contract. In subsequent sections, we will discuss its implementation.

***

## Implementation Detail

### Update Pool

{% hint style="info" %}
You can see Update Pool Formula in the contract at [here](https://github.com/Ton-Dynasty/ThunderMint/blob/fc9e29cd1d0a1253c54279bd49135740803bf428/contracts/trait\_master\_chef.tact#L200)
{% endhint %}

The reward calculation process involves several steps, each determined by specific formulas:

1. **Calculation of Total Reward Over Time:**

* The total reward accumulated over a period is calculated using the formula:

$$
\text{reward} = (\text{\_time} - \text{pool.lastRewardBlock}) \times \text{self.rewardPerSecond}
$$

* &#x20;`_time` is taken as the minimum of the current time or the deadline to prevent exceeding the deadline in calculations.
* The variable `reward` is calculated by subtracting the last updated block time (`lastRewardBlock`) from the current time (`_time`), and then multiplying by the `rewardPerSecond` that the MasterChef stores. This provides the total rewards that should be distributed by MasterChef during this period.

2. **Adjustment for Pool Allocation Point:**

* To calculate the actual reward amount for a specific token pool, consider the pool's allocation point relative to the total allocation points across all pools:

$$
\text{rewardAmount} = \text{ACC\_PRECISION} \times \text{reward} \times \text{pool.allocPoint} / \text{self.totalAllocPoint}
$$

* `ACC_PRECISION` is used to handle decimal calculations, ensuring precision in the distribution process.

3. **Accumulated Reward Per Share:**

* The accumulated reward per share within a pool is updated with:

$$
\text{pool.accRewardPerShare} = \text{pool.accRewardPerShare} + (\text{rewardAmount} / \text{pool.lpSupply})
$$

* `pool.accRewardPerShare` indicates the amount of reward that each unit of token staked in the pool is entitled to. The calculation divides the reward amount by the total liquidity supply (`lpSupply`) in the pool, adding this to the ongoing accumulation as rewards increase over time.

4. **Updating Last Reward Block:**

* After updating the reward calculation, the last reward block time is set to the current time or the deadline:

$$
\text{pool.lastRewardBlock} = \text{\_time}
$$

These formulas collectively determine how rewards are computed and allocated within the MasterChef contract, ensuring fair distribution based on the time tokens are staked and the proportion of the pool’s allocation point.

### User Deposit

{% hint style="info" %}
You can see User Deposit Formula in the contract at [here](https://github.com/Ton-Dynasty/ThunderMint/blob/fc9e29cd1d0a1253c54279bd49135740803bf428/contracts/mini\_chef.tact#L26)
{% endhint %}

When a user deposits tokens into MasterChef, the quantity of these staked tokens is added to the `lpSupply` for the specific pool (for example, the USDT pool). The formula is as follows:

$$
\text{pool.lpSupply} = \text{pool.lpSupply} + \text{msg.amount}
$$

`msg.amount` is the amount specified in the `JettonTransferNotification`, indicating how many tokens the user has transferred to MasterChef.

When MasterChef deploys a user's MiniChef contract and sends the `UserDeposit` message, the user's `rewardDebt` is also calculated using the formula below and then sent to MiniChef:

$$
\text{rewardDebt} = \frac{\text{pool.accRewardPerShare} \times \text{msg.amount}}{\text{ACC\_PRECISION}}
$$

`rewardDebt` is calculated based on the amount the user has deposited and the current `accRewardPerShare`. This represents the total rewards distributed by MasterChef up to that point in time. As `accRewardPerShare` increases over time, the user's `rewardDebt` will also increase. The term `ACC_PRECISION` is used for decimal calculations.

When MiniChef receives a `UserDeposit` message, it updates the user's staked amount and their `rewardDebt` using the following formulas:

$$
\text{userInfo.amount} = \text{userInfo.amount} + \text{msg.amount}
$$

$$
\text{userInfo.rewardDebt} = \text{userInfo.rewardDebt} + \text{msg.rewardDebt}
$$

These formulas ensure that the user’s staked amount and the corresponding reward debt are accurately updated upon receiving a deposit.

### User Harvest

{% hint style="info" %}
You can see User Harvest Formula in the contract at [here](https://github.com/Ton-Dynasty/ThunderMint/blob/fc9e29cd1d0a1253c54279bd49135740803bf428/contracts/mini\_chef.tact#L66)
{% endhint %}

When a user initiates a Harvest operation, the process unfolds as follows:

1. **Pool Update and Reward Calculation:**

* MasterChef first updates the pool to calculate the latest `accRewardPerShare`, which is then sent to MiniChef for further calculations.

2. **Calculation of Accumulated Rewards:**

* Upon receiving the updated `accRewardPerShare`, MiniChef calculates the accumulated rewards a user is entitled to using the formula:

$$
\text{accumulatedReward} = \frac{\text{userInfo.amount} \times \text{msg.accRewardPerShare}}{\text{ACC\_PRECISION}}
$$

* This formula represents the total reward accumulated based on the user’s staked amount and the latest accumulated reward per share.

3. **Determining Actual Pending Rewards:**

* To find out the actual reward a user can claim, the old `userInfo.rewardDebt` is subtracted from the `accumulatedReward`:

$$
\text{\_pendingReward} = \text{accumulatedReward} - \text{userInfo.rewardDebt}
$$

* `_pendingReward` is the reward that a user is eligible to claim after deducting previously issued rewards.&#x20;
* This means that `accumulatedReward` represents the current reward that the user should receive, while `userInfo.rewardDebt` represents the rewards that the user has already claimed. Therefore, subtracting the two gives the reward that the user is entitled to claim this time.

### User Withdraw

{% hint style="info" %}
You can see User Withdraw Formula in the contract at [here](https://github.com/Ton-Dynasty/ThunderMint/blob/fc9e29cd1d0a1253c54279bd49135740803bf428/contracts/mini\_chef.tact#L40)
{% endhint %}

When a user initiates a Withdraw operation, the process is as follows:

1. **Pool Update and Reward Debt Calculation:**

* Upon receiving a withdraw request, MasterChef updates the pool to compute the latest `accRewardPerShare`. It then calculates a reward debt based on the amount the user wishes to withdraw and sends this information to MiniChef through a `WithdrawInternal` message:

$$
\text{rewardDebt} = \frac{\text{pool.accRewardPerShare} \times \text{msg.amount}}{\text{ACC\_PRECISION}}
$$

2. **Updating the Staked Amount in MiniChef:**

* When MiniChef receives the `WithdrawInternal` message, it first updates the user's staked amount by subtracting the withdrawn amount:

$$
\text{userInfo.amount} = \text{userInfo.amount} - \text{msg.amount}
$$

3. **Updating the Reward Debt in MiniChef:**

* MiniChef then updates the reward debt to reflect the reduced stake:

$$
\text{userInfo.rewardDebt} = \text{userInfo.rewardDebt} - \text{msg.rewardDebt}
$$

* The rewards due for the period before the withdrawal will be claimed by the user the next time they call Harvest.

### User Withdraw & Harvest

{% hint style="info" %}
User Withdraw and Harvest primarily combines the logic of User Withdraw and User Harvest into a single operation. Those interested can refer to [this link](https://github.com/Ton-Dynasty/ThunderMint/blob/fc9e29cd1d0a1253c54279bd49135740803bf428/contracts/mini\_chef.tact#L93) to see the implementation method.
{% endhint %}
