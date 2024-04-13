---
description: MasterChef contract is primarily for use by both users and protocols.
---

# 2️⃣ What is MasterChef ?

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p>High-level technical overview of MasterChef Contract</p></figcaption></figure>

## **MasterChef Introduction**

MasterChef is a crucial component of Thunder Finance, enabling both protocols and users to perform specific operations. Below are the actions that each can undertake with MasterChef:

* **For Protocols:**
  * The MasterChef contract primarily stores information related to liquidity mining.
  * Protocols must transfer rewards to MasterChef
  * Specify the tokens that users can stake.
* **For Users:**&#x20;
  * **Deposit:** Users can deposit tokens into MasterChef.
  * **Harvest:** Users can collect rewards from their staked tokens.
  * **Withdraw:** Users can withdraw their staked tokens.
  * **Withdraw and Harvest:** Users can simultaneously unstake their tokens and claim rewards.

Detailed descriptions of operations available to protocols and actions available to users will be provided in the following sections.

## **MasterChef: Protocol Operations**

### **Jetton MasterChef for Protocols:**

1. **Deployment by Kitchen Contract:**
   * Once the Jetton MasterChef contract is deployed by the Kitchen contract, it stores the specified reward token, total reward, and deadline.
2. **Transferring Reward Tokens:**
   * Protocols need to transfer Reward Tokens to Jetton MasterChef.
   * Upon receiving a JettonNotify, Jetton MasterChef verifies if the transferred Jetton amounts match the total reward.
   * Calculating Reward Per Second: Determines the amount of reward distributed per second by the MasterChef.
3. **Adding Pools:**
   * Protocols must add pools, specifying which tokens users are allowed to stake.
   * Adding a pool requires entering two fields:
     * The Jetton master address of the staking token.
     * The allocation point (allocPoint), which determines the percentage of rewards the pool is entitled to receive.
4. **Distribution of Rewards:**
   * Once pools are added, reward distribution begins.
   * Since a MasterChef may contain multiple pools, this allows users to stake various types of tokens, with the reward quantity for each token set by its allocPoint.

### **TON MasterChef for Protocols:**

1. **Deployment by Kitchen Contract:**
   * Upon deployment, the Kitchen Contract simultaneously transfers the reward TONs.
   * Calculating Reward Per Second: Determines the amount of reward distributed per second by the MasterChef.
   * Stores the designated reward token, total reward, and the deadline within the TON MasterChef.
2. **Adding Pools:**
   * Protocols must add pools, specifying which tokens users are allowed to stake.
   * Adding a pool requires filling in two fields:
     * The staking token's Jetton master address.
     * The allocation point (allocPoint), which determines the percentage of rewards the pool receives.
3. **Distribution of Rewards:**
   * Once pools are added, reward distribution begins.
   * Since a MasterChef may contain multiple pools, this allows users to stake various types of tokens, with the reward quantity for each token determined by its allocPoint.

## **MasterChef: User Operations**

Users can perform five operations on MasterChef:

1. **Deposit:**
   * Users stake token into MasterChef, which then deploys a MiniChef contract to store information on the staked amount and related rewards for that particular token.
2. **Harvest:**
   * Users send a `harvest` message to MasterChef, specifying which pool they wish to harvest from by providing the Jetton Master's Address. MasterChef uses the MiniChef to calculate the reward amount eligible for collection and then transfers the rewards to the user.
3. **Withdraw:**
   * Users send a `withdrawal` message to MasterChef, indicating which pool they wish to withdraw from by providing the Jetton Master's Address and the amount they wish to withdraw. MasterChef checks with MiniChef to ensure there is a sufficient amount for unstaking and, if confirmed, transfers the tokens to the user.
4. **Withdraw and Harvest:**
   * This combines operations 2 and 3, allowing users to withdraw their staked tokens and collect rewards simultaneously.
5. **UpdatePool:**
   * Anyone can call `UpdatePool` on MasterChef to refresh the reward calculation. MasterChef also automatically updates the pool during Deposit, Harvest, and Withdraw operations.

{% hint style="info" %}
For details on the reward calculation and how withdrawals affect reward collection, please refer to the [Reward Formula](../reward-formula.md).
{% endhint %}
