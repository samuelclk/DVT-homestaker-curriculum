# Prysm VC

{% hint style="info" %}
The Prysm validator client only works with a Prysm Consensus Client.
{% endhint %}

### Download Prysm

[Download](https://github.com/prysmaticlabs/prysm/releases) the latest version of the Prysm validator client.

```bash
cd
curl -LO https://github.com/prysmaticlabs/prysm/releases/download/v5.1.2/validator-v5.1.2-linux-amd64
curl -LO https://github.com/prysmaticlabs/prysm/releases/download/v5.1.2/validator-v5.1.2-linux-amd64.sha256
```

Run the checksum verification process.

```sh
sha256sum --check validator-v5.1.2-linux-amd64.sha256
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum. Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

_**Expected output:** Verify output of the checksum verification._

```
validator-v5.1.2-linux-amd64: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

<pre class="language-bash"><code class="lang-bash">mv validator-v5.1.2-linux-amd64 prysmvalidator #rename the binary file for easy reference
chmod +x prysmvalidator
<strong>sudo cp prysmvalidator /usr/local/bin
</strong><strong>rm -r prysmvalidator validator-v5.1.2-linux-amd64.sha256
</strong></code></pre>

### Create a new user account

```sh
sudo useradd --no-create-home --shell /bin/false prysmvalidator
```

### Prepare the validator data directory

1\) Create a new folders to store the validator client data, validator keystore, and the validator keystore password

```sh
sudo mkdir -p /var/lib/prysm_validator
```

2\) Run the validator key import process.

```sh
sudo /usr/local/bin/prysmvalidator accounts import --keys-dir=$HOME/validator_keys --wallet-dir=/var/lib/prysm_validator --holesky
```

**Note:** You will be prompted to accept the terms of use, create a new password for the Prysm wallet, and enter the password of your validator keystore.

**Expected output:**

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

3\) Create a plain text password file for the Prysm wallet

```sh
sudo nano /var/lib/prysm_validator/password.txt
```

Enter the password you set during the validator keystore import process. Then, save + exit with `CTRL+O`, `ENTER`, `CTRL+C`.

4\) Change the owner of this new folder to the `prysmvalidator` user

5\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

```sh
sudo chown -R prysmvalidator:prysmvalidator /var/lib/prysm_validator
sudo chmod 700 /var/lib/prysm_validator
```

### Configure the validator client service

Create a systemd configuration file for the Lighthouse Validator Client service to run in the background.

```bash
sudo nano /etc/systemd/system/prysmvalidator.service
```

Paste the configuration parameters below into the file:

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
  --beacon-rpc-gateway-provider=<Internal_IP_address>:5052 \
  --wallet-dir=/var/lib/prysm_validator \
  --wallet-password-file=/var/lib/prysm_validator/password.txt \
  --monitoring-port=8108 \
  --suggested-fee-recipient=<your_designated_ETH_wallet address> \
  --graffiti="<your_graffiti>" \
  --enable-doppelganger

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Lighthouse Validator Client configuration summary:**

1. `--accept-terms-of-use`: Accept the terms and conditions.
2. `--holesky`: Run the validator client on the Holesky testnet
3. `--datadir`: Specify the directory for Lighthouse to store the validator info
4. `--enable-builder`: Required when using external builders to build blocks (e.g. MEV relays)
5. `--beacon-rpc-provider/beacon-rpc-gateway-provider`: URLs to connect to the main and backup _**Prysm**_ consensus clients if any. This needs to be the same IP address set in your _**Prysm**_ consensus client. Refer back [here](../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/) if you don't remember it. Use multiple comma-separated endpoints here to configure fallback beacon nodes for your validator. &#x20;
6. `--wallet-dir`: Path to a wallet directory on-disk for Prysm validator accounts
7. `--wallet-password-file`: Path to a plain-text, .txt file containing your wallet password
8. `--monitoring-port`: Set the port for retrieving metrics
9. `--suggested-fee-recipient`: ETH wallet address to receive rewards from block proposals and MEV bribes
10. `--graffiti`: Optional text to display on-chain when your validator proposes a block
11. `--enable-doppelganger`: Helps prevents slashing due to double signing by checking if your validator keys are already active on the network. _**Not a fool-proof solution.**_

### Start the Prysm Validator Client service

Reload the systemd daemon to register the changes made, start the Prysm Validator Client, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start prysmvalidator.service
sudo systemctl status prysmvalidator.service
```

The output should say the Prysm Validator Client is **“active (running)”.** Press CTRL-C to exit and the Prysm Validator Client will continue to run.

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu prysmvalidator -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
You will see some warnings if your beacon node (consensus client) is not yet synced.
{% endhint %}

Press `CTRL-C` to exit.

If the Prysm Validator Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable prysmvalidator
```

**Expected output:**

```
Created symlink /etc/systemd/system/multi-user.target.wants/prysmvalidator.service → /etc/systemd/system/prysmvalidator.service.
```

### Remove duplicates of validator keystores

To prevent configuration mistakes leading to double signing in the future, remove duplicate copies of the validator signing keystores once everything is running smoothly.

```sh
sudo rm -r ~/validator_keys
```

## Resources

* Releases: [https://github.com/prysmaticlabs/prysm/releases](https://github.com/prysmaticlabs/prysm/releases)
* Documentation: [https://docs.prylabs.network/docs/getting-started](https://docs.prylabs.network/docs/getting-started)
* Discord: [https://discord.gg/prysmaticlabs](https://discord.gg/prysmaticlabs)
