---
description: Store informations for users
---

# 3️⃣ What is MiniChef

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption><p>High-level technical overview of MiniChef Contract</p></figcaption></figure>

## MiniChef Introduction

The MiniChef contract primarily serves to store the quantities of tokens staked by users and information about their rewards. All data related to users' staked tokens are held within the MiniChef contract.

## **MiniChef Receives Four Types of Messages from MasterChef:**

1. **User Deposit:**
   * When MasterChef receives a deposit message from a user, it sends staking and reward information to the user's MiniChef for recording.
2. **User Withdraw:**
   * Upon receiving a withdrawal message from a user, MasterChef queries MiniChef to confirm if there are sufficient tokens for withdrawal.
   * It also records the rewards accrued during that period, which are sent to the user the next time they harvest.
   * MiniChef then accumulates rewards based on the token quantity remaining after withdrawal.
3. **User Harvest:**
   * When MasterChef receives a harvest message from a user, it sends the latest reward calculation to MiniChef for calculation.
   * MiniChef calculates the amount of rewards the user is eligible to claim and instructs MasterChef to transfer these rewards to the user.
   * Before the transfer, MiniChef updates user's reward information that have been claimed by the user.
4. **User Withdraw and Harvest:**
   * When MasterChef receives a message for both withdrawal and harvest, it not only sends the latest reward calculation to MiniChef but also sends the amount the user wishes to unstake for verification.
   * Once confirmed, MiniChef instructs MasterChef to transfer both the rewards and the unstaked tokens to the user.

{% hint style="info" %}
For a detailed explanation of the reward calculation, please refer to the [Reward Formula](../reward-formula.md).
{% endhint %}
