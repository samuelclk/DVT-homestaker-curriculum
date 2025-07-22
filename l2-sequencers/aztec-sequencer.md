# Aztec Sequencer

## Install dependencies

General updates, curl, git, docker.&#x20;

```
sudo apt update -y && sudo apt upgrade -y
sudo apt install git curl -y
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

Add your current user to the docker group, then restart your device to apply the change.&#x20;

```
sudo usermod -aG docker $USER
sudo reboot 0
```

## Prepare ETH Sepolia Node

Download Eth Docker.

```
cd ~ && git clone https://github.com/eth-educators/eth-docker.git && cd eth-docker
```

Install Eth Docker.

```
cd ~/eth-docker
./ethd install
```

Enable running ethd from anywhere in your terminal.

```
source ~/.profile
```

Configure Eth Docker.

```
ethd config
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

```
nano ~/eth-docker/.env
```

* Append `:el-shared.yml:cl-shared.yml` in the `COMPOSE_FILE` line.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

&#x20;`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Start Eth Docker.

```
ethd up
```

## Install Aztec Sequencer service

Download installer script.

```
bash -i <(curl -s https://install.aztec.network)
```

Enable running `aztec` from anywhere in your terminal.

```
echo 'export PATH="$HOME/.aztec/bin:$PATH"' >> ~/.bash_profile && source ~/.bash_profile
```

Install the latest testnet version of aztec.

```
aztec-up -v 0.87.9
```

## Prepare your Aztec sequencer paramaters&#x20;

### Open up a notepad file to paste the following information.

1\) Get the public IP address of your device

```
curl api.ipify.org
```

2\) Wallet #1 public address to receive block rewards: `Copy from your wallet interface.`

3\) Wallet #2 public address to register as an Aztec validator: `Copy from your wallet interface.`

4\) Wallet #2 private key to use with Aztec validator: `On Metamask, select a hot wallet address >> click on the "three lines" menu >> Account details >> Details >> Show private key`

### Get Sepolia Testnet ETH

Get at least 0.01 Sepolia ETH sent to `Wallet #2` from the following faucets.

1\) Google Faucet: [https://cloud.google.com/application/web3/faucet/ethereum/sepolia](https://cloud.google.com/application/web3/faucet/ethereum/sepolia)

2\) PK910 POW Faucet: [https://sepolia-faucet.pk910.de/](https://sepolia-faucet.pk910.de/)

### Set the environment variables

Create a new `aztec` folder and an environment variables file.

```
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

```
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}' http://localhost:8545
```

<details>

<summary>Outputs</summary>

**Synced:**

`"False"`

**Still syncing:**

`{"jsonrpc":"2.0","result":{"startingBlock":"0x0","currentBlock":"0x868853","highestBlock":"0x868853"},"id":1}`

</details>

## Start Aztec Sequencer

Start command.

```
source .env
aztec start --node --archiver --sequencer \
  --network alpha-testnet \
  --l1-rpc-urls $ETHEREUM_HOSTS \
  --l1-consensus-host-urls $L1_CONSENSUS_HOST_URLS \
  --sequencer.validatorPrivateKey $VALIDATOR_PRIVATE_KEY \
  --sequencer.coinbase $COINBASE \
  --p2p.p2pIp $P2P_IP
```

Add your address as an L1 validator.

<pre><code><strong>aztec add-l1-validator \
</strong>  --staking-asset-handler=$STAKING_ASSET_HANDLER \
  --l1-rpc-urls $ETHEREUM_HOSTS \
  --l1-chain-id $L1_CHAIN_ID \
  --private-key $VALIDATOR_PRIVATE_KEY \
  --attester $VALIDATOR_PUBLIC_KEY \
  --proposer-eoa $VALIDATOR_PUBLIC_KEY
</code></pre>

## Open Ports

Open port 40400 on your device.

```
sudo ufw allow 40400
```

Forward port 40400 on your router to your node. Refer to the page below for **Port Forwarding** steps.

{% content-ref url="../tips/advanced-networking.md" %}
[advanced-networking.md](../tips/advanced-networking.md)
{% endcontent-ref %}
