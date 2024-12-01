# Rewards & bonds

## Dashboard

<figure><img src="../../.gitbook/assets/image (1) (2).png" alt=""><figcaption></figcaption></figure>

### How to get here

* Go to the Lido CSM Widget
  * **Mainnet:** [https://csm.lido.fi/](https://csm.lido.fi/)
  * **Holesky:** [https://csm.testnet.fi/](https://csm.testnet.fi/)
* Select the `BOND & REWARDS` section in the navigation bar

### `CLAIM` tab

Here, you will see your net rewards and bond claimable in aggregate and broken down into it's individual parts. Notice that `Locked bond` is also deducted from your aggregate rewards here.

You will also be able to claim your net rewards + bond in total or in individual parts if you wish, and select among 3 token types to receive--ETH/stETH/wstETH.

### `ADD BOND` tab

<figure><img src="../../.gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>

There are 2 activities you can perform here:

1. Review the balance of your total bond provided and the excess/insufficient bond amounts
2. Add more bond so that you can get more of your uploaded validator keys deposited by the CSM or top up any shortages due to poor performance or slashing events. **Read more on bond penalties** [**here**](https://operatorportal.lido.fi/modules/community-staking-module#block-3951aa72ba1e471bafe95b40fef65d2b)**.**

Once your excess bond amount is sufficient for new validator keys to be deposited, the `Keys available to upload` will increase.

<figure><img src="../../.gitbook/assets/image (182).png" alt=""><figcaption></figcaption></figure>

On the other hand, if your bond falls below the required minimum, the unbonded keys count will increase.

<figure><img src="../../.gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
The required minimum bond amount is based on how many of your uploaded validator keys have been deposited by the CSM. Exiting your CSM-deposited keys will not unlock your `Locked bonds`.
{% endhint %}

**At this point, there are 2 options you can take:**

1. Top up your bond amount under the `ADD BOND` tab
2. Wait for new rewards to replenish the bond  amount until it is back to the required level

{% hint style="info" %}
You cannot replenish `Locked bonds` using the `ADD BOND` feature.
{% endhint %}

### `UNLOCK BOND` tab

<figure><img src="../../.gitbook/assets/image (184).png" alt=""><figcaption></figcaption></figure>

This tab allows you to replenish your `Locked bonds` due to MEV theft and resume the accruing of your CSM operator rewards.

## Resources

<table><thead><tr><th width="202">Category</th><th>Navigation</th></tr></thead><tbody><tr><td><a href="https://operatorportal.lido.fi/modules/community-staking-module#block-88e6d7eca6364a758541dc1ee66a278f">Bond &#x26; Rewards</a></td><td> CSM Operator Portal: "Economics" section</td></tr><tr><td><a href="https://operatorportal.lido.fi/modules/community-staking-module#block-3951aa72ba1e471bafe95b40fef65d2b">Bond Penalties</a></td><td>CSM Operator Portal: "Penalties" sub-section</td></tr></tbody></table>
