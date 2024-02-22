# Teku VC

### Prepare the validator keystores

1\) Create 3 new folders to store the validator client data, validator keystore, and the validator keystore password

2\) Copy the validator keystores and it's password file into their respective folders

3\) Change the owner of this folder to the teku user

4\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

```sh
sudo mkdir -p /var/lib/teku_validator/validator_keystores /var/lib/teku_validator/keystore_password
sudo cp ~/validator_keys/<validator_keystore.json> /var/lib/teku_validator/validator_keystores
sudo cp ~/validator_keys/<validator_keystore_password.txt> /var/lib/teku_validator/keystore_password
sudo chown -R teku:teku /var/lib/teku_validator
sudo chmod 700 /var/lib/teku_validator
```

### Configure the validator client service

Create a systemd configuration file for the Teku Validator Client service to run in the background.

```bash
sudo nano /etc/systemd/system/tekuvalidator.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Teku Validator Client (Holesky)
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
  --network=holesky \
  --data-path=/var/lib/teku_validator \
  --validator-keys=/var/lib/teku_validator/validator_keystores:/var/lib/teku_validator/keystore_password \
  --beacon-node-api-endpoint=http://localhost:5051,http://<backup_beacon_node>:<http/rest_port_number>
  --validators-proposer-default-fee-recipient=<your_designated_ETH_wallet address> \
  --validators-proposer-blinded-blocks-enabled=true \
  --validators-graffiti="<your_graffiti_of_choice>" \
  --metrics-enabled=true \
  --metrics-port=8008 \
  --doppelganger-detection-enabled=true 

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Teku Validator Client configuration summary:**

1. `--network`: Run the validator client service on the ETH Holesky testnet
2. `--data-path`: Specify the directory for Teku to store the validator info
3.  `--validator-keys`: File path to the directory where your validator signing keystore and corresponding plain text password file are stored. **Aside from the file extension (e.g. .json vs .txt), the password file will need to be named identically as the validator signing keystore file.** For example:

    <figure><img src="../../.gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>
4. `--beacon-node-api-endpoint`: URLs to connect to the main and backup beacon nodes if any.
5. `--validators-proposer-default-fee-recipient`: ETH wallet address to receive rewards from block proposals and MEV bribes
6. `--validators-proposer-blinded-blocks-enabled`: Required when using external builders to build blocks (e.g. MEV relays)
7. `--validators-graffiti`: Optional text to display on-chain when your validator proposes a block
8. `--metrics-enabled`: Enable metrics for monitoring
9. `--metrics-port`: Port to retrieve metrics for monitoring
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

<figure><img src="../../.gitbook/assets/image (55).png" alt=""><figcaption><p>Example output of the Teku VC running on the Goerli testnet. Look out for Holesky in your output.</p></figcaption></figure>

Press `CTRL-C` to exit.

If the Teku Validator Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable tekuvalidator
```

**Expected output:**

```
Created symlink /etc/systemd/system/multi-user.target.wants/tekuvalidator.service → /etc/s
```

### Remove duplicates of validator keystores

To prevent configuration mistakes leading to double signing in the future, remove duplicate copies of the validator signing keystores once everything is running smoothly.

```sh
sudo rm -r ~/validator_keys
```

## Resources

* Releases: [https://github.com/Consensys/teku/releases](https://github.com/Consensys/teku/releases)
* Documentation: [https://docs.teku.consensys.io/introduction](https://docs.teku.consensys.io/introduction)
* Discord: [https://discord.gg/consensys](https://discord.gg/consensys) (Select the Teku channel)
