# ETHPillar

## VM/Hardware Setup

You will need to prepare your virtual machine (VM) or home staking hardware for all options below. Step-by-step guide below.

{% content-ref url="../hardware-and-systems-setup/assemble-your-hardware/practicing-on-cloud-vms.md" %}
[practicing-on-cloud-vms.md](../hardware-and-systems-setup/assemble-your-hardware/practicing-on-cloud-vms.md)
{% endcontent-ref %}

{% content-ref url="../hardware-and-systems-setup/assemble-your-hardware/" %}
[assemble-your-hardware](../hardware-and-systems-setup/assemble-your-hardware/)
{% endcontent-ref %}

## Lido CSM testnet workflow

### Video guide

{% embed url="https://www.youtube.com/watch?v=aZLPACj2oPI" %}

### Prepare your VM/Hardware

Create a new Google Cloud account to unlock $300 of free cloud credits.

Create a VM on the Google Cloud Console (or any other cloud provider) with the following machine specifications.

* CPU: 2 vCPU
* RAM: 8GB
* Disk: 350GB SSD
* OS: Ubuntu 24.04 LTS
* Enable HTTP & HTTPS traffic

{% hint style="info" %}
Estimated cost per month on Google Cloud = $84, or _**3.5 months of free practice time**_ with $300 of cloud credits&#x20;
{% endhint %}

### Install ETHPillar

**SSH into your VM/hardware:** Click on the dropdown beside the **"SSH"** column and select **"Open in browser window".** Click on **"Authorize"** when prompted.

<figure><img src="../.gitbook/assets/image (197).png" alt=""><figcaption></figcaption></figure>

Go to the [Coincashew website](https://www.coincashew.com/coins/overview-eth/ethpillar) and copy the latest 1-line installation command and paste it into your terminal.

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/coincashew/EthPillar/main/install.sh)"
```

Then, type + enter `ethpillar` and follow along the prompts in the terminal UI (TUI) to:

1. Sync a Nethermind execution client and a Nimbus Consensus + Validator Client
2. Generate suitable validator keys to participate in the Lido CSM
3. Import the generated validator keys onto your validator client
4. Copy the deposit data for uploading onto the [CSM Widget](https://csm.testnet.fi)

### Get Holesky ETH

Run through the first 3 faucets below.

{% content-ref url="../useful-resources/holesky-faucets.md" %}
[holesky-faucets.md](../useful-resources/holesky-faucets.md)
{% endcontent-ref %}

### Upload deposit data & provide bond

{% content-ref url="../bonded-validators-setup/lido-csm/upload-remove-view-validator-keys.md" %}
[upload-remove-view-validator-keys.md](../bonded-validators-setup/lido-csm/upload-remove-view-validator-keys.md)
{% endcontent-ref %}

### ETHPillar Navigation

1. `Arrow keys & Tab key`: Cycle options
2. `Space bar`: Select option
3. `Enter`: Confirm option
4. `CTRL + C`: Exit monitoring
5. `exit` command (type "exit" and `enter` in terminal) : Exit current view
