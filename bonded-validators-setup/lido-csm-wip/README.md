# Lido CSM (WIP)

## How it works

Recall that running bonded validators via the Lido CSM does not require setting up a separate service on your hardware.

{% content-ref url="../../understanding-eth-validators/bonded-validators.md" %}
[bonded-validators.md](../../understanding-eth-validators/bonded-validators.md)
{% endcontent-ref %}

Instead, you simply tweak the parameters of the following steps of the native solo staking setup.

### Keystore generation--Withdrawal address

* During the validator key generation step, generate a number of validator keystores (e.g., max 10 per CSM operator) along with the deposit data file while setting the `withdrawalAddress` field to the Lido CSM contract on **Holesky:** [`0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9`](https://holesky.etherscan.io/address/0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9)

{% hint style="info" %}
**DO NOT DEPOSIT 32 ETH** using the deposit data file generated this way as the Lido CSM will make the deposit for you. _**Doing so will result in a loss of funds.**_
{% endhint %}

{% content-ref url="../../keystore-generation-and-mev-boost/validator-key-generation.md" %}
[validator-key-generation.md](../../keystore-generation-and-mev-boost/validator-key-generation.md)
{% endcontent-ref %}

You will then upload your `deposit data file` in the [next section](uploading-your-deposit-data-file.md). Make sure you complete the remaining steps on this page before that.&#x20;

### **Validator Client Setup--Fee Recipient Address**

* During the validator client setup step, set the `fee_recipient` flag to the designated fee recipient address for Lido CSM on **Holesky:** [`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8)

{% content-ref url="../../native-solo-staking-setup/validator-client-setup/" %}
[validator-client-setup](../../native-solo-staking-setup/validator-client-setup/)
{% endcontent-ref %}

{% hint style="info" %}
For existing solo stakers, you can spin up a new validator client service specifically for your CSM validator keys so that you can retain your own `fee_recipient` address for your solo staking keys. Refer to [this sub-section](running-a-separate-vc-service.md).
{% endhint %}

### **MEV-Boost Setup--Relay endpoints**

* Remove the `-min-bid` flag (if used)

{% hint style="info" %}
**Note:** Ultra Sound and Aestus relays do not censor transactions from OFAC sanctioned addresses.
{% endhint %}

* During the MEV-Boost setup step, set the `-relay` flags only to the list of designated MEV relays for Lido CSM on **Holesky** below.&#x20;

<details>

<summary><strong>Designated MEV Relay List</strong></summary>

1\) Ultra Sound Relay (Holesky): [https://0xb1559beef7b5ba3127485bbbb090362d9f497ba64e177ee2c8e7db74746306efad687f2cf8574e38d70067d40ef136dc@relay-stag.ultrasound.money](https://0xb1559beef7b5ba3127485bbbb090362d9f497ba64e177ee2c8e7db74746306efad687f2cf8574e38d70067d40ef136dc@relay-stag.ultrasound.money)

2\) Aestus Relay (Holesky): [https://0xab78bf8c781c58078c3beb5710c57940874dd96aef2835e7742c866b4c7c0406754376c2c8285a36c630346aa5c5f833@holesky.aestus.live](https://0xab78bf8c781c58078c3beb5710c57940874dd96aef2835e7742c866b4c7c0406754376c2c8285a36c630346aa5c5f833@holesky.aestus.live)

3\) BloXroute Validator Gateway (Holesky): [http://0x821f2a65afb70e7f2e820a925a9b4c80a159620582c1766b1b09729fec178b11ea22abb3a51f07b288be815a1a2ff516@testnet.relay-proxy.blxrbdn.com:18552/](http://0x821f2a65afb70e7f2e820a925a9b4c80a159620582c1766b1b09729fec178b11ea22abb3a51f07b288be815a1a2ff516@testnet.relay-proxy.blxrbdn.com:18552/)

4\) BloXroute (Holesky): [https://0x821f2a65afb70e7f2e820a925a9b4c80a159620582c1766b1b09729fec178b11ea22abb3a51f07b288be815a1a2ff516@bloxroute.holesky.blxrbdn.com](https://0x821f2a65afb70e7f2e820a925a9b4c80a159620582c1766b1b09729fec178b11ea22abb3a51f07b288be815a1a2ff516@bloxroute.holesky.blxrbdn.com)

5\) Beaver Build (Holesky): [https://0x833b55e20769a8a99549a28588564468423c77724a0ca96cffd58e65f69a39599d877f02dc77a0f6f9cda2a3a4765e56@relay-holesky.beaverbuild.org](https://0x833b55e20769a8a99549a28588564468423c77724a0ca96cffd58e65f69a39599d877f02dc77a0f6f9cda2a3a4765e56@relay-holesky.beaverbuild.org)

6\) Eden Network (Holesky): [https://0xb1d229d9c21298a87846c7022ebeef277dfc321fe674fa45312e20b5b6c400bfde9383f801848d7837ed5fc449083a12@relay-holesky.edennetwork.io](https://0xb1d229d9c21298a87846c7022ebeef277dfc321fe674fa45312e20b5b6c400bfde9383f801848d7837ed5fc449083a12@relay-holesky.edennetwork.io)

7\) Titan Relay (Holesky): [https://0xaa58208899c6105603b74396734a6263cc7d947f444f396a90f7b7d3e65d102aec7e5e5291b27e08d02c50a050825c2f@holesky.titanrelay.xyz/](https://0xaa58208899c6105603b74396734a6263cc7d947f444f396a90f7b7d3e65d102aec7e5e5291b27e08d02c50a050825c2f@holesky.titanrelay.xyz/)

8\) Flashbots Boost Relay (Holesky): [https://0xafa4c6985aa049fb79dd37010438cfebeb0f2bd42b115b89dd678dab0670c1de38da0c4e9138c9290a398ecd9a0b3110@boost-relay-holesky.flashbots.net](https://0xafa4c6985aa049fb79dd37010438cfebeb0f2bd42b115b89dd678dab0670c1de38da0c4e9138c9290a398ecd9a0b3110@boost-relay-holesky.flashbots.net)

</details>

#### Verifying the MEV Relay List

You can verify the latest MEV Relay List for the Lido CSM on Holesky here: [https://holesky.etherscan.io/address/0x2d86C5855581194a386941806E38cA119E50aEA3#readContract](https://holesky.etherscan.io/address/0x2d86C5855581194a386941806E38cA119E50aEA3#readContract)

1. Go to the Etherscan link above and it will bring you to the MEV Relay Inclusion List used by the Lido CSM
2.  Under `Contract`>>`Read Contract`>>`4. get_relays`>>`Query`

    <figure><img src="../../.gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure>
3. A list of relay endpoints will appear under this section `4. get_relays`. Verify that you are only using relay endpoints from this list.

{% hint style="info" %}
The Holesky version of this Etherscan link is currently broken so you will face an error. Refer to the **Designated MEV Relay List** in the previous section in the meantime.
{% endhint %}

## Workflow breakdown

{% tabs %}
{% tab title="Existing Solo Stakers" %}
1. [Generate new validator keys](generating-csm-keystores.md) while setting the `withdrawal_address` to the  Lido CSM contract on **Holesky:** [`0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9`](https://holesky.etherscan.io/address/0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9)
2. [Configure a separate validator client](running-a-separate-vc-service.md) while setting the `fee_recipient` flag to the designated fee recipient address for Lido CSM on **Holesky:** [`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8) and import the newly generated CSM keystores
3. [On your MEV-Boost service](../../keystore-generation-and-mev-boost/set-up-and-configure-mev-boost.md), remove the `-min-bid` flag (if used), and  set the `-relay` flags only to the list of designated MEV relays for Lido CSM on **Holesky** (refer to previous section)
4. [Upload the newly generated deposit data file](uploading-your-deposit-data-file.md) pertaining to your CSM keystores onto the Lido CSM Web App and provide the required bond amount in ETH/stETH/wstETH
5. Wait for your CSM validator keys to be funded by Lido and make sure your node remains online in the meantime!

{% hint style="info" %}
**DO NOT DEPOSIT 32 ETH** using the deposit data file generated this way as the Lido CSM will make the deposit for you. _**Doing so will result in a loss of funds.**_
{% endhint %}
{% endtab %}

{% tab title="New Solo Stakers" %}
1. Follow this guide until you have your execution and consensus client set up
2. [Generate new validator keys](../../keystore-generation-and-mev-boost/validator-key-generation.md) while setting the `withdrawal_address` to the  Lido CSM contract on **Holesky:** [`0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9`](https://holesky.etherscan.io/address/0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9)
3. [Configure your validator client](../../native-solo-staking-setup/validator-client-setup/) while setting the `fee_recipient` flag to the designated fee recipient address for Lido CSM on **Holesky:** [`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8) and import the newly generated CSM keystores
4. [Configure your MEV-Boost service](../../keystore-generation-and-mev-boost/set-up-and-configure-mev-boost.md). Do not set the`-min-bid` flag and  set the `-relay` flags only to the list of designated MEV relays for Lido CSM on **Holesky** (refer to previous section)
5. [Upload the newly generated deposit data](uploading-your-deposit-data-file.md) file pertaining to your CSM keystores onto the Lido CSM Web App and provide the required bond amount in ETH/stETH/wstETH
6. Wait for your CSM validator keys to be funded by Lido and make sure your node remains online in the meantime!

{% hint style="info" %}
**DO NOT DEPOSIT 32 ETH** using the deposit data file generated this way as the Lido CSM will make the deposit for you. _**Doing so will result in a loss of funds.**_
{% endhint %}
{% endtab %}
{% endtabs %}

### Step-by-step guide in the following sub-sections
