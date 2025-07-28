---
description: >-
  For existing Ethereum Mainnet node operators: Solo Stakers, Rocketpool, Lido
  CSM, SSV, Obol, Stakewise etc
---

# Nodeset Hyperdrive

{% hint style="warning" %}
This guide assumes you have an existing Ethereum Mainnet execution and consensus client already synced and running.
{% endhint %}

## Pre-requisites

1. Install, configure, and sync an Ethereum mainnet full node. Refer to the [ETH Docker](../automation-tools/eth-docker.md) or [ETHPillar](../automation-tools/ethpillar.md) setup segments if you do not have this yet.
2. Expose HTTP & Websocket ports on your execution client and the REST port on your consensus client. _Note down all 3 port numbers. We will refer to them as `PORT_1`, `PORT_2`, and `PORT_3`_
   1. [ETH Docker example](https://dvt-homestaker.stakesaurus.com/automation-tools/eth-docker#optional-make-el-and-cl-endpoints-accessible-on-host)
   2. ETHPillar example (WIP)
3. Identify the private IP address of your node by running `ip a | grep enp`. You are looking for a number that looks like this: `192.168.xx.xx`. _Note down this number. We will refer to this as `PRIVATE_IP_ADDRESS`_

## Install dependencies

General updates, curl, git, docker.&#x20;

```sh
sudo apt update -y && sudo apt upgrade -y
sudo apt install git curl -y
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

Add your current user to the docker group, then restart your device to apply the change.&#x20;

```sh
sudo usermod -aG docker $USER
sudo reboot 0
```

## Installing Hyperdrive

Download the latest binary release of Hyperdrive into `/usr/bin` and make this binary file executable.

```sh
sudo wget https://github.com/nodeset-org/hyperdrive/releases/latest/download/hyperdrive-cli-linux-amd64 -O /usr/bin/hyperdrive && sudo chmod +x /usr/bin/hyperdrive
```

Install Hyperdrive.

```sh
hyperdrive service install
```

## Configuring Hyperdrive

Start the Hyperdrive service configuration Terminal UI (TUI).

```sh
hyperdrive service config
```

**Select from the TUI:**&#x20;

1. Welcome: `Next`
2. Network: `Ethereum Mainnet`&#x20;
3. Client Mode: `Externally Managed`
4.  Select your Execution Client

    1. HTTP URL: http://`PRIVATE_IP_ADDRESS`:`PORT_1`
    2. Websocket URL: http://`PRIVATE_IP_ADDRESS`:`PORT_2`

    {% hint style="success" %}
    The default values for PORT\_1 and PORT\_2 are 8545 and 8546 respectively.
    {% endhint %}
5.  Select your Consensus Client

    1. HTTP URL: http://`PRIVATE_IP_ADDRESS`:`PORT_3`

    {% hint style="success" %}
    The default value for PORT\_3 is 5052.
    {% endhint %}
6. Use Fallback Clients: `No` (unless you have one)
7. Modules: `Stakewise`
8. Metrics: `Yes`
9. Mev-Boost Mode: `Externally Managed`&#x20;
10. **Save and Exit**

<details>

<summary>Editing your configurations</summary>

Running `hyperdrive service config` again will bring you to this menu.

<figure><img src="../.gitbook/assets/Screenshot 2025-07-28 at 11.19.25 PM.png" alt=""><figcaption></figcaption></figure>

_(Take note of the navigation instructions on the bottom of the TUI)_

**Configuration Options**

1. **Execution Client:**&#x20;
   1. Client Mode: `Externally Managed`
   2. Execution Client: Select from the dropdown menu according to your actual externally managed Execution Client used. _**Note:** Erigon is not available._
   3. HTTP URL: http://`PRIVATE_IP_ADDRESS`:`PORT_1`
   4. Websocket URL: http://`PRIVATE_IP_ADDRESS`:`PORT_2`

{% hint style="success" %}
The default values for PORT\_1 and PORT\_2 are 8545 and 8546 respectively.
{% endhint %}

2. **Beacon Node:**
   1. Client Mode: `Externally Managed`
   2. Beacon Node: Select from the dropdown menu according to your actual externally managed Consensus Client used. _**Note:** Grandine is not available._
   3. HTTP URL: http://`PRIVATE_IP_ADDRESS`:`PORT_3`

{% hint style="success" %}
The default value for PORT\_3 is 5052.
{% endhint %}

3. **MEV-Boost:**
   1. Enable Mev-Boost: Make sure this box is checked (X).
   2. Mev-Boost Mode: `Externally Managed`

4) **Fallback Clients:**
   1. Use Fallback Clients: Check this box (X) if you want to use a fallback client.
   2. Execution Client URL: http://`PRIVATE_IP_ADDRESS`:`PORT_1`
   3. Beacon Node URL: http://`PRIVATE_IP_ADDRESS`:`PORT_3`
   4. RPC URL (Prysm Only): Only use this if you are running Prysm as your Consensus Client/Beacon Node. _Defaults to http://`PRIVATE_IP_ADDRESS`:4000._
5) **All other configuration options:** Keep to default settings

### Saving your settings

Use the `TAB` button to navigate to the **Review Changes and Save** button at the bottom of the TUI.

</details>

{% hint style="danger" %}
Be very careful with confirming the next steps as it concerns slashing conditions.&#x20;
{% endhint %}

You will then see the following prompts:

* &#x20;`Would you like to start the Hyperdrive services automatically now? [y/n]`
* `It looks like this is your first time starting a Validator Client. Just to be sure, does your node have any existing, active validators attesting on the Beacon Chain? [y/n]`

Hit `y` and `Enter` for both if you do not have the same validator keys loaded onto Hyperdrive running elsewhere for at least 15 minutes _**(e.g., fresh install of Hyperdrive with no validator keys assigned to you yet).**_

### Creating the Hyperdrive node wallet

After the containers start, Hyperdrive will check your wallet status.&#x20;

In a fresh installation, it detects that you don't have a wallet and offers to create one.&#x20;

* For a new install, respond with `y`.&#x20;
* If this installation is part of disaster recovery or migration, choose `n`, as you'll use the recover wallet command instead.

Hyperdrive then walks you through creation of the wallet, presenting the mnemonic, and testing to ensure you saved it.

## Generate validator keys

Generate 17 new Ethereum validator keys (current limit per operator).

```sh
hyperdrive stakewise wallet generate-keys
```

#### Expected output:

```
Note: key generation is an expensive process, this may take a long time! Progress will be printed as each key is generated.

Generated 0x903bee1b9f05c133548f4afae99b7c51cfd1646389a629554a49bf69cb7ce0e4216ee50e3b882a665ca595949fff65aa (1/2) in 4.586464858s
Generated 0xa5172893d3252995c8a7178a88b7798edbc96b4733629eb96e04bd52b716645bd59cd2b1fb470ada8ac0b3d84cd84746 (2/2) in 4.697215708s
Completed in 9.283731898s.

You now have 2 validator keys ready for deposits:
	0x903bee1b9f05c133548f4afae99b7c51cfd1646389a629554a49bf69cb7ce0e4216ee50e3b882a665ca595949fff65aa
	0xa5172893d3252995c8a7178a88b7798edbc96b4733629eb96e04bd52b716645bd59cd2b1fb470ada8ac0b3d84cd84746

Restarting Validator Client to load the new keys... done!
Your new keys are now loaded.
Your node will deposit with them automatically once the vault has been funded.
It will start attesting for those validators automatically once they have been activated.
```

## Fund your Node Wallet

Fund your node wallet with 0.01 ETH to pay the gas costs of generating validators. Each validator costs approximately 0.00034 ETH at 1 gwei gas costs.&#x20;

{% hint style="warning" %}
If you run out of ETH in your wallet, you won't be able to create more validators, even if there are assets available.
{% endhint %}

**To retrieve your Node Wallet address.**

```
hyperdrive wallet status
```

## Register your Node Wallet Address

{% hint style="warning" %}
You will need to go through the on-boarding process to get permissions to access this. Apply under **"I am a Node Operator"** [here](https://nodeset.io/).
{% endhint %}

* Head over to [https://nodeset.io/](https://nodeset.io/) and go to **Dashboard**
* Connect your wallet and go to **Node Addresses**
* Add your `Node Address`
* Sign the terms of service on the **Stakewise Dashboard**

{% hint style="danger" %}
IMPORTANT: Backup your node wallet mnemonic and private key at this step. [Official back up recommendations by Nodeset.](https://docs.nodeset.io/stakewise-integration/node-operator-guide-wip#id-6.-backup-your-node-wallet-mnemonic-and-or-private-key)
{% endhint %}

## Monitoring your services

**All logs:**

```sh
hyperdrive service logs
```

**Validator client.**

```sh
hyperdrive service logs sw_vc
```

**Hyperdrive operator service.**

```sh
hyperdrive service logs sw_operator
```

* Ignore these warnings:

```
WARNING: your wallet has less ETH than StakeWise recommends (0.01 ETH per key).
Current wallet balance: 0.012000
You need 0.158000 more ETH to use all of these keys.
```

**Check validator key allocations from Nodeset.**

```sh
hyperdrive stakewise validator status
```

### Set up alerts

Use the following tools for easy alerting setup when your validators go offline or are underperforming.

* [https://beaconcha.in/](https://beaconcha.in/)
* [https://www.node-sentinel.xyz](https://www.node-sentinel.xyz)
* [https://ethduti.es/](https://ethduti.es/)

## Updating Hyperdrive

Download the latest binary release of Hyperdrive into `/usr/bin` and make this binary file executable.

```sh
sudo wget https://github.com/nodeset-org/hyperdrive/releases/latest/download/hyperdrive-cli-linux-amd64 -O /usr/bin/hyperdrive && sudo chmod +x /usr/bin/hyperdrive
```

Install Hyperdrive.

```sh
hyperdrive service install -d
# the -d flag skips the dependencies which you should already have
```

Restart Hyperdrive for the changes to take effect.

```sh
hyperdrive service start
```

## Reference:

Official Hyperdrive guide by Nodeset.

{% embed url="https://docs.nodeset.io/node-operators/hyperdrive" %}
