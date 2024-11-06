# SSV Staker

## Credits

This guide references the ideas and work of one of the Lido Community Staking members, [@ivans\_music](https://x.com/ivans\_music). _**Check out his original work below!**_

{% embed url="https://hackmd.io/@9u0cieIUT2Gsdm7YYOCKUg/HkEsIu4pR#Comprehensive-guide-to-setting-up-a-distributed-Lido-CSM-validator-cluster-with-SSV-Network" %}

## Pre-requisites&#x20;

1\) Get testnet SSV tokens via the official SSV faucet below.

{% embed url="https://faucet.ssv.network/" %}

2\) Install and configure ETH Docker only if you have not completed the **SSV Operator** section

{% content-ref url="../../automation-tools/eth-docker.md" %}
[eth-docker.md](../../automation-tools/eth-docker.md)
{% endcontent-ref %}

* Choose `Holešovice Testnet` >> `Ethereum node - consensus, execution and validator client`
* Choose any configuration options for the subsequent steps. We only want to get to a point where you can use ETH Docker to generate validator keystores easily here.&#x20;

## Method 1: Distribute existing keystores

### Customise ETH Docker Settings

Open up the ETH Docker `.env` file for editing.

```sh
nano ~/eth-docker/.env
```

Add `:deposit-cli.yml` to the end of the `COMPOSE_FILE=` line.

### Generate validator keystores

First, generate your validator keystores.

```sh
ethd cmd run --rm deposit-cli-new --execution_address YOURHARDWAREWALLETADDRESS --uid $(id -u)
```

Copy the validator keystore onto your laptop. _**Open the terminal on your laptop and run:**_

```sh
scp $USER@EXTERNAL_IP_ADDRESS:~/eth-docker/.eth/validators/keystore*.json $HOME/Documents
```

**Note:** Replace EXTERNAL\_IP\_ADDRESS with your actual VM's external IP

Your validator keystore will now be found in the `Documents` folder of your laptop.&#x20;

{% hint style="danger" %}
Delete this copy of your validator keystore on your laptop completely after splitting it on SSV in the next section.
{% endhint %}

### Split keystores on SSV

Open up the[ SSV webapp](https://app.ssv.network/) and your wallet wallet.

* Click on the **Operators** drop down and switch to **Validators**&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-03 at 3.37.05 PM.png" alt=""><figcaption></figcaption></figure>

* Add Cluster >> Go to Distribute Validator >> Generate new key shares
* Select cluster size and the SSV Operators in your cluster >> Select `Online` as your preferred method to split your validator keystore
* Upload your keystore.json file and provide your keystore password (set during keystroke generation)
* Choose the period you want to run your validator key for, accept the fees charged by your chosen SSV Operators, and read + acknowledge the warnings/disclaimers
* Approve the spending of your SSV tokens and register your validator&#x20;

{% hint style="danger" %}
Delete the copy of your validator keystore on your laptop completely after splitting it on SSV.
{% endhint %}

## Method 2: Distributed Key Generation (WIP)

Open up the[ SSV webapp](https://app.ssv.network/) and your wallet wallet.

* Go to Distribute Validator >> Generate new key shares
* Select cluster size and the SSV Operators in your cluster >> Select `Offline` as your preferred DKG method
* Select `DKG - Generate from New Key,` number of keys you want to generate, & set the withdrawal address to `0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9` (Lido **TESTNET** withdrawal vault)
  * Select `Linux (and WSL)` and copy the DKG command that will be generated for you

### Initiating the DKG Ceremony

Make sure all cluster members have confirmed that their DKG service is running and reachable.

Run `cd`, then run the generated DKG command on your VM and you should see _**"DKG ceremony completed"**_ if the DKG ceremony completes successfully.&#x20;

<figure><img src="../../.gitbook/assets/image (202).png" alt=""><figcaption></figcaption></figure>

Back up all DKG output files located in `$HOME/ceremony*` folder.

```sh
#run on your laptop
scp -r $USER@<EXTERNAL_IP_ADDRESS>:~/ceremony* $HOME
```

Save this folder onto a USB drive and delete the copy on your laptop.

```sh
#run on your laptop
sudo rm -r $HOME/ceremony*
```

Return to the SSV Webapp and acknowledge **Step 2: Deposit Validator** (although this is not yet done at this point), then go to **Step 3** and click on Register Validator.

