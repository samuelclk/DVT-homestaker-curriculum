# Aztec Sequencer

{% hint style="success" %}
Both new and existing Ethereum node operators can use the exact same flow on this page.
{% endhint %}

## Hardware Requirements

<table><thead><tr><th width="128">Component</th><th>ETH Sepolia</th><th>Aztec Sequencer</th><th>Total</th></tr></thead><tbody><tr><td><strong>CPU</strong></td><td>8 cores</td><td>4 cores</td><td>12 cores</td></tr><tr><td><strong>RAM</strong></td><td>16GB</td><td>16GB</td><td>32GB</td></tr><tr><td><strong>Disk</strong></td><td>1TB</td><td>1TB</td><td>2TB</td></tr><tr><td><strong>Networking</strong></td><td>-</td><td>-</td><td>25Mbps up/down</td></tr></tbody></table>

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

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

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

## Install Aztec Sequencer service

Download installer script.

```sh
bash -i <(curl -s https://install.aztec.network)
```

Enable running `aztec` from anywhere in your terminal.

```sh
echo 'export PATH="$HOME/.aztec/bin:$PATH"' >> ~/.bash_profile && source ~/.bash_profile
```

Install the latest testnet version of aztec.

```sh
aztec-up -v 1.1.2
```

## Prepare your Aztec sequencer paramaters&#x20;

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
    image: aztecprotocol/aztec:1.1.2
    environment:
      ETHEREUM_HOSTS: $ETHEREUM_HOSTS
      L1_CONSENSUS_HOST_URLS: $L1_CONSENSUS_HOST_URLS
      DATA_DIRECTORY: /data
      VALIDATOR_PRIVATE_KEY: $VALIDATOR_PRIVATE_KEY
      COINBASE: $COINBASE
      P2P_IP: $P2P_IP
      LOG_LEVEL: info
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

## Register your Aztec validator

### Complete ZK-KYC

* Join [Aztec Discord](https://discord.com/invite/aztec)
* Download [zkPassport](https://zkpassport.id/) on mobile and register your zkID
* Go to [https://testnet.aztec.network/add-validator](https://testnet.aztec.network/add-validator) and input your Passport's issuing country, issue date, and expiry date&#x20;
* Connect the wallet you used as your Aztec validator earlier (`Wallet #2`)
* Verify proof of humanity by scanning the QR code with the zkPassport app mobile app
* Register your validator and sign the transaction
* Input your discord handle to claim the `Explorer` role.
* Hit **Submit**. View your FIFO validator queue by searching for your address: [https://dashtec.xyz/](https://dashtec.xyz/)

## Port Forwarding

Forward port 40400 on your home/office router to your node. Refer to the page below for **Port Forwarding** steps.

{% content-ref url="../tips/advanced-networking.md" %}
[advanced-networking.md](../tips/advanced-networking.md)
{% endcontent-ref %}
