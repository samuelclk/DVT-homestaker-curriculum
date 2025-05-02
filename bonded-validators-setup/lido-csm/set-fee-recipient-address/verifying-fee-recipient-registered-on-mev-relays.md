# Verifying Fee Recipient Registered on MEV Relays

## Checker Script

### Mainnet

Install dependencies and activate a Python virtual environment.

```sh
sudo apt update
sudo apt install git python3-venv python3-full python3-requests
# Activate Python virtual environment
python3 -m venv ~/check_relay_env
source ~/check_relay_env/bin/activate
pip install web3 requests
```

Download the Fee Recipient checker script ([Source](https://gist.github.com/skhomuti/bc188884f6bdc1ce2c65a949e84b0dc5)).

```sh
git clone https://gist.github.com/bc188884f6bdc1ce2c65a949e84b0dc5.git check_no_relays
cd check_no_relays
```

Set the RPC endpoint using your local execution client or one of the free publicly available ones here: [https://ethereumnodes.com/](https://ethereumnodes.com/)

```sh
export RPC_URL="http://127.0.0.1:8545"
```

**or;**

```sh
export RPC_URL="https://eth.llamarpc.com"
```

Run the Checker Script and enter your CSM Node Operator ID when prompted.

```sh
python3 check_no_relays.py
```

**Expected output:**

<figure><img src="../../../.gitbook/assets/Screenshot 2025-02-28 at 5.06.55 PM (1).png" alt=""><figcaption><p>Example output using dummy pubkeys</p></figcaption></figure>

Once done, you can deactivate the Python virtual environment.

```sh
deactivate
```

### **Troubleshooting:**

```
ConnectionRefusedError: [Errno 111] Connection refused
```

If you face a "connection refused" error as seen above, it either means the RPC endpoint of your Execution Client is not enabled or the `RPC_URL` used is incorrect

**How to fix?**

{% tabs %}
{% tab title="Eth Docker" %}
Edit the `.env` file.

```
nano ~/eth-docker/.env
```

Add **`:el-shared.yml`** to the back of the `COMPOSE_FILE=` line.

Save and exit with `CTRL+O`, `ENTER`, `CTRL+X`.

Restart the Eth Docker stack with `ethd down` then `ethd up`.
{% endtab %}

{% tab title="systemd" %}
Enable RPC or HTTP endpoint on your Execution Client.

```sh
sudo nano /etc/systemd/system/EXECUTION_CLIENT.service
# replace EXECUTION_CLIENT with the actual file name
```

Make sure the following flags are added.

1. Geth, Reth, Erigon: `--http`
2. Nethermind: `--JsonRpc.Enabled`
3. Besu: `--rpc-http-enabled`

Save and exit with `CTRL+O`, `ENTER`, `CTRL+X`.

Restart your execution client.

```sh
sudo systemctl daemon-reload
sudo systemctl restart EXECUTION_CLIENT
# replace EXECUTION_CLIENT with the actual file name
```
{% endtab %}

{% tab title="Eth Pillar" %}
Enabled by default.
{% endtab %}
{% endtabs %}

### Testnet

<mark style="color:red;">WIP</mark>

## Manual Check

For each MEV Relay you have configured, replace `<VALIDATOR_PUBKEY>` for each of the commands below and run them on your terminal.&#x20;

Then verify that the Fee Recipient Address matches with the Lido Execution Layer Rewards Vaults on Mainnet or Holesky depending on your setup ([Source](https://operatorportal.lido.fi/modules/community-staking-module)).&#x20;

**Example output:**

> {"message":{"fee\_recipient":"<mark style="background-color:yellow;">0x388C818CA8B9251b393131C08a736A67ccB19297</mark>","gas\_limit":"30000000","timestamp":"1739938746","pubkey":"0x...."},"signature":"0x...."}

{% hint style="info" %}
Lido Execution Layer Rewards Vault Address&#x20;

* **Mainnet** = [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)
* **Hoodi** = [`0x9b108015fe433F173696Af3Aa0CF7CDb3E104258`](https://hoodi.cloud.blockscout.com/address/0x9b108015fe433F173696Af3Aa0CF7CDb3E104258)
{% endhint %}

{% tabs %}
{% tab title="Mainnet" %}
**1) Titan Relay Global (Mainnet) (Non-filtering):**

```
curl https://0x8c4ed5e24fe5c6ae21018437bde147693f68cda427cd1122cf20819c30eda7ed74f72dece09bb313f2a1855595ab677d@global.titanrelay.xyz/relay/v1/data/validator_registration?pubkey=<VALIDATOR_PUBKEY>
```

**2) Titan Relay Regional (Mainnet) (Filtering):**

```
curl https://0x8c4ed5e24fe5c6ae21018437bde147693f68cda427cd1122cf20819c30eda7ed74f72dece09bb313f2a1855595ab677d@regional.titanrelay.xyz/relay/v1/data/validator_registration?pubkey=<VALIDATOR_PUBKEY>
```

**3) Agnostic Relay (Mainnet)**

```
curl https://0xa7ab7a996c8584251c8f925da3170bdfd6ebc75d50f5ddc4050a6fdc77f2a3b5fce2cc750d0865e05d7228af97d69561@agnostic-relay.net/relay/v1/data/validator_registration?pubkey=<VALIDATOR_PUBKEY>
```

**4) Ultrasound Relay (Mainnet)**

```
curl https://0xa1559ace749633b997cb3fdacffb890aeebdb0f5a3b6aaa7eeeaf1a38af0a8fe88b9e4b1f61f236d2e64d95733327a62@relay.ultrasound.money/relay/v1/data/validator_registration?pubkey=<VALIDATOR_PUBKEY>
```

**5) Aestus Relay (Mainnet)**

```
curl https://0xa15b52576bcbf1072f4a011c0f99f9fb6c66f3e1ff321f11f461d15e31b1cb359caa092c71bbded0bae5b5ea401aab7e@aestus.live/relay/v1/data/validator_registration?pubkey=<VALIDATOR_PUBKEY>
```

**6) Manifold SecureRPC (Mainnet)**

```
curl https://0x98650451ba02064f7b000f5768cf0cf4d4e492317d82871bdc87ef841a0743f69f0f1eea11168503240ac35d101c9135@mainnet-relay.securerpc.com/relay/v1/data/validator_registration?pubkey=<VALIDATOR_PUBKEY>
```

**7) Flashbots (Mainnet)**

```
curl https://0xac6e77dfe25ecd6110b8e780608cce0dab71fdd5ebea22a16c0205200f2f8e2e3ad3b71d3499c54ad14d6c21b41a37ae@boost-relay.flashbots.net/relay/v1/data/validator_registration?pubkey=<VALIDATOR_PUBKEY>
```

**8) bloXroute Max-Profit (Mainnet)**

```
curl https://0x8b5d2e73e2a3a55c6c87b8b6eb92e0149a125c852751db1422fa951e42a09b82c142c3ea98d0d9930b056a3bc9896b8f@bloxroute.max-profit.blxrbdn.com/relay/v1/data/validator_registration?pubkey=<VALIDATOR_PUBKEY>
```

**9) bloXroute Regulated (Mainnet)**

```
curl https://0xb0b07cd0abef743db4260b0ed50619cf6ad4d82064cb4fbec9d3ec530f7c5e6793d9f286c4e082c0244ffb9f2658fe88@bloxroute.regulated.blxrbdn.com/relay/v1/data/validator_registration?pubkey=<VALIDATOR_PUBKEY>
```
{% endtab %}

{% tab title="Hoodi" %}
<mark style="color:red;">WIP</mark>
{% endtab %}
{% endtabs %}

## What to do if Fee Recipient is wrong? <a href="#verify-your-fee-recipient" id="verify-your-fee-recipient"></a>

It’s important to **verify your fee recipient configuration before your validator proposes a block**, as incorrect settings may result in protocol penalties.

#### Check your local configuration[​](https://docs.lido.fi/staking-modules/csm/guides/fee-recipient/#check-your-local-configuration) <a href="#check-your-local-configuration" id="check-your-local-configuration"></a>

Make sure the fee recipient address is set to the following.

{% hint style="info" %}
* **Mainnet** = [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)
* **Hoodi** = [`0x9b108015fe433F173696Af3Aa0CF7CDb3E104258`](https://hoodi.cloud.blockscout.com/address/0x9b108015fe433F173696Af3Aa0CF7CDb3E104258)
{% endhint %}

Check this in your setup as follows:

* **Dappnode:** open the `Staking Brain` and review the `Fee Recipient` field for each validator.
* **Stereum:** open the UI and check the fee recipient field in the validator client settings.
* **Eth-Docker / Sedge:** inspect your `.env` file and confirm the `FEE_RECIPIENT` variable is set correctly.
* **SSV:** log in to [app.ssv.network](https://app.ssv.network/), click on **Fee Address**, and verify the address.
* **Obol:** check your cluster definition file or Launchpad configuration _before_ deploying.
* **systemd (separate VC):** inspect your validator service file (`/etc/systemd/system/<validator-client>.service`) and confirm the correct `--fee-recipient` or equivalent flag is set.

{% hint style="danger" %}
Note for systemd users, make sure you **do not** set the Fee Recipient for your Solo Staking validator keys to the Lido Execution Layer Rewards Vault.
{% endhint %}

#### Check client logs[​](https://docs.lido.fi/staking-modules/csm/guides/fee-recipient/#check-client-logs) <a href="#check-client-logs" id="check-client-logs"></a>

Most validator clients log the configured fee recipient address during startup.\
Check your startup logs and confirm that the fee recipient is set to the **Lido Execution Layer Rewards Vault**.

### Seek Lido's help[​](https://docs.lido.fi/staking-modules/csm/guides/fee-recipient/#seek-lidos-help) <a href="#seek-lidos-help" id="seek-lidos-help"></a>

If you're running a validator client or using a custom setup not covered in this guide, and need help setting the correct fee recipient address, the **Lido Community Validator Specialists (Chimera)** are here to help.

Join the [Lido Discord](https://discord.gg/lido) and tag:

`@community-validator-support`

We’ll assist you in configuring your setup correctly to avoid penalties.
