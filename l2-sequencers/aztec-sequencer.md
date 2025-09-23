---
description: Complete end-to-end guide for new and existing Ethereum node operators
---

# Aztec Sequencer

## Hardware Requirements (Testnet)

* CPU: 8 cores
* RAM: 16GB
* SSD: 1TB

{% hint style="success" %}
You can set this up using the same machine as your Mainnet Ethereum node if you have 32GB of RAM & 4TB SSD
{% endhint %}

<details>

<summary>Proposed hardware requirements for Mainnet</summary>

<table><thead><tr><th width="128">Component</th><th>ETH Sepolia</th><th>Aztec sequencer</th><th>Total</th></tr></thead><tbody><tr><td><strong>CPU</strong></td><td>8 cores</td><td>4 cores</td><td>12 cores</td></tr><tr><td><strong>RAM</strong></td><td>16GB</td><td>16GB</td><td>32GB</td></tr><tr><td><strong>Disk</strong></td><td>1TB</td><td>1TB</td><td>2TB</td></tr><tr><td><strong>Networking</strong></td><td>-</td><td>-</td><td>25Mbps up/down</td></tr></tbody></table>

[Guide to procure & assemble your hardware](../hardware-and-systems-setup/procuring-your-hardware.md) **(open in new tab).**

</details>

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

## Prepare ETH Sepolia Node

Download a copy of Eth Docker and name it `sepolia-eth-docker` to avoid conflicting with any existing mainnet versions.&#x20;

```sh
cd ~ && git clone https://github.com/eth-educators/eth-docker.git sepolia-eth-docker && cd sepolia-eth-docker
```

Install Eth Docker if you have not done so previously.

```sh
cd ~/sepolia-eth-docker
./ethd install
```

Call this shortcut `sepethd` and enable running it from anywhere in your terminal.

```sh
echo 'alias sepethd="~/sepolia-eth-docker/ethd"' >> ~/.bash_profile && source ~/.bash_profile
```

Configure Eth Docker.

```sh
sepethd config
```

**Main options:**

* Sepolia Testnet
* Ethereum RPC node

<details>

<summary>Other options:</summary>

* Any consensus & execution client combination
* Do you want to expire pre-merge history?: `No`
* Checkpoint sync provider: `Leave as default`
* Use MEV Boost: `No`
* Use Grafana dashboards: `Yes`
* Fallback fee recipient address: `Use your own wallet address here`

</details>

Expose RPC and REST endpoints of your Execution and Consensus clients.

```sh
nano ~/sepolia-eth-docker/.env
```

* Append `:el-shared.yml:cl-shared.yml` in the `COMPOSE_FILE` line.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<details>

<summary>Additional edits for those who are already running a Mainnet or Hoodi Ethereum node on the same machine</summary>

* Press `CTRL+W` and search for **EL\_P2P\_PORT.** Then, replace the value with `30305`

- Press `CTRL+W` and search for **CL\_P2P\_PORT.** Then, replace the value with `9001`

* Press `CTRL+W` and search for **EL\_RPC\_PORT.** Then, replace the value with `8547`
* Press `CTRL+W` and search for **EL\_WS\_PORT.** Then, replace the value with `8548`

- Press `CTRL+W` and search for **CL\_REST\_PORT.** Then, replace the value with `5053`
- Press `CTRL+W` and search for **GRAFANA\_PORT.** Then, replace the value with `3001`
- Press `CTRL+W` and search for **PROMETHEUS\_PORT.** Then, replace the value with `9091`

Scroll and search manually if `CTRL+W` does not work for you. This avoid port conflicts with your existing setup.&#x20;

</details>

&#x20;`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Start Eth Docker.

```sh
sepethd up
```

<details>

<summary>Monitor logs</summary>

Execution client. `CTRL+C` to exit monitoring view.

```
sepethd logs execution -f -tail 20
```

Consensus client. `CTRL+C` to exit monitoring view.

```
sepethd logs consensus -f -tail 20
```

</details>

## Install Aztec sequencer service

Download installer script.

<pre class="language-sh"><code class="lang-sh"><strong>cd
</strong><strong>bash -i &#x3C;(curl -s https://install.aztec.network)
</strong></code></pre>

Enable running `aztec` from anywhere in your terminal.

```sh
echo 'export PATH="$HOME/.aztec/bin:$PATH"' >> ~/.bash_profile && source ~/.bash_profile
```

Install the latest testnet version of aztec.

```sh
aztec-up -v latest
```

## Prepare your Aztec paramaters&#x20;

### Open up a notepad file to paste the following information.

1\) Get the public IP address of your device

```sh
curl api.ipify.org
```

2\) `Wallet #1` public address to receive block rewards: `Copy from your wallet interface.`

3\) `Wallet #2` public address to register as an Aztec validator: `Copy from your wallet interface.`

4\) `Wallet #2` private key to use with Aztec validator: `On Metamask, select a hot wallet address >> click on the "three lines" menu >> Account details >> Details >> Show private key`

### Get Sepolia Testnet ETH

Get at least 0.01 Sepolia ETH sent to `Wallet #2` from the following faucets.

**1) Google Faucet:** [https://cloud.google.com/application/web3/faucet/ethereum/sepolia](https://cloud.google.com/application/web3/faucet/ethereum/sepolia)

**2) PK910 POW Faucet:** [https://sepolia-faucet.pk910.de/](https://sepolia-faucet.pk910.de/)

### Set the environment variables

Create a new `aztec` folder and an environment variables file.

```sh
cd
mkdir aztec
cd aztec
nano .env
```

Copy and paste the following content in the file, then edit the variables tagged with `Edit:`&#x20;

```
# Execution & Consensus client endpoints
ETHEREUM_HOSTS="http://localhost:8545" 
L1_CONSENSUS_HOST_URLS="http://localhost:5052"

# Edit: Private key of validator account (must include 0x prefix)
VALIDATOR_PRIVATE_KEY="0xHOT_WALLET_PRIVATE_KEY"

# Edit: Public key of validator account
VALIDATOR_PUBLIC_KEY="WALLET_PUBLIC_KEY"

# Edit: Address to receive block rewards
COINBASE="ANOTHER_WALLET_PUBLIC_KEY"

# Edit: Public IP address (find with: curl api.ipify.org)
P2P_IP=xx.xx.xx.xx

# Aztec constants
STAKING_ASSET_HANDLER="0xF739D03e98e23A7B65940848aBA8921fF3bAc4b2"
L1_CHAIN_ID="11155111"
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

### Wait for ETH Sepolia Execution Client to fully sync

Takes \~6 hours to sync. Check with this command.

```sh
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}' http://localhost:8545
```

<details>

<summary>Outputs</summary>

**Synced:**

`{"jsonrpc":"2.0","result":false,"id":1}`

**Still syncing:**

`{"jsonrpc":"2.0","result":{"startingBlock":"0x0","currentBlock":"0x868853","highestBlock":"0x868853"},"id":1}`

</details>

## Start Aztec Sequencer

Open port 5052, 8545, 40400 on your device.

```sh
sudo ufw allow 5052
sudo ufw allow 8545
sudo ufw allow 40400
sudo ufw enable
```

Create docker compose file.

```sh
nano ~/aztec/docker-compose.yml
```

Paste the following contents.

```yaml
name: aztec-node
services:
  node:
    network_mode: host # Optional, run with host networking
    image: aztecprotocol/aztec:latest
    environment:
      ETHEREUM_HOSTS: $ETHEREUM_HOSTS
      L1_CONSENSUS_HOST_URLS: $L1_CONSENSUS_HOST_URLS
      DATA_DIRECTORY: /data
      VALIDATOR_PRIVATE_KEY: ${VALIDATOR_PRIVATE_KEY}
      COINBASE: $COINBASE
      P2P_IP: $P2P_IP
      LOG_LEVEL: debug
    entrypoint: >
      sh -c 'node --no-warnings /usr/src/yarn-project/aztec/dest/bin/index.js start --network alpha-testnet start --node --archiver --sequencer'
    ports:
      - 40400:40400/tcp
      - 40400:40400/udp
      - 8080:8080

    volumes:
      - /home/my-node/node:/data # Local directory
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Start command.

```sh
cd ~/aztec
source .env
docker compose up -d
```

Monitor for errors. `CTRL+C` to exit monitoring view.

```sh
docker logs aztec-node-node-1 -f --tail 20
```

<details>

<summary>Example outputs</summary>

**Working:**

`[14:14:45.496] DEBUG: sequencer Sequencer sync check succeeded {"worldState":{"number":3267,"hash":"0x16cefa4a7fbdf0ecc1e5c93c45f402f89dd3b2f41c4714e780c17bd39e2d86aa"},"l2BlockSource":{"number":3267,"hash":"0x16cefa4a7fbdf0ecc1e5c93c45f402f89dd3b2f41c4714e780c17bd39e2d86aa"},"p2p":{"number":3267,"hash":"0x16cefa4a7fbdf0ecc1e5c93c45f402f89dd3b2f41c4714e780c17bd39e2d86aa"},"l1ToL2MessageSource":{"number":3267,"hash":"0x16cefa4a7fbdf0ecc1e5c93c45f402f89dd3b2f41c4714e780c17bd39e2d86aa"}}`

In this case, **3267** is the latest block number of your Aztec validator.

**Not working:**

<mark style="color:red;">WIP</mark>

</details>

### Check Aztec node sync status

> Credits: [ceberus-node](https://x.com/CerberusNode)

```sh
bash <(curl -s https://raw.githubusercontent.com/cerberus-node/aztec-network/refs/heads/main/sync-check.sh)
```

Press `Enter` when prompted.

<figure><img src="../.gitbook/assets/image (221).png" alt=""><figcaption></figcaption></figure>

<details>

<summary>Fully synced output</summary>

<figure><img src="../.gitbook/assets/image (222).png" alt=""><figcaption></figcaption></figure>

</details>

### Check for config errors

Install the `gawk` package.

```sh
sudo apt update && sudo apt install gawk
```

Run this checker script.

> **Credits:** [DeepPatel2412](https://github.com/DeepPatel2412)

```sh
bash <(curl -Ls https://raw.githubusercontent.com/DeepPatel2412/Aztec-Tools/refs/heads/main/ConfigChecker)
```

**Expected output:**

```
==================================================
               Aztec Node Addresses
==================================================
● Node
  └── Status: Correct Setup ✓
● Validator Addresses
  └── 0x3208c6e0d44439f3ba9a605deec1f980f29e3928 ✓
  └── 0x3208c6e0d44439f3ba9a605deec1f980f29e3928 ✓
● Sequencer Address
  └── 0x3208c6e0d44439f3ba9a605deec1f980f29e3928 ✓
==================================================
              Aztec Directory Check
==================================================
● Directory Path
  └── Current/parent is: /home/slutnode/aztec ✓
● docker-compose.yml
  └── Status: Correct Setup ✓
● .env
  └── Status: Correct Setup ✓
==================================================
```

## Register your Aztec Validator (Sequencer)

### Complete ZK-KYC

* Join [Aztec Discord](https://discord.com/invite/aztec)
* Download [zkPassport](https://zkpassport.id/) on mobile and register your zkID
* Go to [https://testnet.aztec.network/add-validator](https://testnet.aztec.network/add-validator) and input your Passport's issuing country, issue date, and expiry date&#x20;
* Connect the wallet you used as your Aztec validator earlier (`Wallet #2`)
* Verify proof of humanity by scanning the QR code with the zkPassport app mobile app
* Register your validator and sign the transaction
* Input your discord handle to claim the `Explorer` role.
* Hit **Submit**. View your FIFO validator queue by searching for your address: [https://dashtec.xyz/queue](https://dashtec.xyz/queue)

## Port Forwarding

Forward port 40400 on your home/office router to your node. Refer to the page below for **Port Forwarding** steps.

{% content-ref url="../tips/advanced-networking.md" %}
[advanced-networking.md](../tips/advanced-networking.md)
{% endcontent-ref %}

## Updating your Aztec sequencer

```sh
cd ~/aztec
docker compose down && docker compose pull && docker compose up -d
```
