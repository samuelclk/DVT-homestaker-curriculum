# Method 1: Configure on validator keys

## Validator Clients

{% tabs %}
{% tab title="Teku" %}
Assuming your Teku validator client is already set up, stop your Teku validator client.

```sh
sudo systemctl stop tekuvalidator.service
```

Find the pubkey values of your own <mark style="color:red;">**non-CSM**</mark> validator keystores generated.

```sh
sudo find /var/lib -name "keystore*.json"
```

For each `resulting filepath`, run:

```sh
grep -oP '"pubkey": *"\K[^"]+' RESULTING_FILEPATH
```

_**You should now have a list of all your own**** **<mark style="color:red;">**non-CSM**</mark>** ****validator keystore pubkeys.**_

Create a custom proposer configuration file.

```sh
sudo nano /var/lib/teku_validator/validator/proposer_configuration.json
```

Paste the following contents into the file.

```
{
  "proposer_config": {
    "YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)_01": {
      "fee_recipient": "YOUR_OWN_FEE_RECIPIENT_ADDRESS",
      "builder": {
        "enabled": true
      }
    },
    "YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)_02": {
      "fee_recipient": "YOUR_OWN_FEE_RECIPIENT_ADDRESS",
      "builder": {
        "enabled": true
      }
    },
    "YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)_03": {
      "fee_recipient": "YOUR_OWN_FEE_RECIPIENT_ADDRESS",
      "builder": {
        "enabled": true
      }
    }
  },
  "default_config": {
    "fee_recipient": "LIDO_EXECUTION_LAYER_REWARDS_VAULT",
    "builder": {
      "enabled": true
    }
  }
}
```

**Replace** `YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)` with your own actual validator pubkeys <mark style="color:red;">**(NOT CSM).**</mark>

**Replace** `YOUR_OWN_FEE_RECIPIENT_ADDRESS` with your desired wallet address.

**Replace** `LIDO_EXECUTION_LAYER_REWARDS_VAULT` with the following options.

* **Mainnet**

```
suggested_fee_recipient: "0x388C818CA8B9251b393131C08a736A67ccB19297"
```

* **Holesky**

```
suggested_fee_recipient: "0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8"
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Set the permissions of the custom proposer configuration file.

```sh
sudo chown -R teku_validator:teku_validator /var/lib/teku_validator/validator
```

### Adding more <mark style="color:red;">non-CSM</mark> validator keystores:

If you want to add more of your own validator keystores, replicate the following segment, taking note of the indentation.

```
    "YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)_03": {
      "fee_recipient": "YOUR_OWN_FEE_RECIPIENT_ADDRESS",
      "builder": {
        "enabled": true
      }
    }
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Edit the Teku validator client service file.

```
sudo nano /etc/systemd/system/tekuvalidator.service
```

Add the `--validators-proposer-config` flag and point it to the `proposer_configuration.json` file. Then remove the `--validators-proposer-default-fee-recipient flag`. e.g.,

```
[Unit]
Description=Teku Validator Client (Holesky)
Wants=network-online.target
After=network-online.target

[Service]
User=teku_validator
Group=teku_validator
Type=simple
Restart=always
RestartSec=5
Environment="JAVA_OPTS=-Xmx6g"
Environment="TEKU_OPTS=-XX:-HeapDumpOnOutOfMemoryError"
ExecStart=/usr/local/bin/teku/bin/teku vc \
  --network=holesky \
  --data-path=/var/lib/teku_validator \
  --validator-keys=/var/lib/teku_validator/validator_keystores:/var/lib/teku_validator/keystore_password \
  --beacon-node-api-endpoint=http://<Internal_IP_address>:5051 \
  --validators-proposer-config=/var/lib/teku_validator/validator/proposer_configuration.json \
  --validators-proposer-blinded-blocks-enabled=true \
  --validators-graffiti="<your_graffiti_of_choice>" \
  --metrics-enabled=true \
  --metrics-port=8108 \
  --doppelganger-detection-enabled=true 

[Install]
WantedBy=multi-user.target
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Restart your Teku validator client.

```sh
sudo systemctl daemon-reload
sudo systemctl start tekuvalidator.service
sudo systemctl status tekuvalidator.service
```

Monitor for errors.

```sh
sudo journalctl -fu tekuvalidator -o cat | ccze -A
```
{% endtab %}

{% tab title="Nimbus" %}
Configure a separate validator client and set the `fee_recipient` address to the Lido Execution Layer Rewards Vault there. Refer to the subpage below.

{% content-ref url="method-2-configure-on-separate-validator-client.md" %}
[method-2-configure-on-separate-validator-client.md](method-2-configure-on-separate-validator-client.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Lodestar" %}
{% hint style="info" %}
The `--proposerSettingsFile` feature of Lodestar and its format is in [alpha and subject to change](https://chainsafe.github.io/lodestar/run/validator-management/validator-cli#--proposersettingsfile). Hence we will fallback to running a separate validator client to customise the `fee_recipient` address.
{% endhint %}

Create a custom `proposer_settings.yml` file.

```
sudo nano /var/lib/lodestar_validator/proposer_settings.yml
```

Use the following template and make the necessary edits.

```
proposer_config:
  'YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)_01':
    graffiti: 'non-CSM graffiti'
    strict_fee_recipient_check: false
    fee_recipient: 'YOUR_OWN_FEE_RECIPIENT_ADDRESS'
    builder:
      enabled: true
      gas_limit: "30000000"
  'YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)_02':
    fee_recipient: 'YOUR_OWN_FEE_RECIPIENT_ADDRESS'
    builder:
      enabled: "true"
      gas_limit: "30000000"
default_config:
  graffiti: 'CSM graffiti'
  strict_fee_recipient_check: true
  fee_recipient: 'LIDO_EXECUTION_LAYER_REWARDS_VAULT'
  builder:
    enabled: true
    gas_limit: "30000000"
```

**Replace** `YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)` with your own actual validator pubkeys <mark style="color:red;">**(NOT CSM).**</mark>

**Replace** `YOUR_OWN_FEE_RECIPIENT_ADDRESS` with your desired wallet address.

**Replace** `LIDO_EXECUTION_LAYER_REWARDS_VAULT` with the following options.

* **Mainnet**

```
suggested_fee_recipient: "0x388C818CA8B9251b393131C08a736A67ccB19297"
```

* **Holesky**

```
suggested_fee_recipient: "0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8"
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Set the permissions of the custom proposer configuration file.

```sh
sudo chown -R lodestar_validator:lodestar_validator /var/lib/lodestar_validator
```

### Adding more <mark style="color:red;">non-CSM</mark> validator keystores:

If you want to add more of your own validator keystores, replicate the following segment, taking note of the indentation.

```
  'YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)':
    fee_recipient: 'YOUR_OWN_FEE_RECIPIENT_ADDRESS'
    builder:
      enabled: "true"
      gas_limit: "30000000"
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Edit the `docker-compose.yml` file in the Lodestar folder.

```sh
cd ~/lodestar_validator
sudo nano docker-compose.yml
```

Add the `--proposerSettingsFile` flag and point it to the `proposer_settings.yml` file.

```
      - --proposerSettingsFile
      - /var/lib/lodestar_validator/proposer_settings.yml
```

Then remove the following flags.

```
      - --suggestedFeeRecipient
      - "<your_designated_ETH_wallet_address>"
```

**New example:**

```yaml
services:
  validator_client:
    image: chainsafe/lodestar:latest
    container_name: lodestar_validator
    user: <UID>:<GID>
    restart: unless-stopped
    volumes:
      - /var/lib/lodestar_validator:/var/lib/lodestar_validator
    command:
      - validator
      - --dataDir
      - /var/lib/lodestar_validator
      - --importKeystores
      - /var/lib/lodestar_validator/validator_keystores
      - --importKeystoresPassword
      - /var/lib/lodestar_validator/keystore_password/<validator_signing_keystore_password_file_name>.txt
      - --network
      - holesky
      - --beaconNodes
      - http://127.0.0.1:5052
      - --builder
      - --proposerSettingsFile
      - /var/lib/lodestar_validator/proposer_settings.yml
      - --doppelgangerProtection
      - --metrics
      - --metrics.port
      - "5064"
      - --graffiti
      - "your_graffiti_of_choice"
    environment:
      NODE_OPTIONS: --max-old-space-size=2048
    ports:
      - "5064:5064"
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Restart your Lodestar validator client.

```sh
docker compose down
docker compose up -d
```

Monitor for errors.

```sh
docker logs lodestar_validator -f
```

#### Method 2:

Alternatively, to configure a separate validator client and set the `fee_recipient` address to the Lido Execution Layer Rewards Vault there, refer to the following subpage.

{% content-ref url="method-2-configure-on-separate-validator-client.md" %}
[method-2-configure-on-separate-validator-client.md](method-2-configure-on-separate-validator-client.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Lighthouse" %}
Assuming you have set up your Lighthouse validator client and imported your CSM validator keystores. Stop your validator client.

```sh
sudo systemctl stop lighthousevalidator.service
```

Then, edit the `validator_definitions.yml` file with the designated `fee_recipeint` address.

```sh
sudo nano /var/lib/lighthouse_validator/validators/validator_definitions.yml
# Actual filepath might vary according to your configurations
```

Find the pubkeys of each of your CSM validator keystores.

```sh
sudo find /var/lib -name "keystore*.json"
```

For each `resulting filepath`, run:

```sh
grep -oP '"pubkey": *"\K[^"]+' RESULTING_FILEPATH
```

and add the following line under each keystore as a new line. **Note:** Take note of the exact indentation.&#x20;

* **Mainnet**

```
suggested_fee_recipient: "0x388C818CA8B9251b393131C08a736A67ccB19297"
```

* **Holesky**

```
suggested_fee_recipient: "0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8"
```

#### Example with Mainnet fee recipient:

```
---
- enabled: true
  voting_public_key: 0xa1a6d4a72db3fff3a81401cb9a80e1a7d18a96c3a6c3c9b50531e3b02e50b2baeba06e3a7c9fe24374e48091006b11f7
  description: ''
  type: local_keystore
  voting_keystore_path: /var/lib/lighthouse_validator/validators/0xa1a6d4a72db3fff3a81401cb9a80e1a7d18a96c3a6c3c9b50531e3b02e50b2baeba06e3a7c9fe24374e48091006b11f7/keystore-m>
  voting_keystore_password: password
  suggested_fee_recipient: "0x388C818CA8B9251b393131C08a736A67ccB19297"
- enabled: true
  voting_public_key: 0x8e2a8abfeeca058d757f6d6ff5c058c61ab1d40804c28298d44bfe4486265c96e5d30f781a2a3cee6e2b250a85c40e38
  description: ''
  type: local_keystore
  voting_keystore_path: /var/lib/lighthouse_validator/validators/0x8e2a8abfeeca058d757f6d6ff5c058c61ab1d40804c28298d44bfe4486265c96e5d30f781a2a3cee6e2b250a85c40e38/keystore-m>
  voting_keystore_password: password
  suggested_fee_recipient: "0x388C818CA8B9251b393131C08a736A67ccB19297"
```

Restart your Lighthouse validator client.

```sh
sudo systemctl start lighthousevalidator.service
sudo systemctl status lighthousevalidator.service
```

Monitor for errors.

```sh
sudo journalctl -fu lighthousevalidator -o cat | ccze -A
```
{% endtab %}

{% tab title="Prysm" %}
Assuming your Prysm validator client is already set up, stop your Prysm validator client.

```sh
sudo systemctl stop prysmvalidator.service
```

Find the pubkey values of your own <mark style="color:red;">**non-CSM**</mark> validator keystores generated.

```sh
sudo find /var/lib -name "keystore*.json"
```

For each `resulting filepath`, run:

```sh
grep -oP '"pubkey": *"\K[^"]+' RESULTING_FILEPATH
```

_**You should now have a list of all your own**** **<mark style="color:red;">**non-CSM**</mark>** ****validator keystore pubkeys.**_

Create a custom proposer configuration file.

```sh
sudo nano /var/lib/prysm_validator/validator/proposer_configuration.json
```

Paste the following contents into the file.

```
{
  "proposer_config": {
    "YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)_01": {
      "fee_recipient": "YOUR_OWN_FEE_RECIPIENT_ADDRESS",
      "builder": {
        "enabled": true
      }
    },
    "YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)_02": {
      "fee_recipient": "YOUR_OWN_FEE_RECIPIENT_ADDRESS",
      "builder": {
        "enabled": true
      }
    },
    "YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)_03": {
      "fee_recipient": "YOUR_OWN_FEE_RECIPIENT_ADDRESS",
      "builder": {
        "enabled": true
      }
    }
  },
  "default_config": {
    "fee_recipient": "LIDO_EXECUTION_LAYER_REWARDS_VAULT",
    "builder": {
      "enabled": true
    }
  }
}
```

**Replace** `YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)` with your own actual validator pubkeys <mark style="color:red;">**(NOT CSM).**</mark>

**Replace** `YOUR_OWN_FEE_RECIPIENT_ADDRESS` with your desired wallet address.

**Replace** `LIDO_EXECUTION_LAYER_REWARDS_VAULT` with the following options.

* **Mainnet**

```
suggested_fee_recipient: "0x388C818CA8B9251b393131C08a736A67ccB19297"
```

* **Holesky**

```
suggested_fee_recipient: "0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8"
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Set the permissions of the custom proposer configuration file.

```sh
sudo chown -R prysm_validator:prysm_validator /var/lib/prysm_validator/validator
```

### Adding more <mark style="color:red;">non-CSM</mark> validator keystores:

If you want to add more of your own validator keystores, replicate the following segment, taking note of the indentation.

```
    "YOUR_OWN_VALIDATOR_PUBKEY_(NOT_CSM)_03": {
      "fee_recipient": "YOUR_OWN_FEE_RECIPIENT_ADDRESS",
      "builder": {
        "enabled": true
      }
    }
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Edit the Prysm validator client service file.

```
sudo nano /etc/systemd/system/prysmvalidator.service
```

Add the `--proposer-settings-file` flag and point it to the `proposer_configuration.json` file. Then remove the `--suggested-fee-recipient` flag. e.g.,

```
[Unit]
Description=Prysm Validator Client (Holesky)
Wants=network-online.target
After=network-online.target

[Service]
User=prysmvalidator
Group=prysmvalidator
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/prysmvalidator \
  --accept-terms-of-use \
  --holesky \
  --datadir=/var/lib/prysm_validator \
  --enable-builder \
  --beacon-rpc-provider=<Internal_IP_address>:4000 \
  --beacon-rpc-gateway-provider=<Internal_IP_address>:5051 \
  --wallet-dir=/var/lib/prysm_validator \
  --wallet-password-file=/var/lib/prysm_validator/password.txt \
  --monitoring-port=8108 \
  --proposer-settings-file=/var/lib/prysm_validator/validator/proposer_configuration.json \
  --graffiti="<your_graffiti>" \
  --enable-doppelganger

[Install]
WantedBy=multi-user.target
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Restart your Prysm validator client.

```sh
sudo systemctl daemon-reload
sudo systemctl start prysmvalidator.service
sudo systemctl status prysmvalidator.service
```

Monitor for errors.

```sh
sudo journalctl -fu prysmvalidator -o cat | ccze -A
```
{% endtab %}
{% endtabs %}

## Automation Tools

{% tabs %}
{% tab title="ETH Docker" %}
With ETH Docker running (i.e., `ethd up`), run&#x20;

```
./ethd keys list
```

then

```
./ethd keys set-recipient 0xPUBKEY 0xADDRESS 
```

with the public key of the key you wish to set a separate fee recipient for, and the Ethereum address fees should go to.
{% endtab %}

{% tab title="ETH Pillar" %}
Not straightforward. Designed to assign custom `fee_recipient` addresses by running a separate validator client. Refer to the subpage below, under **"Automation Tools"**

{% content-ref url="method-2-configure-on-separate-validator-client.md" %}
[method-2-configure-on-separate-validator-client.md](method-2-configure-on-separate-validator-client.md)
{% endcontent-ref %}
{% endtab %}
{% endtabs %}
