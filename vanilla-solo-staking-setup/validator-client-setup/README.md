# Validator client setup

## Teku

### Configure the validator client service

Create a systemd configuration file for the Teku Validator Client service to run in the background.

```bash
sudo nano /etc/systemd/system/tekuvalidator.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Teku Validator Client (Mainnet)
Wants=network-online.target
After=network-online.target
[Service]
User=teku
Group=teku
Type=simple
Restart=always
RestartSec=5
Environment="JAVA_OPTS=-Xmx6g"
Environment="TEKU_OPTS=-XX:-HeapDumpOnOutOfMemoryError"
ExecStart=/usr/local/bin/teku/bin/teku vc \
  --network=mainnet \
  --data-path=/var/lib/teku \
  --validator-keys=/var/lib/teku/validator_keys:/var/lib/teku/validator_keys \
  --beacon-node-api-endpoint=http://localhost:5051,http://<backup_beacon_node>:<http/rest_port_number>
  --validators-proposer-default-fee-recipient=<designated wallet address> \
  --validators-proposer-blinded-blocks-enabled=true \
  --validators-graffiti="<yourgraffiti>" \
  --doppelganger-detection-enabled=true 

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Teku Validator Client configuration summary:**

1. `--network`: Run the validator client service on the ETH mainnet
2. `--data-path`: Specify the directory for Teku to store the validator info
3. `--validators-external-signer-public-keys`: Public keys of the validators set up for remote signing
4.  `--validator-keys`: File path to the directory where your validator signing keystore and corresponding plain text password file are stored. **Aside from the file extension (e.g. .json vs .txt), the password file will need to be named identically as the validator signing keystore file.** For example:

    <figure><img src="../../.gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>
5. `--beacon-node-api-endpoint`: URLs to connect to the main and backup beacon nodes if any. You may refer to an experimental backup beacon node setup using Google's blockchain node engine [here](broken-reference).
6. `--validators-proposer-default-fee-recipient`: ETH wallet address to receive rewards from block proposals and MEV bribes
7. `--validators-proposer-blinded-blocks-enabled`: Required when using external builders to build blocks (e.g. MEV relays)
8. `--validators-graffiti`: Optional text to display on-chain when your validator proposes a block
9. `--metrics-enabled`: Enable monitoring of beacon node metrics
10. `--doppelganger-detection-enabled`: Helps prevents slashing due to double signing by checking if your validator keys are already active on the network. _**Not a fool-proof solution.**_

### Start the Teku Validator Client service

Reload the systemd daemon to register the changes made, start the Teku Validator Client, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start tekuvalidator.service
sudo systemctl status tekuvalidator.service
```

The output should say the Teku Validator Client is **“active (running)”.** Press CTRL-C to exit and the Teku Validator Client will continue to run.

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu tekuvalidator -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (55).png" alt=""><figcaption></figcaption></figure>

Press `CTRL-C` to exit.

If the Teku Validator Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable tekuvalidator
```

**Expected output:**

```
Created symlink /etc/systemd/system/multi-user.target.wants/tekuvalidator.service → /etc/s
```
