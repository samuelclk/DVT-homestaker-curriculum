# Running a separate VC service

## Pre-requisites

Make sure you have downloaded the necessary files according to your choice of validator client. Otherwise, revisit the following pages to download and move them into the `/usr/local/bin` directory.

{% tabs %}
{% tab title="Teku" %}
{% content-ref url="../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/teku-bn.md" %}
[teku-bn.md](../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/teku-bn.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Nimbus" %}
{% content-ref url="../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/nimbus-bn.md" %}
[nimbus-bn.md](../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/nimbus-bn.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Lodestar" %}
{% content-ref url="../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/lodestar-bn.md" %}
[lodestar-bn.md](../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/lodestar-bn.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Lighthouse" %}
{% content-ref url="../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/lighthouse-bn.md" %}
[lighthouse-bn.md](../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/lighthouse-bn.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Prysm" %}
{% content-ref url="../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/prysm-bn.md" %}
[prysm-bn.md](../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/prysm-bn.md)
{% endcontent-ref %}
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

<pre class="language-sh"><code class="lang-sh"><strong>sudo /usr/local/bin/nimbus_beacon_node deposits import --data-dir:/var/lib/csm_nimbus_validator/ ~/validator_keys
</strong></code></pre>

3\) Change the owner of this new folder to the `csm_nimbus_validator` user

4\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

```sh
sudo chown -R nimbusvalidator:nimbusvalidator /var/lib/nimbus_validator
sudo chmod 700 /var/lib/nimbus_validator
```
{% endtab %}

{% tab title="Untitled" %}
### Prepare the validator keystores

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
Description=CSM Teku Validator Client (Holesky)
Wants=network-online.target
After=network-online.target
[Service]
User=csm_teku_validator
Group=csm_teku_validator
Type=simple
Restart=always
RestartSec=5
Environment="JAVA_OPTS=-Xmx6g"
Environment="TEKU_OPTS=-XX:-HeapDumpOnOutOfMemoryError"
ExecStart=/usr/local/bin/teku/bin/teku vc \
  --network=holesky \
  --data-path=/var/lib/csm_teku_validator \
  --validator-keys=/var/lib/csm_teku_validator/validator_keystores:/var/lib/csm_teku_validator/keystore_password \
  --beacon-node-api-endpoint=http://<Internal_IP_address>:5051 \
  --validators-proposer-default-fee-recipient=0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8 \
  --validators-proposer-blinded-blocks-enabled=true \
  --validators-graffiti="<your_graffiti_of_choice>" \
  --metrics-enabled=true \
  --metrics-port=8108 \
  --doppelganger-detection-enabled=true 

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

{% hint style="info" %}
**Important:** Recall that you will have to use a designated fee recipient address ([`0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8`](https://holesky.etherscan.io/address/0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8)`)` as a CSM operator
{% endhint %}

Refer to the native Teku validator client setup section for more information on the flags used. &#x20;

{% content-ref url="../../native-solo-staking-setup/validator-client-setup/teku-vc.md" %}
[teku-vc.md](../../native-solo-staking-setup/validator-client-setup/teku-vc.md)
{% endcontent-ref %}
{% endtab %}

{% tab title="Nimbus" %}
```sh
sudo nano /etc/systemd/system/csm_nimbusvalidator.service
```
{% endtab %}

{% tab title="Lodestar" %}
```sh
sudo mkdir ~/csm_lodestar_validator
cd ~/csm_lodestar_validator
sudo nano docker-compose.yml
```
{% endtab %}

{% tab title="Lighthouse" %}
```sh
sudo nano /etc/systemd/system/csm_prysmvalidator.service
```
{% endtab %}

{% tab title="Prysm" %}
```sh
sudo nano /etc/systemd/system/csm_tekuvalidator.service
```
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

<figure><img src="../../.gitbook/assets/image (55).png" alt=""><figcaption><p>Example output of the Teku VC running on the Goerli testnet. Look out for Holesky in your output.</p></figcaption></figure>

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
sudo rm -r ~/validator_keys
```
{% endtab %}

{% tab title="Nimbus" %}
```sh
sudo nano /etc/systemd/system/csm_nimbusvalidator.service
```
{% endtab %}

{% tab title="Lodestar" %}
```sh
sudo mkdir ~/csm_lodestar_validator
cd ~/csm_lodestar_validator
sudo nano docker-compose.yml
```
{% endtab %}

{% tab title="Lighthouse" %}
```sh
sudo nano /etc/systemd/system/csm_prysmvalidator.service
```
{% endtab %}

{% tab title="Prysm" %}
```sh
sudo nano /etc/systemd/system/csm_tekuvalidator.service
```
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
```sh
sudo useradd --no-create-home --shell /bin/false csm_nimbus_validator
```
{% endtab %}

{% tab title="Lodestar" %}

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

###

