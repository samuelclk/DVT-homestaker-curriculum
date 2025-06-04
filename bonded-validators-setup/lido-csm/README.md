# Lido CSM

The [Community Staking Module (CSM)](https://operatorportal.lido.fi/modules/community-staking-module) is the [Lido protocol’s](https://lido.fi/) first module with permissionless entry, allowing any node operator to operate validators by providing an ETH-based bond as security collateral

## Workflow breakdown

Recall that running bonded validators via the Lido CSM does not require setting up a separate service on your hardware.

{% content-ref url="../../understanding-eth-validators/bonded-validators.md" %}
[bonded-validators.md](../../understanding-eth-validators/bonded-validators.md)
{% endcontent-ref %}

Instead, you simply tweak the parameters of the following steps of the native solo staking setup.

{% tabs %}
{% tab title="Existing Solo Stakers" %}
1. [Generate new validator keys](generating-csm-keystores.md) while setting the `withdrawal_address` to the  [Lido withdrawal vault](https://holesky.etherscan.io/address/0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9).
   * **Mainnet:** [`0xB9D7934878B5FB9610B3fE8A5e441e8fad7E293f`](https://etherscan.io/address/0xb9d7934878b5fb9610b3fe8a5e441e8fad7e293f)
   * **Hoodi:** [`0x4473dCDDbf77679A643BdB654dbd86D67F8d32f2`](https://hoodi.cloud.blockscout.com/address/0x4473dCDDbf77679A643BdB654dbd86D67F8d32f2)&#x20;
2. For the validator client, set the `fee_recipient` flag to the Lido Execution Layer Rewards Vault either [on the validator key level](set-fee-recipient-address/method-1-configure-on-validator-keys.md) or [configuring a separate validator client.](set-fee-recipient-address/method-2-configure-on-separate-validator-client.md)
   * **Mainnet:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)
   * **Hoodi :** [`0x9b108015fe433F173696Af3Aa0CF7CDb3E104258`](https://hoodi.cloud.blockscout.com/address/0x9b108015fe433F173696Af3Aa0CF7CDb3E104258)&#x20;
3. Import the newly generated CSM keystores1
4. For the [MEV-Boost service](../../keystore-generation-and-mev-boost/set-up-and-configure-mev-boost.md),
   1. the`-min-bid` flag may be configured either at MEV-Boost level or at the CL client, the current acceptable maximum value for min-bid is `0.07` [based on community consensus](https://research.lido.fi/t/lido-node-operator-mev-boost-min-bid-guidance/3347) and may change.&#x20;
   2. On the consensus layer client, set the `builder-boost-factor` (or equivalent flags) to `100%.` i.e., local and builder payloads should be treated with equal weights.&#x20;
   3. The `-relay` flags should be set to a list of values only using relays from the the [list of Vetted MEV-Boost Relays for Lido CSM](https://enchanted-direction-844.notion.site/6d369eb33f664487800b0dedfe32171e?v=8e5d1f1276b0493caea8a2aa1517ed65) (refer to "_**Key settings to note**_" section).
      1. **Mainnet:** Select **"Mainnet Active + Vetted"** tab. _You can choose only from relays tagged with `(must use some)` and `(may use)`, and must choose at least one tagged with `(must use some)`._&#x20;
      2. **Hoodi:** Select **"Hoodi Only"** tab
5. [Upload the newly generated deposit data file](upload-remove-view-validator-keys.md) pertaining to your CSM keystores onto the Lido CSM Widget and provide the required bond amount in ETH/stETH/wstETH
   * **Mainnet:** [https://csm.lido.fi/](https://csm.lido.fi/)
   * **Hoodi :** [https://csm.testnet.fi/](https://csm.testnet.fi/)
6. Wait for your CSM validator keys to be deposited by Lido and make sure your node remains online in the meantime!

{% hint style="info" %}
**DO NOT DEPOSIT 32 ETH** using the deposit data file generated this way as the Lido CSM will make the deposit for you. _**Doing so will result in a loss of funds.**_
{% endhint %}
{% endtab %}

{% tab title="New Solo Stakers" %}
1. Follow this guide from [Setup Overview](../../hardware-and-systems-setup/setup-overview.md) until you have your[ execution](../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-execution-layer-client/) and [consensus client ](../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/)set up
2. [Generate new validator keys](generating-csm-keystores.md) while setting the `withdrawal_address` to the  [Lido withdrawal vault](https://holesky.etherscan.io/address/0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9).
   * **Mainnet:** [`0xB9D7934878B5FB9610B3fE8A5e441e8fad7E293f`](https://etherscan.io/address/0xb9d7934878b5fb9610b3fe8a5e441e8fad7e293f)
   * **Holesky:** [`0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9`](https://holesky.etherscan.io/address/0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9)
3. For the validator client,
   1. Set the `fee_recipient` flag to the Lido Execution Layer Rewards Vault either [on the validator key level](set-fee-recipient-address/method-1-configure-on-validator-keys.md) or [configuring a separate validator client.](set-fee-recipient-address/method-2-configure-on-separate-validator-client.md)
      * **Mainnet:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)
      * **Holesky :** [`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8)
4. Import the newly generated CSM keystores
5. For the [MEV-Boost service](../../keystore-generation-and-mev-boost/set-up-and-configure-mev-boost.md),&#x20;
   1. the`-min-bid` flag may be configured either at MEV-Boost level or at the CL client, the current acceptable maximum value for min-bid is `0.07` [based on community consensus](https://research.lido.fi/t/lido-node-operator-mev-boost-min-bid-guidance/3347) and may change.&#x20;
   2. On the consensus layer client, set the `builder-boost-factor` (or equivalent flags) to `100%.` i.e., local and builder payloads should be treated with equal weights.&#x20;
   3. The `-relay` flags should be set to a list of values only using relays from the the [list of Vetted MEV-Boost Relays for Lido CSM](https://enchanted-direction-844.notion.site/6d369eb33f664487800b0dedfe32171e?v=8e5d1f1276b0493caea8a2aa1517ed65) (refer to "_**Key settings to note**_" section).
      1. **Mainnet:** Select **"Mainnet Active + Vetted"** tab. _You can choose only from relays tagged with `(must use some)` and `(may use)`, and must choose at least one tagged with `(must use some)`._&#x20;
      2. **Holesky :** Select **"Holesky Only"** tab
6. [Upload the newly generated deposit data file](upload-remove-view-validator-keys.md) pertaining to your CSM keystores onto the Lido CSM Widget and provide the required bond amount in ETH/stETH/wstETH
   * **Mainnet:** [https://csm.lido.fi/](https://csm.lido.fi/)
   * **Holesky :** [https://csm.testnet.fi/](https://csm.testnet.fi/)
7. Wait for your CSM validator keys to be deposited by Lido and make sure your node remains online in the meantime!

{% hint style="info" %}
**DO NOT DEPOSIT 32 ETH** using the deposit data file generated this way as the Lido CSM will make the deposit for you. _**Doing so will result in a loss of funds.**_
{% endhint %}
{% endtab %}
{% endtabs %}

### _\*Step-by-step guide in the following sub-sections_

## Get Support

{% tabs %}
{% tab title="Lido Discord Community" %}
**Join here:** [**https://discord.com/invite/lido**](https://discord.com/invite/lido)

#### Instructions

* Select the `Rules` channel and react

<figure><img src="../../.gitbook/assets/image (190).png" alt=""><figcaption></figcaption></figure>

* Select the `cs-get-started` channel and react with both emojis

<figure><img src="../../.gitbook/assets/image (192).png" alt=""><figcaption></figcaption></figure>

* Drop your questions in the `csm-testnet` or  `csm-mainnet` channel
{% endtab %}

{% tab title="Stakesaurus Telegram Community" %}
**Join here:** [https://t.me/stakesaurus](https://t.me/stakesaurus)
{% endtab %}
{% endtabs %}

## How CSM works

As an overview, the Lido CSM deposits valid validator keys uploaded by node operators if the minimum bond required has also been provided.

{% hint style="info" %}
**"Valid validator keys"** in this case refers to validator keystores generated while setting the `withdrawalAddress` field to the Lido CSM contract.
{% endhint %}

### Rewards&#x20;

Solo stakers receive rewards from 2 sources:

1. **Bond rebase**: staking rewards generated from the bonded tokens ((w)stETH)--e.g., `90% * ETH PoS staking yield * total ETH bond provided`
2. **Node Operator rewards**: 6% share of rewards from the active validator keys deposited by the Lido Protocol with possible reductions for bad performance--e.g., `6% * ETH PoS staking yield * total validator keys deposited (32 ETH each) - poor performance penalties`

{% hint style="info" %}
**Note:** The share of rewards % above apply only on CSM V1. The values for CSM V2 may differ and will be set by DAO vote
{% endhint %}

Further, CSM operators will enjoy 2 additional rewards features described in more detail [here](https://operatorportal.lido.fi/modules/community-staking-module#block-6f17b30ed89d4b7eacc5d94a6a9a6095):

* **Rewards smoothing** across all Lido modules (e.g., block proposer rewards, sync committee rewards)
* **Rewards socialisation** among validators whose performance exceeds a certain threshold and underperforming validators will receive no node operator rewards for the given frame

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption><p>Illustration on Rewards Socialisation</p></figcaption></figure>

### Bond mechanics

#### Providing the CSM bond

The required bond amounts can be provided by anyone, although it will most likely come from the node operator using the CSM (CSM operator) themselves.

#### Bond decrease

The bond provided serves as a deterrence against dishonest behaviours and poor performance by the node operator. e.g.,

1. **MEV theft:**  If detected, a fine will be imposed by burning part of the node operator's bond and an amount of bond equivalent to the stolen amount will be locked until it is made whole.
2. **Slashing events:** Slashing penalties will be deducted from the bond amount and burnt
3. **Sustained poor performance:** If the effective balance of any validators fall below 32 ETH, the shortfall will be deducted from the bond amount and burnt

These events will cause the net bond balance of the CSM operator to fall below the required threshold.&#x20;

In this scenario, the CSM node operator will cease to accrue rewards on their validator keys deposited by Lido CSM until:

* The CSM node operator tops up the shortage
* New rewards generated by the CSM node operator fills up the shortage--e.g., All new rewards will be used to replenish the bond shortage until it is back to the required level

#### Bond increase

On the other hand, because the bond is provided in stETH (which rebases in quantity), the bond balance of CSM operators will increase over time, above the required threshold.

Excess bond balance, together with accrued rewards, will be claimable by CSM operators from the CSM Web App.

{% hint style="info" %}
More details on bond mechanics [here](https://operatorportal.lido.fi/modules/community-staking-module#block-268ecefc0b37498badc1bf0baab04e0b).
{% endhint %}

### Rewards Address & Manager Address

There are 2 main addresses used by CSM operators.

1. **Rewards Address:** This is the address that all accrued rewards and excess bond amounts will go to when claimed. Rewards Addresses can change Manager Addresses but Rewards Addresses can only be changed by itself.
2. **Manager Address:** This is the address that can trigger the claiming of all accrued rewards and excess bond amounts to the Rewards Address. The Manager Address can also upload/remove new/existing deposit data files. The Manager Address cannot change the Rewards Address.

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-26 at 10.51.27 PM.png" alt=""><figcaption><p>Break down of scope for each address. Source: <a href="https://operatorportal.lido.fi/modules/community-staking-module#block-c58d307283e942ecab5eeb96f9a89235">Lido CSM operator portal</a></p></figcaption></figure>

Upon creation of a Node Operator these addresses are set equal, but they can be changed afterwards.

It is recommended to use different addresses for security reasons. A hot wallet may be used for the Manager address to simplify daily operations, while a cold wallet is preferable for the Rewards address to enhance security. Node Operators are solely responsible for the security of the private keys related to these addresses.

{% hint style="success" %}
For example, CSM operators with their hot wallet addresses included in the Early Adoption list can change their Rewards Address to a more secure address
{% endhint %}

**More details on Rewards vs Manager addresses** [**here**](https://operatorportal.lido.fi/modules/community-staking-module#block-268ecefc0b37498badc1bf0baab04e0b)**.**

## Key settings to note

These settings are part of the expectations for all node operators participating in the CSM. Read more [here](https://operatorportal.lido.fi/modules/community-staking-module#block-c58d307283e942ecab5eeb96f9a89235).

### Keystore generation--Withdrawal address

During the validator key generation step, generate a number of validator keystores along with the deposit data file while setting the `withdrawalAddress` field to the Lido Withdrawal Vault.

* **Mainnet:** [`0xB9D7934878B5FB9610B3fE8A5e441e8fad7E293f`](https://etherscan.io/address/0xb9d7934878b5fb9610b3fe8a5e441e8fad7e293f)
* **Hoodi:** [`0x4473dCDDbf77679A643BdB654dbd86D67F8d32f2`](https://hoodi.cloud.blockscout.com/address/0x4473dCDDbf77679A643BdB654dbd86D67F8d32f2)&#x20;

{% hint style="info" %}
**DO NOT DEPOSIT 32 ETH** using the deposit data file generated this way as the Lido CSM will make the deposit for you. _**Doing so will result in a loss of funds.**_
{% endhint %}

{% content-ref url="../../keystore-generation-and-mev-boost/validator-key-generation.md" %}
[validator-key-generation.md](../../keystore-generation-and-mev-boost/validator-key-generation.md)
{% endcontent-ref %}

You will then upload your `deposit data file` in the [next section](upload-remove-view-validator-keys.md). Make sure you complete the remaining steps on this page before that.&#x20;

### **Validator Client Setup--Fee Recipient Address**

During the validator client setup step, set the `fee_recipient` flag to the Lido Execution Layer Rewards Vault.&#x20;

* **Mainnet:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)
* **Hoodi:** [`0x9b108015fe433F173696Af3Aa0CF7CDb3E104258`](https://hoodi.cloud.blockscout.com/address/0x9b108015fe433F173696Af3Aa0CF7CDb3E104258)

{% content-ref url="../../native-solo-staking-setup/validator-client-setup/" %}
[validator-client-setup](../../native-solo-staking-setup/validator-client-setup/)
{% endcontent-ref %}

For existing solo stakers, you can set the `fee_recipient` address in one of 2 ways in [this sub-page](set-fee-recipient-address/):

* Method 1:  Set the `fee_recipient` address per validator key
* Method 2: Spin up a new validator client service specifically for your CSM validator keys so that you can retain your own `fee_recipient` address for your solo staking keys.&#x20;

### **MEV-Boost Setup**

### **Relay endpoints**

1. Set the `-min-bid` flag or set it to 0.07
2. During the MEV-Boost setup step, set the `-relay` flags only to the [list of designated MEV relays for Lido CSM](https://enchanted-direction-844.notion.site/6d369eb33f664487800b0dedfe32171e?v=8e5d1f1276b0493caea8a2aa1517ed65)
   * **Mainnet:** Select **"Mainnet Active + Vetted"** tab. _You can choose only from relays tagged with “must use some” and “may use”, and must choose at least one tagged with “must use some”._&#x20;
   * **Hoodi:** Select **"Hoodi Only"** tab

#### Verifying your MEV Boost setup

To verify that your validator pubkeys have been successfully registered onto the builder network, look out for the following lines in your logs.

* **Validator client logs:** `Validator *** 1 out of 1 validator registration(s) were successfully sent to the builder network via the Beacon Node.`
* **Mev-boost logs:** `level=info msg="http: POST /eth/v1/builder/validators 200" duration=0.076901 method=POST`

{% hint style="info" %}
Make sure you are seeing **method=POST** instead of **method=GET** in the Mev-boost logs.
{% endhint %}

#### Verifying the MEV Relay List

You can verify the latest MEV Relay List for the Lido CSM below.

* **Hoodi:** [https://hoodi.etherscan.io/address/0x279d3a456212a1294daed0faee98675a52e8a4bf#readContract](https://hoodi.etherscan.io/address/0x279d3a456212a1294daed0faee98675a52e8a4bf#readContract)
* **Mainnet:** [https://etherscan.io/address/0xf95f069f9ad107938f6ba802a3da87892298610e#readContract](https://etherscan.io/address/0xf95f069f9ad107938f6ba802a3da87892298610e#readContract)

**Steps to verify list of approved MEV Relays:**&#x20;

1. Go to the Etherscan link above and it will bring you to the MEV Relay Inclusion List used by the Lido CSM
2.  Under `Contract`>>`Read Contract`>>`4. get_relays`>>`Query`

    <figure><img src="../../.gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure>
3. A list of relay endpoints will appear under this section `4. get_relays`. Verify that you are only using relay endpoints from this list.

### Other MEV Boost Settings

<details>

<summary><em>How to configure Builder/Local Boost settings per CL/VC client</em> ❓</summary>

**Teku**

* Teku CL (action: set to 100), [Source](https://docs.teku.consensys.io/reference/cli#builder-bid-compare-factor)

```
--builder-bid-compare-factor
```

(Default: 90) For example, a builder bid comparison factor of 90 means the builder's payload is chosen when its value is at least 10% greater than what can be built locally.

**Prysm**

* Prysm CL (action: set to 0), [Source](https://docs.prylabs.network/docs/prysm-usage/parameters#client-stats-flags)

```
--local-block-value-boost
```

(Default: 10) Use builder block if: builder\_bid\_value \* 100 > local\_block\_value \* (local-block-value-boost + 100)

**Lighthouse**

* Lighthouse VC (action: set to 100), [Source](https://lighthouse-book.sigmaprime.io/help_vc.html)

```
--builder-boost-factor
```

(Default: 100)

**Nimbus**

For Nimbus, which setting has the priority:

**depends where the validators live (which component owns the private keys). If they're in the VC, then the VC determines it. if in the BN, then the BN does**

* Nimbus CL (action: set to 0), [Source](https://nimbus.guide/external-block-builder.html), [Source 2](https://nimbus.guide/options.html)

```
--local-block-value-boost
```

&#x20;(Default =10) Increase execution layer block values for builder bid comparison by a percentage.

* Nimbus VC (no action)

```
--builder-boost-factor
```

(Default: 100) Percentage multiplier to apply to the builder's payload value when choosing between a builder payload header and payload from the paired execution node.

**Lodestar**

Lodestar VC (action: --builder.selection = ‘maxprofit’), [Source](https://chainsafe.github.io/lodestar/run/validator-management/vc-configuration/#configure-your-builder-selection-andor-builder-boost-factor)

```
--builder.selection
```

(Default: "executiononly"). maxprofit: An alias of--builder.boostFactor=100, which will always choose the more profitable block.

</details>

