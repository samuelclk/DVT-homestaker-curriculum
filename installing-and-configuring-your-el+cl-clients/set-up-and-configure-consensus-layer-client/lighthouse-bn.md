# Lighthouse BN

## Download Lighthouse

[Download](https://github.com/sigp/lighthouse/releases) the latest version of Lighthouse and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/sigp/lighthouse/releases/download/v5.3.0/lighthouse-v5.3.0-x86_64-unknown-linux-gnu.tar.gz
curl -LO https://github.com/sigp/lighthouse/releases/download/v5.3.0/lighthouse-v5.3.0-x86_64-unknown-linux-gnu.tar.gz.asc
```

Run the checksum verification process.

```sh
gpg --keyserver keyserver.ubuntu.com --recv-keys 15E66D941F697E28F49381F426416DC3F30674B0
gpg --verify lighthouse-v5.3.0-x86_64-unknown-linux-gnu.tar.gz.asc lighthouse-v5.3.0-x86_64-unknown-linux-gnu.tar.gz
```

Verify the release signing key (`--recv-keys`) in the first command above in the releases page [here](https://github.com/sigp/lighthouse/releases).

{% hint style="info" %}
Each downloadable file comes with it's own checksum. Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

_**Expected output:** Verify output of the checksum verification._

```
gpg: Signature made Thu Mar 28 07:30:45 2024 UTC
gpg:                using RSA key 15E66D941F697E28F49381F426416DC3F30674B0
gpg: Good signature from "Sigma Prime <security@sigmaprime.io>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 15E6 6D94 1F69 7E28 F493  81F4 2641 6DC3 F306 74B0
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

<pre class="language-bash"><code class="lang-bash">tar xvf lighthouse-v5.3.0-x86_64-unknown-linux-gnu.tar.gz
sudo cp lighthouse /usr/local/bin
<strong>rm -r lighthouse*
</strong></code></pre>

## Configure the Lighthouse Consensus Client

{% hint style="info" %}
_We will be running the consensus client and validator client of Lighthouse as separate services so that there is more flexibility to configure a failover node for maximum uptime when you decide it is needed._
{% endhint %}

Create an account (`lighthouse`) without server access for the Lighthouse Consensus Client & Validator Client to run as a background service. This type of user account will not have root access so it restricts potential attackers to only the Lighthouse Consensus Client & Validator Client services in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false lighthousebeacon
```

Create a directory for Lighthouse to store the blockchain and validator data of the Consensus layer. Move the `validator_keys` directory into this folder. Then set the owner of this directory to the `lighthouse` so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/lighthouse_beacon
sudo chown -R lighthousebeacon:lighthousebeacon /var/lib/lighthouse_beacon
sudo chmod 700 /var/lib/lighthouse_beacon
```

If there are no errors, create a systemd configuration file for the Lighthouse Consensus Client service to run in the background.

```bash
sudo nano /etc/systemd/system/lighthousebeacon.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Lighthouse Beacon Node (Holesky)
Wants=network-online.target
After=network-online.target

[Service]
User=lighthousebeacon
Group=lighthousebeacon
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/lighthouse bn \
  --network holesky \
  --datadir /var/lib/lighthouse_beacon \
  --execution-endpoint http://127.0.0.1:8551 \
  --execution-jwt /var/lib/jwtsecret/jwt.hex \
  --checkpoint-sync-url=https://holesky.beaconstate.ethstaker.cc/ \
  --metrics \
  --metrics-port 8009 \
  --validator-monitor-auto \
  --port 9000 \
  --http \
  --http-port 5052 \
  --http-address <Internal_IP_address> \
  --builder http://127.0.0.1:18550 

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Lighthouse Consensus Client configuration summary:**

1. `--network`: Run the Consensus Client service on the ETH Holesky testnet
2. `--datadir`: Specify the directory for Lighthouse to store data related to the consensus client
3. `--execution-endpoint`: URL to connect to the execution layer client
4. `--execution-jwt`: File path to locate the JWT secret we generated earlier
5. `--checkpoint-sync-url`: Enables nearly instant syncing of the Consensus Client by pointing to one of the checkpoint sync URLs here - [https://eth-clients.github.io/checkpoint-sync-endpoints/](https://eth-clients.github.io/checkpoint-sync-endpoints/)
6. `--metrics`: Enable monitoring of Consensus Client metrics
7. `--metrics-port`: Port to connect to the metrics server. Used by Prometheus & Grafana for monitoring.
8. `--validator-monitor-auto`: Provides additional logging and metrics for locally controlled validators
9. `--port:` Sets the port for peer-to-peer communication. Defaults to 9000.
10. `--http`: Allows the validator client to connect to this consensus client. Also allows monitoring endpoints to pull metrics from this service
11. `--http-port`: Sets the port to connect to the consensus client
12. `--http-address`: Sets the IP address to connect to the REST API of the consensus client that will be used by the DVT clients. Use the internal IP address of your device here (check by running `ip a`) - e.g. `192.168.x.x`. Defaults to `127.0.0.1` otherwise
13. `--builder`: URL to connect to external builders (e.g. MEV relays)

## Start the Lighthouse Consensus Client

Reload systemd to register the changes made, start the Lighthouse Consensus Client service, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start lighthousebeacon.service
sudo systemctl status lighthousebeacon.service
```

**Expected output:** The output should say Lighthouse Consensus Client is **“active (running)”.** Press `CTRL-C` to exit and Lighthouse Consensus Client will continue to run. It should take just a few minutes for Lighthouse to sync on Holesky.

<figure><img src="../../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

Use the following command to check the logs of Lighthouse Consensus Client’s syncing process. Watch out for any warnings or errors.

```bash
sudo journalctl -fu lighthousebeacon -o cat | ccze -A
```

**Expected output:**&#x20;

<figure><img src="../../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>

Press `Ctrl+C` to exit monitoring.

If the Lighthouse Consensus Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable lighthousebeacon.service
```

## Verify the Initial State roots (Checkpoint Sync)

1. Go to [https://holesky.beaconcha.in/](https://holesky.beaconcha.in/) on your browser and search for the slot number (`slot`).&#x20;
2.  &#x20;Verify the `Block Root` and `State Roo`t with your `journalctl` output

    <figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption><p>testnet example: prater.beaconcha.in</p></figcaption></figure>
3. `journalctl` output

<figure><img src="../../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

## Resources

* Releases: [https://github.com/sigp/lighthouse/releases](https://github.com/sigp/lighthouse/releases)
* Documentation: [https://lighthouse-book.sigmaprime.io/intro.html](https://lighthouse-book.sigmaprime.io/intro.html)
* Discord: [https://discord.com/invite/TX7HKfgJN3](https://discord.com/invite/TX7HKfgJN3)
