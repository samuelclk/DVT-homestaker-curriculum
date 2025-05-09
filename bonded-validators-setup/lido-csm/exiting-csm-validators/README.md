# Exiting CSM validators

## Monitoring for exit requests

The `DASHBOARD` header provides a consolidated view of any exit requests issued to you in the form of `Unbonded` and `Stuck` keys.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## How to Exit Keys and Withdraw Your Bond

1\) Sign & broadcast an exit message for each validator key you want to exit. Refer to sub-sections/pages below.

2\) Wait for validator key to be fully exited on the beacon chain. Check your validator ID pubkey on [beaconcha.in](https://beaconcha.in/)

3\) Connect your wallet address to the Lido CSM Widget ([Mainnet](https://csm.lido.fi/)) ([Testnet](https://csm.testnet.fi/))

4\) Navigate to `Keys`>>`View Keys` to verify that the status of your validator key is marked as `Withdrawn`

<figure><img src="../../../.gitbook/assets/image (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

5\) Navigate to `Bond & Rewards`>>`Claim` to claim your deposited bond and any accumulated rewards

<figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="warning" %}
The 32 ETH deposited to activate each validator key will return to the Lido Protocol. Meanwhile, CSM Operators get their ETH-based bond deposits back from the Lido CSM Contract.
{% endhint %}

## Exit requests from Lido

There are 3 reasons why CSM operators can receive exit requests from the Lido Protocol:

1. The node operator's bond amounts fall below the minimum threshold for the number of validator keys deposited by the CSM
2. To cover withdrawal requests from stETH holders
3. By DAO decision in some exceptional cases. This will require an on-chain vote and public discussion on the [Lido Research forum](https://research.lido.fi/).

Exit requests can be observed in the number `Unbonded` keys on the operator dashboard of the CSM Web App.

<figure><img src="../../../.gitbook/assets/Screenshot 2024-06-27 at 3.48.39 PM.png" alt="" width="375"><figcaption></figcaption></figure>

When this happens, CSM operators must initiate an exit on the requested number of validator keys as soon as possible (within 96 hours) to avoid penalty measures from having `Stuck Keys`.

## Stuck Keys

`Stuck Keys` accrue when CSM operators do not perform timely (within 96 hours) exits on the required number of CSM-deposited validator keys when requested by the Lido Protocol.&#x20;

**Penalties of having `Stuck Keys` include:**

1. New validator keys of the CSM operator will not be deposited&#x20;
2. New staking rewards stop accruing for the CSM operator

Penalties are lifted when there are no more `Stuck Keys`.  More details [here](https://operatorportal.lido.fi/modules/community-staking-module#block-0ed61a4c0a5a439bbb4be20e814b4e38).
