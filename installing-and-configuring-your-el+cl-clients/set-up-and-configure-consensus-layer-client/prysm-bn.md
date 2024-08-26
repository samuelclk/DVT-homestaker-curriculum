# Prysm BN

## Download Prysm

[Download](https://github.com/sigp/lighthouse/releases) the latest version of Prysm beacon node (`beacon-chain`) and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/prysmaticlabs/prysm/releases/download/v5.0.3/beacon-chain-v5.0.3-linux-amd64
curl -LO https://github.com/prysmaticlabs/prysm/releases/download/v5.0.3/beacon-chain-v5.0.3-linux-amd64.sha256
```

Run the checksum verification process.

```sh
sha256sum --check beacon-chain-v5.0.3-linux-amd64.sha256
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum. Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

_**Expected output:** Verify output of the checksum verification._

```
beacon-chain-v5.0.3-linux-amd64: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

<pre class="language-bash"><code class="lang-bash">mv beacon-chain-v5.0.3-linux-amd64 prysmbeacon #rename the binary file for easy reference
chmod +x prysmbeacon
<strong>sudo cp prysmbeacon /usr/local/bin
</strong><strong>rm -r prysmbeacon beacon-chain-v5.0.3-linux-amd64.sha256
</strong></code></pre>

## Configure the Prysm Consensus Client

{% hint style="info" %}
_We will be running the consensus client and validator client of Prysm as separate services so that there is more flexibility to configure a failover node for maximum uptime when you decide it is needed._
{% endhint %}

Create an account (`prysmbeacon`) without server access for the Prysm Consensus Client & Validator Client to run as a background service. This type of user account will not have root access so it restricts potential attackers to only the Prysm Consensus Client & Validator Client services in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false prysmbeacon
```

Create a directory for Prysm Beacon to store the blockchain and validator data of the Consensus layer. Move the `validator_keys` directory into this folder. Then set the owner of this directory to the `prysmbeacon` so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/prysm_beacon
sudo chown -R prysmbeacon:prysmbeacon /var/lib/prysm_beacon
sudo chmod 700 /var/lib/prysm_beacon
```

If there are no errors, create a systemd configuration file for the Prysm Consensus Client to run in the background.

```bash
sudo nano /etc/systemd/system/prysmbeacon.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Prysm Beacon Node (Holesky)
Wants=network-online.target
After=network-online.target

[Service]
User=prysmbeacon
Group=prysmbeacon
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/prysmbeacon \
  --accept-terms-of-use \
  --holesky \
  --datadir=/var/lib/prysm_beacon \
  --execution-endpoint=http://127.0.0.1:8551 \
  --jwt-secret=/var/lib/jwtsecret/jwt.hex \
  --checkpoint-sync-url=https://holesky.beaconstate.ethstaker.cc/ \
  --monitoring-port 8009 \
  --p2p-tcp-port 13000 \
  --p2p-udp-port 12000 \
  --rpc-port 4000 \
  --rpc-host <Internal_IP_address> \
  --grpc-gateway-port 5051 \
  --grpc-gateway-host <Internal_IP_address> \
  --http-mev-relay=http://127.0.0.1:18550 

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Prysm Consensus Client configuration summary:**

1. `--accept-terms-of-use`: Accept the terms and conditions.
2. `--holesky`: Run the Consensus Client service on the ETH Holesky testnet
3. `--datadir`: Specify the directory for Prysm to store data related to the consensus client
4. `--execution-endpoint`: URL to connect to the execution layer client
5. `--jwt-secret`: File path to locate the JWT secret we generated earlier
6. `--checkpoint-sync-url`: Enables nearly instant syncing of the Consensus Client by pointing to one of the checkpoint sync URLs here - [https://eth-clients.github.io/checkpoint-sync-endpoints/](https://eth-clients.github.io/checkpoint-sync-endpoints/)
7. `--monitoring-port`: Port to connect to the metrics server. Used by Prometheus & Grafana for monitoring.
8. `--p2p-tcp-port/--p2p-udp-port`: Sets the port for peer-to-peer communication. Defaults to 13000 & 12000 respectively.
9. `--rpc-port`: RPC port exposed by the Prysm beacon node (default: 4000) that will be used by the validator and DVT clients.
10. `--rpc-host`: Host on which the RPC server should listen (default: "127.0.0.1") that will be used by the validator and DVT clients.
11. `--grpc-gateway-port`: Sets the port to connect to the consensus client
12. `--grpc-gateway-host`: Sets the IP address to connect to the REST API of the consensus client that will be used by the validator and DVT clients. Use the internal IP address of your device here (check by running `ip a`) - e.g. `192.168.x.x`. Defaults to `127.0.0.1` otherwise
13. `--http-mev-relay`: URL to connect to external builders (e.g. MEV relays)

## Start the Prysm Consensus Client

Reload systemd to register the changes made, start the Prysm Consensus Client service, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start prysmbeacon.service
sudo systemctl status prysmbeacon.service
```

**Expected output:** The output should say Prysm Consensus Client is **“active (running)”.** Press `CTRL-C` to exit and Prysm Consensus Client will continue to run. It should take just a few minutes for Prysm to sync on Holesky.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the following command to check the logs of Prysm Consensus Client’s syncing process. Watch out for any warnings or errors.

```bash
sudo journalctl -fu prysmbeacon -o cat | ccze -A
```

**Expected output:**&#x20;

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Press `Ctrl+C` to exit monitoring.

If the Prysm Consensus Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable prysmbeacon.service
```

## Verify the Initial State roots (Checkpoint Sync)

1. Go to [https://holesky.beaconcha.in/](https://holesky.beaconcha.in/) on your browser and search for the slot number (`slot`).&#x20;
2.  &#x20;Verify the `Block Root` and `State Roo`t with your `journalctl` output

    <figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption><p>testnet example: prater.beaconcha.in</p></figcaption></figure>
3. `journalctl` output

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Resources

* Releases: [https://github.com/prysmaticlabs/prysm/releases](https://github.com/prysmaticlabs/prysm/releases)
* Documentation: [https://docs.prylabs.network/docs/getting-started](https://docs.prylabs.network/docs/getting-started)
* Discord: [https://discord.gg/prysmaticlabs](https://discord.gg/prysmaticlabs)
