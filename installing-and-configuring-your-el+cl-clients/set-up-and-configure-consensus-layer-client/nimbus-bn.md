# Nimbus BN

## Download Nimbus

[Download](https://github.com/status-im/nimbus-eth2/releases) the latest version of Nimbus, extract the zipped file, and then run the checksum verification process to ensure that the "nimbus\_beacon\_node" and "nimbus\_validator\_client" files have not been tampered with during download.

<pre class="language-bash"><code class="lang-bash">cd
curl -LO https://github.com/status-im/nimbus-eth2/releases/download/v24.9.0/nimbus-eth2_Linux_amd64_24.9.0_f54a0366.tar.gz
<strong>tar xvf nimbus-eth2_Linux_amd64_24.9.0_f54a0366.tar.gz
</strong><strong>cd nimbus-eth2_Linux_amd64_24.9.0_f54a0366/build
</strong><strong>echo "e40ec8fb6ded9a085bf52f7fa4b2e3cb8f63670f4d4c0f8e190f5a3d7e0038230985083b22ed859a1ddbdce230df05ebb182a73cfa8a614b470aa7516111b7d0  nimbus_beacon_node" | sha512sum --check
</strong>echo "35737f59e52fdc47823532b15d372a225779f38887c39664239a2171bc6f24a79633dfbb9257c7715ae640ea9037a3c6352e2e58c53af74a352d745e23d8c74b  nimbus_validator_client" | sha512sum --check
</code></pre>

{% hint style="info" %}
Each downloadable file comes with it's own checksum. Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

_**Expected output:** Verify output of the checksum verification._

```
nimbus_beacon_node: OK
nimbus_validator_client: OK
```

If checksum is verified, extract the consensus client and validator client binaries into the `(/usr/local/bin)` directory (as a best practice). Then, clean up the duplicated copies.

```bash
cd ~/nimbus-eth2_Linux_amd64_24.9.0_f54a0366/build
sudo cp nimbus_beacon_node nimbus_validator_client /usr/local/bin
cd
rm -r nimbus*
```

## Configure the Nimbus Consensus Client

{% hint style="info" %}
_We will be running the Consensus client and Validator client of Nimbus as separate services so that there is more flexibility to configure a failover node for maximum uptime when you decide it is needed._
{% endhint %}

Create an account (`nimbus`) without server access for the Nimbus Beacon Node & Validator Client to run as a background service. This type of user account will not have root access so it restricts potential attackers to only the Nimbus Beacon Node & Validator Client services in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false nimbus
```

Create a directory for Nimbus to store the blockchain and validator data of the Consensus layer. Move the `validator_keys` directory into this folder. Then set the owner of this directory to the Nimbus so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/nimbus_beacon
sudo chmod 700 /var/lib/nimbus_beacon
```

If there are no errors, create a systemd configuration file for the Nimbus Beacon Node service to run in the background.

```bash
sudo nano /etc/systemd/system/nimbusbeacon.service
```

Paste the configuration parameters below into the file:

```
[Unit]
Description=Nimbus Beacon Node (Holesky)
Wants=network-online.target
After=network-online.target

[Service]
User=nimbus
Group=nimbus
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/nimbus_beacon_node \
  --network=holesky \
  --data-dir=/var/lib/nimbus_beacon \
  --web3-url=http://127.0.0.1:8551 \
  --jwt-secret=/var/lib/jwtsecret/jwt.hex \
  --payload-builder=true \
  --payload-builder-url=http://127.0.0.1:18550 \
  --tcp-port=9000 \
  --udp-port=9000 \
  --rest \
  --rest-address=<Internal_IP_address> \
  --rest-port=5052 \
  --metrics \
  --metrics-port=8009 \
  --local-block-value-boost=0
  
[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Nimbus Beacon Node configuration summary:**

1. `--network`: Run the beacon node service on the ETH Holesky testnet
2. `--data-path`: Specify the directory for Nimbus to store data related to the consensus client
3. `--web3-url`: URL to connect to the execution layer client
4. `--jwt-secret`: File path to locate the JWT secret we generated earlier
5. `--payload-builder`: Enable external payload builder
6. `--payload-builder-url`: URL to connect to external payload builder (MEV-boost)
7. `--tcp-port / --udp-port:` Sets the port for peer-to-peer communication. Defaults to 9000.
8. `--rest`: Enable the REST API endpoint for validator and DVT clients to connect to this consensus client&#x20;
9. `--rest-address`: Sets the IP address to connect to the REST API of the consensus client that will be used by the DVT clients. Use the internal IP address of your device here (check by running `ip a`) - e.g. `192.168.x.x`. Defaults to `127.0.0.1` otherwise
10. `--rest-port`: Port to connect to the REST API endpoint
11. `--metrics`: Enable monitoring of consensus client metrics
12. `--metrics-port`: Port to connect to the metrics endpoint
13. `--local-block-value-boost`: How much more (in %) must the value of externally-built blocks be in order to outsource block building vs building blocks locally. Set to `0` to be indifferent. Default `=10`

## Run the checkpoint sync process

For Nimbus, you have to first run the checkpoint sync process separately before firing up the consensus client with the above configurations.

```sh
sudo /usr/local/bin/nimbus_beacon_node trustedNodeSync --network=holesky --data-dir=/var/lib/nimbus_beacon --trusted-node-url=https://holesky.beaconstate.ethstaker.cc/ --backfill=false
```

* Checkpoint sync URL list: [https://eth-clients.github.io/checkpoint-sync-endpoints/](https://eth-clients.github.io/checkpoint-sync-endpoints/)
* Go to [holesky.beaconcha.in ](https://holesky.beaconcha.in/)on your browser and search for the slot number (`slot`)
* Verify the `Block Root` and `State Root` with your output

**Expected output:**

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

## Start the Nimbus Consensus Client

First we have to change the owner of the nimbus beacon data directory to the `nimbus` user.

```sh
sudo chown -R nimbus:nimbus /var/lib/nimbus_beacon
```

Reload systemd to register the changes made, start the Nimbus Consensus Client service, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start nimbusbeacon.service
sudo systemctl status nimbusbeacon.service
```

**Expected output:** The output should say Nimbus Beacon Node is **“active (running)”.** Press CTRL-C to exit and Nimbus Beacon Node will continue to run. It should take just a few minutes for Nimbus to sync on the Holesky.

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Use the following command to check the logs of Nimbus Consensus Client’s syncing process. Watch out for any warnings or errors.

```bash
sudo journalctl -fu nimbusbeacon -o cat | ccze -A
```

**Expected output:**&#x20;

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

Press `Ctrl+C` to exit monitoring.

**Note:** You will see some warnings around `"Block failed to load unexpectedly"` if your execution client has yet to fully sync. The following output is expected initially and should go away after a few hours.

<figure><img src="../../.gitbook/assets/image (155).png" alt=""><figcaption></figcaption></figure>

If the Nimbus Consensus Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable nimbusbeacon.service
```

## Resources

* Releases: [https://github.com/status-im/nimbus-eth2/releases](https://github.com/status-im/nimbus-eth2/releases)
* Documentation: [https://nimbus.guide/install.html](https://nimbus.guide/install.html)
* Discord: [https://discord.gg/qnjVyhatUa](https://discord.gg/qnjVyhatUa)
