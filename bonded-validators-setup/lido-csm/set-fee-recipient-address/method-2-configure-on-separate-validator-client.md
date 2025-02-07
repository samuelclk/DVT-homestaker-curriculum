# Method 2: Configure on separate validator client

## Pre-requisites

Make sure you have downloaded the necessary files according to your choice of validator client. Otherwise, revisit the following pages to download and move them into the `/usr/local/bin` directory.

{% tabs %}
{% tab title="Teku" %}
{% content-ref url="../../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/teku-bn.md" %}
[teku-bn.md](../../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/teku-bn.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Nimbus" %}
{% content-ref url="../../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/nimbus-bn.md" %}
[nimbus-bn.md](../../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/nimbus-bn.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Lodestar" %}
As we will be using Docker to run the Lodestar VC, we only need to install Docker at this point, and the actual Lodestar binary files will be downloaded when spinning up the Docker container.

## Installing dependencies - Docker

The script below performs the following:

1. Download and run the official Docker installation script
2. Creates a new user group called "docker"
3. Adds your current Linux user account to this new docker group

```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

sudo groupadd docker
sudo usermod -aG docker $USER
```

Log out and then back in again for the new user group settings to take effect.

```sh
exit
```

```sh
ssh <user>@<IP_address> -p <port_no.> -i <SSH_key> -v
```
{% endtab %}

{% tab title="Lighthouse" %}
{% content-ref url="../../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/lighthouse-bn.md" %}
[lighthouse-bn.md](../../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/lighthouse-bn.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Prysm" %}
{% content-ref url="../../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/prysm-bn.md" %}
[prysm-bn.md](../../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/prysm-bn.md)
{% endcontent-ref %}

{% hint style="info" %}
The Prysm validator client only works with a Prysm Consensus Client.
{% endhint %}
{% endtab %}
{% endtabs %}

{% hint style="info" %}
The Validator Client files are downloaded from the same sources as their respective Consensus Clients.
{% endhint %}

## Create new CSM user

{% tabs %}
{% tab title="Teku" %}
```sh
sudo useradd --no-create-home --shell /bin/false csm_teku_validator
```
{% endtab %}

{% tab title="Nimbus" %}
```sh
sudo useradd --no-create-home --shell /bin/false csm_nimbus_validator
```
{% endtab %}

{% tab title="Lodestar" %}
```sh
sudo useradd --no-create-home --shell /bin/false csm_lodestar_validator
```
{% endtab %}

{% tab title="Lighthouse" %}
```sh
sudo useradd --no-create-home --shell /bin/false csm_lighthouse_validator
```
{% endtab %}

{% tab title="Prysm" %}
```sh
sudo useradd --no-create-home --shell /bin/false csm_prysm_validator
```
{% endtab %}
{% endtabs %}

By clearly segregating the users and permissions for separate services in your workflow, this will provide additional safeguards against operational mistakes that can lead to slashing via double signing.

## Create new folders for CSM data & keys

Create separate folders, import the CSM validator keys, and set appropriate permissions.

{% tabs %}
{% tab title="Teku" %}
### Prepare the CSM validator keystores

1\) Create 3 new folders to store the validator client data, validator keystore, and the validator keystore password

2\) Copy the validator keystores and it's password file into their respective folders

3\) Change the owner of this folder to the teku user

4\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

```sh
sudo mkdir -p /var/lib/csm_teku_validator/validator_keystores /var/lib/csm_teku_validator/keystore_password
sudo cp ~/validator_keys/<validator_keystore.json> /var/lib/csm_teku_validator/validator_keystores
sudo cp ~/validator_keys/<validator_keystore_password.txt> /var/lib/csm_teku_validator/keystore_password
sudo chown -R csm_teku_validator:csm_teku_validator /var/lib/csm_teku_validator
sudo chmod 700 /var/lib/csm_teku_validator
```

{% hint style="info" %}
**Aside from the file extension, the validator\_keystore\_password file will need to be named identically as the validator signing keystore file (e.g. keystore-m-123.json, keystore-m-123.txt)**
{% endhint %}
{% endtab %}

{% tab title="Nimbus" %}
### Prepare the CSM validator keystores

1\) Create a new folders to store the validator client data, validator keystore, and the validator keystore password

```sh
sudo mkdir -p /var/lib/csm_nimbus_validator
```

2\) Run the validator key import process.

<pre class="language-sh"><code class="lang-sh"><strong>sudo /usr/local/bin/nimbus_beacon_node deposits import --data-dir:/var/lib/csm_nimbus_validator/ ~/staking_deposit-cli*/validator_keys
</strong></code></pre>

3\) Change the owner of this new folder to the `csm_nimbus_validator` user

4\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

```sh
sudo chown -R csm_nimbus_validator:csm_nimbus_validator /var/lib/csm_nimbus_validator
sudo chmod 700 /var/lib/csm_nimbus_validator
```
{% endtab %}

{% tab title="Lodestar" %}
## Prepare the validator data directory

1\) Create 3 new folders to store the validator client data, validator keystore, and the validator keystore password

2\) Copy the validator keystores and it's password file into their respective folders

3\) Change the owner of these new folders to the `csm_lodestar_validator`user

4\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

5\) Retrieve the UID and GID of the `csm_lodestar_validator` user account to be used in your `docker-compose.yml` file in the next step

<pre class="language-sh"><code class="lang-sh">sudo mkdir -p /var/lib/csm_lodestar_validator/validator_keystores /var/lib/csm_lodestar_validator/keystore_password
<strong>sudo cp ~/validator_keys/&#x3C;validator_keystore.json> /var/lib/csm_lodestar_validator/validator_keystores
</strong>sudo cp ~/validator_keys/&#x3C;validator_keystore_password.txt> /var/lib/csm_lodestar_validator/keystore_password
sudo chown -R csm_lodestar_validator:csm_lodestar_validator /var/lib/csm_lodestar_validator
sudo chmod 700 /var/lib/csm_lodestar_validator
id csm_lodestar_validator
</code></pre>

{% hint style="info" %}
**Aside from the file extension, the validator\_keystore\_password file will need to be named identically as the validator signing keystore file (e.g. keystore-m-123.json, keystore-m-123.txt)**
{% endhint %}

_**Expected output:**_

```
uid=1004(csm_lodestar_validator) gid=1005(csm_lodestar_validator) groups=1005(csm_lodestar_validator)
```

_**New folders created:**_

```
/var/lib/csm_lodestar_validator/
/var/lib/csm_lodestar_validator/validator_keystores
/var/lib/csm_lodestar_validator/keystore_password
```
{% endtab %}

{% tab title="Lighthouse" %}
### Prepare the validator data directory

1\) Create a new folders to store the validator client data, validator keystore, and the validator keystore password

```sh
sudo mkdir -p /var/lib/csm_lighthouse_validator
```

2\) Run the validator key import process.

```sh
sudo lighthouse account validator import --network holesky --datadir /var/lib/csm_lighthouse_validator --directory=$HOME/staking_deposit-cli*/validator_keys
```

**Expected output:**

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

3\) Change the owner of this new folder to the `csm_lighthouse_validator` user

4\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

```sh
sudo chown -R csm_lighthouse_validator:csm_lighthouse_validator /var/lib/csm_lighthouse_validator
sudo chmod 700 /var/lib/csm_lighthouse_validator
```
{% endtab %}

{% tab title="Prysm" %}
1\) Create a new folders to store the validator client data, validator keystore, and the validator keystore password

```sh
sudo mkdir -p /var/lib/csm_prysm_validator
```

2\) Run the validator key import process.

```sh
sudo /usr/local/bin/prysmvalidator accounts import --keys-dir=$HOME/staking_deposit-cli*/validator_keys --wallet-dir=/var/lib/csm_prysm_validator --holesky
```

**Note:** You will be prompted to accept the terms of use, create a new password for the Prysm wallet, and enter the password of your validator keystore.

**Expected output:**

<figure><img src="../../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

3\) Create a plain text password file for the Prysm wallet

```sh
sudo nano /var/lib/csm_prysm_validator/password.txt
```

Enter the password you set during the validator keystore import process. Then, save + exit with `CTRL+O`, `ENTER`, `CTRL+C`.

4\) Change the owner of this new folder to the `csm_prysm_validator` user

5\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

```sh
sudo chown -R csm_prysmvalidator:csm_prysmvalidator /var/lib/csm_prysm_validator
sudo chmod 700 /var/lib/csm_prysm_validator
```
{% endtab %}
{% endtabs %}

## Configure the separate VC service

Create a new configuration file for your separate validator client.

{% tabs %}
{% tab title="Teku" %}
Create a systemd configuration file for the Teku Validator Client service to run in the background.

```sh
sudo nano /etc/systemd/system/csm_tekuvalidator.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=CSM Teku Validator Client
Wants=network-online.target
After=network-online.target
[Service]
User=csm_teku_validator
Group=csm_teku_validator
Type=simple
Restart=always
RestartSec=5
Environment="JAVA_OPTS=-Xmx8g"
Environment="TEKU_OPTS=-XX:-HeapDumpOnOutOfMemoryError"
ExecStart=/usr/local/bin/teku/bin/teku vc \
  --network=<holesky_or_mainnet> \
  --data-path=/var/lib/csm_teku_validator \
  --validator-keys=/var/lib/csm_teku_validator/validator_keystores:/var/lib/csm_teku_validator/keystore_password \
  --beacon-node-api-endpoint=http://<Internal_IP_address>:5051 \
  --validators-proposer-default-fee-recipient=<holesky_or_mainnet_fee_recipient_address> \
  --validators-proposer-blinded-blocks-enabled=true \
  --validators-builder-registration-default-enabled=true \
  --validators-graffiti="<your_graffiti>" \
  --metrics-enabled=true \
  --metrics-port=8108 \
  --doppelganger-detection-enabled=true 

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

{% hint style="info" %}
**Important:** Recall that you will have to use designated fee recipient addresses as a CSM operator.\
&#xNAN;**- Holesky:** [`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8)

**- Mainnet:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)
{% endhint %}

Refer to the native Teku validator client setup section for more information on the other flags used. &#x20;

{% content-ref url="../../../native-solo-staking-setup/validator-client-setup/teku-vc.md" %}
[teku-vc.md](../../../native-solo-staking-setup/validator-client-setup/teku-vc.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Nimbus" %}
Create a systemd configuration file for the Nimbus Validator Client service to run in the background.

```bash
sudo nano /etc/systemd/system/csm_nimbusvalidator.service
```

Paste the configuration parameters below into the file:

```
[Unit]
Description=CSM Nimbus Validator Client
Wants=network-online.target
After=network-online.target

[Service]
User=csm_nimbus_validator
Group=csm_nimbus_validator
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/nimbus_validator_client \
  --data-dir=/var/lib/csm_nimbus_validator \
  --payload-builder=true \
  --beacon-node=http://<Internal_IP_address>:5051 \
  --metrics \
  --metrics-port=8108 \
  --suggested-fee-recipient=<holesky_or_mainnet_fee_recipient_address> \
  --graffiti="<your_graffiti>" \
  --doppelganger-detection

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

{% hint style="info" %}
**Important:** Recall that you will have to use designated fee recipient addresses as a CSM operator.\
&#xNAN;**- Holesky:** [`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8)

**- Mainnet:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)
{% endhint %}

Refer to the native Nimbus validator client setup section for more information on the other flags used.

{% content-ref url="../../../native-solo-staking-setup/validator-client-setup/nimbus-vc.md" %}
[nimbus-vc.md](../../../native-solo-staking-setup/validator-client-setup/nimbus-vc.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Lodestar" %}
Create a new folder for the CSM Lodestar validator client.&#x20;

```sh
cd
sudo mkdir csm_lodestar_validator
```

Create a `docker-compose.yml` file in the Lodestar folder.

```sh
cd ~/csm_lodestar_validator
sudo nano docker-compose.yml
```

Paste the following configuration into the `docker-compose.yml` file. **Note:** This is similar to the `systemd` configuration file used in the setup of other clients in this curriculum.

```yaml
services:
  validator_client:
    image: chainsafe/lodestar:latest
    container_name: csm_lodestar_validator
    user: <UID>:<GID> #replace with the actual UID and GID of the csm_lodestar_validator user
    restart: unless-stopped
    volumes:
      - /var/lib/csm_lodestar_validator:/var/lib/csm_lodestar_validator
    command:
      - validator
      - --dataDir
      - /var/lib/csm_lodestar_validator
      - --importKeystores
      - /var/lib/csm_lodestar_validator/validator_keystores
      - --importKeystoresPassword
      - /var/lib/csm_lodestar_validator/keystore_password/<validator_signing_keystore_password_file_name>.txt
      - --network
      - <holesky_or_mainnet>
      - --beaconNodes
      - http://<Internal_IP_address>:5051
      - --builder
      - --builder.boostFactor
      - 100
      - --suggestedFeeRecipient
      - "<holesky_or_mainnet_fee_recipient_address>"
      - --doppelgangerProtection
      - --metrics
      - --metrics.port
      - "5064"
      - --graffiti
      - "your_graffiti"
    environment:
      NODE_OPTIONS: --max-old-space-size=2048
    ports:
      - "5064:5064"
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.&#x20;

{% hint style="info" %}
**Important:** Recall that you will have to use designated fee recipient addresses as a CSM operator.\
&#xNAN;**- Holesky:** [`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8)

**- Mainnet:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)
{% endhint %}

Refer to the native Lodestar validator client setup section for more information on the other flags used.

{% content-ref url="../../../native-solo-staking-setup/validator-client-setup/lodestar-vc.md" %}
[lodestar-vc.md](../../../native-solo-staking-setup/validator-client-setup/lodestar-vc.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Lighthouse" %}
Create a systemd configuration file for the Lighthouse Validator Client service to run in the background.

```bash
sudo nano /etc/systemd/system/csm_lighthousevalidator.service
```

Paste the configuration parameters below into the file:

```
[Unit]
Description=CSM Lighthouse Validator Client (Holesky)
Wants=network-online.target
After=network-online.target

[Service]
User=csm_lighthouse_validator
Group=csm_lighthouse_validator
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/lighthouse vc \
  --network <holesky_or_mainnet> \
  --datadir /var/lib/csm_lighthouse_validator \
  --builder-proposals \
  --builder-boost-factor 100 \
  --beacon-nodes http://<Internal_IP_address>:5051 \
  --metrics \
  --metrics-port 8108 \
  --suggested-fee-recipient <holesky_or_mainnet_fee_recipient_address> \
  --graffiti="<your_graffiti>" \
  --enable-doppelganger-protection

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

{% hint style="info" %}
**Important:** Recall that you will have to use designated fee recipient addresses as a CSM operator.\
&#xNAN;**- Holesky:** [`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8)

**- Mainnet:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)
{% endhint %}

Refer to the native Lighthouse validator client setup section for more information on the other flags used.

{% content-ref url="../../../native-solo-staking-setup/validator-client-setup/lighthouse-vc.md" %}
[lighthouse-vc.md](../../../native-solo-staking-setup/validator-client-setup/lighthouse-vc.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Prysm" %}
Create a systemd configuration file for the Lighthouse Validator Client service to run in the background.

```bash
sudo nano /etc/systemd/system/csm_prysmvalidator.service
```

Paste the configuration parameters below into the file:

```
[Unit]
Description=CSM Prysm Validator Client (Holesky)
Wants=network-online.target
After=network-online.target

[Service]
User=csm_prysm_validator
Group=csm_prysm_validator
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/prysmvalidator \
  --accept-terms-of-use \
  --<holesky_or_mainnet> \
  --datadir=/var/lib/csm_prysm_validator \
  --enable-builder \
  --beacon-rpc-provider=<Internal_IP_address>:4000 \
  --beacon-rpc-gateway-provider=<Internal_IP_address>:5051 \
  --wallet-dir=/var/lib/csm_prysm_validator \
  --wallet-password-file=/var/lib/csm_prysm_validator/password.txt \
  --monitoring-port=8108 \
  --suggested-fee-recipient=<holesky_or_mainnet_fee_recipient_address> \
  --graffiti="<your_graffiti>" \
  --enable-doppelganger

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

{% hint style="info" %}
**Important:** Recall that you will have to use designated fee recipient addresses as a CSM operator.\
&#xNAN;**- Holesky:** [`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8)

**- Mainnet:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)
{% endhint %}

Refer to the native Lighthouse validator client setup section for more information on the other flags used.

{% content-ref url="../../../native-solo-staking-setup/validator-client-setup/prysm-vc.md" %}
[prysm-vc.md](../../../native-solo-staking-setup/validator-client-setup/prysm-vc.md)
{% endcontent-ref %}
{% endtab %}
{% endtabs %}

## Start the CSM Validator Client

Create a new configuration file for your separate validator client.

{% tabs %}
{% tab title="Teku" %}
Reload the systemd daemon to register the changes made, start the Teku Validator Client, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start csm_tekuvalidator.service
sudo systemctl status csm_tekuvalidator.service
```

The output should say the Teku Validator Client is **“active (running)”.** Press CTRL-C to exit and the Teku Validator Client will continue to run.

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu csm_tekuvalidator -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../../.gitbook/assets/image (55).png" alt=""><figcaption><p>Example output of the Teku VC running on the Goerli testnet. Look out for Holesky in your output.</p></figcaption></figure>

Press `CTRL-C` to exit.

If the Teku Validator Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable csm_tekuvalidator
```

**Expected output:**

```
Created symlink /etc/systemd/system/multi-user.target.wants/csm_tekuvalidator.service → /etc/s
```

### Remove duplicates of validator keystores

To prevent configuration mistakes leading to double signing in the future, remove duplicate copies of the validator signing keystores once everything is running smoothly.

```sh
sudo rm -r ~/staking_deposit-cli*/validator_keys
```
{% endtab %}

{% tab title="Nimbus" %}
Reload the systemd daemon to register the changes made, start the Nimbus Validator Client, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start csm_nimbusvalidator.service
sudo systemctl status csm_nimbusvalidator.service
```

The output should say the Nimbus Validator Client is **“active (running)”.** Press CTRL-C to exit and the Nimbus Validator Client will continue to run.

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu csm_nimbusvalidator -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

Press `CTRL-C` to exit.

If the Nimbus Validator Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable csm_nimbusvalidator
```

**Expected output:**

```
Created symlink /etc/systemd/system/multi-user.target.wants/csm_nimbusvalidator.service → /etc/systemd/system/csm_nimbusvalidator.service.
```

### Remove duplicates of validator keystores

To prevent configuration mistakes leading to double signing in the future, remove duplicate copies of the validator signing keystores once everything is running smoothly.

```sh
sudo rm -r ~/staking_deposit-cli*/validator_keys
```
{% endtab %}

{% tab title="Lodestar" %}
1\) Make sure you are in the same folder as the `docker-compose.yml` file you created earlier.

```sh
cd ~/csm_lodestar_validator
```

&#x20;2\) Start the docker container.

```sh
docker compose up -d
```

**Expected output:**

<figure><img src="../../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

3\) Make sure there are no error messages by monitoring the logs for a few minutes.

```sh
docker logs csm_lodestar_validator -f
```

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Remove duplicates of validator keystores

To prevent configuration mistakes leading to double signing in the future, remove duplicate copies of the validator signing keystores once everything is running smoothly.

```sh
sudo rm -r ~/staking_deposit-cli*/validator_keys
```
{% endtab %}

{% tab title="Lighthouse" %}
Reload the systemd daemon to register the changes made, start the Lighthouse Validator Client, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start csm_lighthousevalidator.service
sudo systemctl status csm_lighthousevalidator.service
```

The output should say the Lighthouse Validator Client is **“active (running)”.** Press CTRL-C to exit and the Lighthouse Validator Client will continue to run.

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu csm_lighthousevalidator -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
You will see some warnings if your beacon node (consensus client) is not yet synced.
{% endhint %}

Press `CTRL-C` to exit.

If the Lighthouse Validator Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable csm_lighthousevalidator
```

**Expected output:**

```
Created symlink /etc/systemd/system/multi-user.target.wants/csm_lighthousevalidator.service → /etc/systemd/system/csm_lighthousevalidator.service.
```

### Remove duplicates of validator keystores

To prevent configuration mistakes leading to double signing in the future, remove duplicate copies of the validator signing keystores once everything is running smoothly.

```sh
sudo rm -r ~/staking_deposit-cli*/validator_keys
```
{% endtab %}

{% tab title="Prysm" %}
Reload the systemd daemon to register the changes made, start the Prysm Validator Client, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start csm_prysmvalidator.service
sudo systemctl status csm_prysmvalidator.service
```

The output should say the Prysm Validator Client is **“active (running)”.** Press CTRL-C to exit and the Prysm Validator Client will continue to run.

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu csm_prysmvalidator -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
You will see some warnings if your beacon node (consensus client) is not yet synced.
{% endhint %}

Press `CTRL-C` to exit.

If the Prysm Validator Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable csm_prysmvalidator
```

**Expected output:**

```
Created symlink /etc/systemd/system/multi-user.target.wants/csm_prysmvalidator.service → /etc/systemd/system/csm_prysmvalidator.service.
```

### Remove duplicates of validator keystores

To prevent configuration mistakes leading to double signing in the future, remove duplicate copies of the validator signing keystores once everything is running smoothly.

```sh
sudo rm -r ~/staking_deposit-cli*/validator_keys
```
{% endtab %}
{% endtabs %}

## Automation Tools

{% tabs %}
{% tab title="ETH Pillar" %}
{% hint style="warning" %}
Only 1 instance of ETH Pillar can be running per device. If you are already using ETH Pillar for your own validator node setup, then you will need to use any of the other methods (e.g., ETH Docker) listed in this subpage to import your CSM validator keystores.
{% endhint %}

Select `4 - Lido CSM Validator Client Only`.

<figure><img src="../../../.gitbook/assets/image (203).png" alt=""><figcaption></figcaption></figure>

Enter your consensus client (beacon node) address. **Example:** http://127.0.0.1:5052

Verify the `fee_recipient` address is set to the `Lido Execution Layer Rewards Vault`.

* **Holesky:** [`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8)
* **Mainnet:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)

Generate and import CSM validator keys.
{% endtab %}

{% tab title="ETH Docker" %}
ETH Docker sets the `fee_recipient` address on the validator key level. Refer to the subpage below, under **"Automation Tools".**

{% content-ref url="method-1-configure-on-validator-keys.md" %}
[method-1-configure-on-validator-keys.md](method-1-configure-on-validator-keys.md)
{% endcontent-ref %}
{% endtab %}
{% endtabs %}

## Resources

{% tabs %}
{% tab title="Teku" %}
* Releases: [https://github.com/Consensys/teku/releases](https://github.com/Consensys/teku/releases)
* Documentation: [https://docs.teku.consensys.io/introduction](https://docs.teku.consensys.io/introduction)
* Discord: [https://discord.gg/consensys](https://discord.gg/consensys) (Select the Teku channel)
{% endtab %}

{% tab title="Nimbus" %}
* Releases: [https://github.com/status-im/nimbus-eth2/releases](https://github.com/status-im/nimbus-eth2/releases)
* Documentation: [https://nimbus.guide/install.html](https://nimbus.guide/install.html)
* Discord: [https://discord.gg/BWKx5Xta](https://discord.gg/BWKx5Xta)
{% endtab %}

{% tab title="Lodestar" %}
* Git repository: [https://github.com/ChainSafe/lodestar-quickstart.git](https://github.com/ChainSafe/lodestar-quickstart.git)
* Documentation: [https://chainsafe.github.io/lodestar/](https://chainsafe.github.io/lodestar/)
* Discord: [https://discord.gg/7Gdb4nFh](https://discord.gg/7Gdb4nFh)
{% endtab %}

{% tab title="Lighthouse" %}
* Releases: [https://github.com/sigp/lighthouse/releases](https://github.com/sigp/lighthouse/releases)
* Documentation: [https://lighthouse-book.sigmaprime.io/intro.html](https://lighthouse-book.sigmaprime.io/intro.html)
* Discord: [https://discord.com/invite/TX7HKfgJN3](https://discord.com/invite/TX7HKfgJN3)
{% endtab %}

{% tab title="Prysm" %}
* Releases: [https://github.com/prysmaticlabs/prysm/releases](https://github.com/prysmaticlabs/prysm/releases)
* Documentation: [https://docs.prylabs.network/docs/getting-started](https://docs.prylabs.network/docs/getting-started)
* Discord: [https://discord.gg/prysmaticlabs](https://discord.gg/prysmaticlabs)
{% endtab %}
{% endtabs %}
