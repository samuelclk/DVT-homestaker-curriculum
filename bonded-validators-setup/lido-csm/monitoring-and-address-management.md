# Monitoring & Address management

## Lido CSM Widget URLS

* **Mainnet:** [https://csm.lido.fi/](https://csm.lido.fi/)
* **Holesky:** [https://csm.testnet.fi/](https://csm.testnet.fi/)

## Monitoring validator performance

Refer to the monitoring section below.

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

## Status of uploaded keys

Review the status of your uploaded keys at the end of the **Lido CSM: Upload/Remove/View keys** section.

{% content-ref url="upload-remove-view-validator-keys.md" %}
[upload-remove-view-validator-keys.md](upload-remove-view-validator-keys.md)
{% endcontent-ref %}

## Monitoring for exit requests

The `DASHBOARD` header provides a consolidated view of any exit requests issued to you in the form of `Unbonded` and `Stuck` keys.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
As there are no custom alerts available yet, you should check this once every 3 days for now.
{% endhint %}

## Changing Rewards & Manager Address

Node operators can change the Rewards and Manager Address in the `REWARDS ADDRESS` & `MANAGER ADDRESS` tab under the `ROLES` header of the Lido CSM Widget.

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

The process of changing the Manager and Rewards addressed is two-phased:

* Propose new address from the existing one
* Accept the change from the new address

This process helps Node Operators to avoid incorrect changes to the non-existing address.

### Accept Rewards/Manager Address Assignments

Node operators can view their Address assignments in the `INBOX REQUESTS` tab under the `ROLES` header of the [CSM Widget](https://csm.testnet.fi/).

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Reset Reward/Manager Address Change

There will also be a method to reset Manager address to Reward Address from the Rewards address in case the Manager Address was compromised or lost.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
More details on Rewards vs Manager addresses [here](https://operatorportal.lido.fi/modules/community-staking-module#block-268ecefc0b37498badc1bf0baab04e0b).
{% endhint %}

### Step-by-Step to Change Rewards/Manager Address

1. Connect your current Rewards Address to the Lido CSM Widget
2. Propose a change to the Rewards or Manager Address&#x20;
   1. Navigate to the `REWARDS ADDRESS` or `MANAGER ADDRESS` tab under the `ROLES` header of the Lido CSM Widget
   2. Paste your new addresses for the change
   3.  Submit the onchain transaction

       <figure><img src="../../.gitbook/assets/image (1).png" alt="" width="563"><figcaption></figcaption></figure>
3. &#x20;Disconnect your current Rewards Address from the Lido CSM Widget
4. Connect your new Rewards or Manager Address to the Lido CSM Widget
5.  &#x20;Accept the Rewards or Manager Address assignment

    1. Navigate to the `REWARDS ADDRESS` or `MANAGER ADDRESS` tab under the `ROLES` header of the Lido CSM Widget
    2. Select the address assignments you want to accept&#x20;
    3. Submit the onchain transaction

    <figure><img src="../../.gitbook/assets/image (2).png" alt="" width="563"><figcaption></figcaption></figure>
