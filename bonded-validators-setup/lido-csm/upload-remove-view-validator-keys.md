# Upload/Remove/View validator keys

## Upload keys

* Go to the Lido CSM Web App and connect your wallet that is eligible for the Early Adoption Period
* Select `Become a Node Operator` and then `Create a Node Operator`
* On the widget, upload your `deposit data file` and select the corresponding bond type (ETH, stETH, wstETH) & bond amount to provide. The following information will be displayed on the Web App.
  * The bond amount required for the first validator key
  * The bond amount required for the next validator key
  * The total bond amount required for all validator keys included in your `deposit data file`

<figure><img src="../../.gitbook/assets/Screenshot 2024-06-27 at 6.07.12 PM.png" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="info" %}
Recall that for each validator key that you the Lido CSM to fund, you will need to provide some amount of bond according to your bond curve.
{% endhint %}

* Specify your referrer (e.g., Dappnode, Stereum, eBunker) using the referrer's ERC-20 address so that Lido CSM can reward them for bringing you on board!&#x20;

{% hint style="info" %}
**Note:** This will **not** affect your rewards. So do your best to help your referrer receive their rewards! Finally, select `Submit`, sign the transaction with your connected wallet, and you are all set.
{% endhint %}

* Now you just need to wait for the Lido CSM to fund your validator keys (using your `deposit data file`). This is a first-in, first-out process so expect a queue when demand is high.&#x20;

{% hint style="info" %}
**DO NOT DEPOSIT 32 ETH** using the deposit data file generated this way as the Lido CSM will make the deposit for you. _**Doing so will result in a loss of funds.**_
{% endhint %}

## Remove keys

* Go to the CSM Web App, under the `KEYS` header
* Select the `REMOVE` tab on the widget
* Select the keys you want to remove--e.g., duplicate keys or keys that have their deposit data files misplaced

<figure><img src="../../.gitbook/assets/Screenshot 2024-06-27 at 6.12.59 PM.png" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="info" %}
Removing previously uploaded keys will incur a fee.
{% endhint %}

## View keys

You can also view the status of the keys pertaining to your uploaded deposit data file and take the necessary actions.

<figure><img src="../../.gitbook/assets/image (187).png" alt=""><figcaption></figcaption></figure>

<table><thead><tr><th width="145">Status</th><th>What it means</th><th>What to do?</th></tr></thead><tbody><tr><td><mark style="color:green;"><strong>Active</strong></mark></td><td>Key has been funded &#x26; is active on the beacon chain</td><td>Make sure your validator node is online to perform its duties</td></tr><tr><td><strong>Queued</strong></td><td>Key has been funded and pending activation on the beacon chain</td><td>Make sure your validator node is online to perform its duties</td></tr><tr><td><strong>Depositable</strong></td><td>Key is valid and bond is sufficient. Pending deposit from Lido Protocol</td><td>Maintain sufficient bond amounts</td></tr><tr><td><strong>Exited</strong></td><td>Key has been exited</td><td>None</td></tr><tr><td><mark style="color:orange;"><strong>Unbonded</strong></mark></td><td>Bond is insufficient for this Active Key</td><td>Top up bond or exit key</td></tr><tr><td><mark style="color:red;"><strong>Duplicated</strong></mark></td><td>Key has been uploaded twice</td><td>Remove duplicate key</td></tr><tr><td><mark style="color:red;"><strong>Invalid</strong></mark></td><td>Uploaded key has an invalid signature</td><td>Remove key</td></tr><tr><td><mark style="color:red;"><strong>Stuck</strong></mark></td><td>Exit request for Active Key was not fulfilled within 96 hours</td><td>Exit key</td></tr></tbody></table>
