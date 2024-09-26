# Upload/Remove/View validator keys

## Upload keys

* Go to the [CSM Widget](https://csm.testnet.fi/) and connect your wallet
* Select `Become a Node Operator` and then `Create a Node Operator`
* On the CSM Widget, upload your `deposit data file` and select the corresponding bond type (ETH, stETH, wstETH), and provide the desired bond amount

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% tabs %}
{% tab title="Copy & Paste" %}
Print the contents of the `deposit_data.json` file on your node machine, then copy & paste the contents into CSM Widget directly.

* Identify the actual file name of your `deposit_data.json` file on your node

```sh
ls ~/validator_keys
```

* **Print the contents:**

```sh
sudo cat ~/validator_keys/deposit_data-<timestamp>.json
#replace <timestamp> with the actual numbers in your file name
```
{% endtab %}

{% tab title="Upload File" %}
Transfer the deposit\_data-\<timestamp>.json file from the USB used in the validator key generation step onto your working device (e.g., laptop with Metamask), then upload the file.
{% endtab %}
{% endtabs %}

* Finally, select `Submit`, sign the transaction with your connected wallet, and you are all set.
* Now you just need to wait for the Lido CSM to deposit your validator keys (using your `deposit data file`). This is a first-in, first-out process so expect a queue when demand is high. More details on this process [here](https://operatorportal.lido.fi/modules/community-staking-module#block-25614a13674b465f875db871081091f9)

{% hint style="info" %}
**DO NOT DEPOSIT 32 ETH** using the deposit data file generated this way as the Lido CSM will make the deposit for you. _**Doing so will result in a loss of funds.**_
{% endhint %}

## Remove keys

The Node Operator can delete uploaded keys voluntarily (e.g., duplicate keys) if it has not been deposited yet.&#x20;

A fee is confiscated from the Node Operator's bond on each deleted key to cover maximal possible operational costs associated with the queue processing. Keys (via the deposit data file) can be deleted in continuous batches (e.g., from index 5 to 10).

More details [here](https://operatorportal.lido.fi/modules/community-staking-module#block-051fceb673504a489e541e3615984084).

* Go to the [CSM Widget](https://csm.testnet.fi/), under the `KEYS` header
* Select the `REMOVE` tab on the widget
* Select the keys you want to remove--

<figure><img src="../../.gitbook/assets/Screenshot 2024-06-27 at 6.12.59 PM.png" alt="" width="375"><figcaption></figcaption></figure>

{% hint style="warning" %}
Keys that have been deposited cannot be deleted and only can be exited.
{% endhint %}

## View keys

You can also view the status of the keys pertaining to your uploaded deposit data file and take the necessary actions.

<figure><img src="../../.gitbook/assets/image (187).png" alt=""><figcaption></figcaption></figure>

<table><thead><tr><th width="145">Status</th><th>What it means</th><th>What to do?</th></tr></thead><tbody><tr><td><mark style="color:green;"><strong>Active</strong></mark></td><td>Key has been deposited &#x26; is either pending activation or active on the <a href="https://holesky.beaconcha.in/">beacon chain</a></td><td>Make sure your validator node is online to perform its duties</td></tr><tr><td><strong>Depositable</strong></td><td>Key is valid and bond is sufficient. Pending deposit from Lido Protocol</td><td>Maintain sufficient bond amounts</td></tr><tr><td><strong>Exited</strong></td><td>Key has been exited</td><td>None</td></tr><tr><td><mark style="color:orange;"><strong>Unbonded</strong></mark></td><td>Bond is insufficient for this key, which can be Active or otherwise </td><td>- Active key: Top up bond or exit key<br>- Non-active key: Top up bond or do nothing</td></tr><tr><td><mark style="color:red;"><strong>Duplicated</strong></mark></td><td>Key has been uploaded twice</td><td>Remove duplicate key</td></tr><tr><td><mark style="color:red;"><strong>Invalid</strong></mark></td><td>Uploaded key has an invalid signature</td><td>Remove key</td></tr><tr><td><mark style="color:red;"><strong>Stuck</strong></mark></td><td>Exit request for Active Key was not fulfilled within 96 hours</td><td>Exit key</td></tr></tbody></table>
