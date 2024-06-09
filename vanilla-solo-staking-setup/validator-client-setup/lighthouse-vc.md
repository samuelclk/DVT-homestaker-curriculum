# Lighthouse VC

### Download Lighthouse

Follow the steps in this previous section to download Lighthouse if you have not done so.

{% content-ref url="../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/lighthouse-bn.md" %}
[lighthouse-bn.md](../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/lighthouse-bn.md)
{% endcontent-ref %}

### Create a new user account

```sh
sudo useradd --no-create-home --shell /bin/false lighthousevalidator
```

### Prepare the validator data directory

1\) Create a new folders to store the validator client data, validator keystore, and the validator keystore password

```sh
sudo mkdir -p /var/lib/lighthouse_validator
```

2\) Run the validator key import process.

```sh
sudo lighthouse account validator import --network holesky --datadir /var/lib/lighthouse_validator --directory=$HOME/validator_keys
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

3\) Change the owner of this new folder to the `lighthousevalidator` user

4\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

```sh
sudo chown -R lighthousevalidator:lighthousevalidator /var/lib/lighthouse_validator
sudo chmod 700 /var/lib/lighthouse_validator
```

### Configure the validator client service

Create a systemd configuration file for the Lighthouse Validator Client service to run in the background.

```bash
sudo nano /etc/systemd/system/lighthousevalidator.service
```

Paste the configuration parameters below into the file:

```
[Unit]
Description=Lighthouse Validator Client (Holesky)
Wants=network-online.target
After=network-online.target

[Service]
User=lighthousevalidator
Group=lighthousevalidator
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/lighthouse vc \
  --network holesky \
  --datadir /var/lib/lighthouse_validator \
  --builder-proposals \
  --beacon-nodes http://<Internal_IP_address>:5051 \
  --metrics \
  --metrics-port 8108 \
  --suggested-fee-recipient <your_designated_ETH_wallet address> \
  --graffiti="<your_graffiti>" \
  --enable-doppelganger-protection

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Lighthouse Validator Client configuration summary:**

1. `--network`: Run the validator client on the Holesky testnet
2. `--data-dir`: Specify the directory for Lighthouse to store the validator info
3. `--builder-proposals`: Required when using external builders to build blocks (e.g. MEV relays)
4. `--beacon-nodes`: URLs to connect to the main and backup consensus clients if any. This needs to be the same IP address set in your consensus client. Refer back [here](../../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client/) if you don't remember it. Use multiple comma-separated endpoints here to configure fallback beacon nodes for your validator.&#x20;
5. `--metrics`: Enable metrics for monitoring
6. `--metrics-port`: Set the port for retrieving metrics
7. `--suggested-fee-recipient`: ETH wallet address to receive rewards from block proposals and MEV bribes
8. `--graffiti`: Optional text to display on-chain when your validator proposes a block
9. `--enable-doppelganger-protection`: Helps prevents slashing due to double signing by checking if your validator keys are already active on the network. _**Not a fool-proof solution.**_

### Start the Lighthouse Validator Client service

Reload the systemd daemon to register the changes made, start the Lighthouse Validator Client, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start lighthousevalidator.service
sudo systemctl status lighthousevalidator.service
```

The output should say the Lighthouse Validator Client is **“active (running)”.** Press CTRL-C to exit and the Lighthouse Validator Client will continue to run.

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu lighthousevalidator -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
You will see some warnings if your beacon node (consensus client) is not yet synced.
{% endhint %}

Press `CTRL-C` to exit.

If the Lighthouse Validator Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable lighthousevalidator
```

**Expected output:**

```
Created symlink /etc/systemd/system/multi-user.target.wants/lighthousevalidator.service → /etc/systemd/system/lighthousevalidator.service.
```

### Remove duplicates of validator keystores

To prevent configuration mistakes leading to double signing in the future, remove duplicate copies of the validator signing keystores once everything is running smoothly.

```sh
sudo rm -r ~/validator_keys
```

## Resources

* Releases: [https://github.com/sigp/lighthouse/releases](https://github.com/sigp/lighthouse/releases)
* Documentation: [https://lighthouse-book.sigmaprime.io/intro.html](https://lighthouse-book.sigmaprime.io/intro.html)
* Discord: [https://discord.com/invite/TX7HKfgJN3](https://discord.com/invite/TX7HKfgJN3)
