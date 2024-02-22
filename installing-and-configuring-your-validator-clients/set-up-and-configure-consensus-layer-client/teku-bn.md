# Teku BN

## Install the dependencies - Java Runtime Environment

```bash
sudo apt install openjdk-17-jdk
```

## Download Teku

[Download](https://github.com/ConsenSys/teku/releases) the latest version of Teku and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://artifacts.consensys.net/public/teku/raw/names/teku.tar.gz/versions/24.2.0/teku-24.2.0.tar.gz
echo "f7da4109b180e1f1118d6fa13e4d48a964d0f58724d1e6d3fd4a92ddccabab58 teku-24.2.0.tar.gz" | sha256sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum. Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

_**Expected output:** Verify output of the checksum verification._

```
teku-24.2.0.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf teku-24.2.0.tar.gz
sudo cp -a teku-24.2.0 /usr/local/bin/teku
rm -r teku-24.2.0.tar.gz teku-24.2.0
```

## Configure the Teku Consensus Client

{% hint style="info" %}
_We will be running the consensus client and consensus client of Teku as separate services so that there is more flexibility to configure a failover node for maximum uptime when you decide it is needed._
{% endhint %}

Create an account (`teku`) without server access for the Teku Consensus Client & Validator Client to run as a background service. This type of user account will not have root access so it restricts potential attackers to only the Teku Consensus Client & Validator Client services in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false teku
```

Create a directory for Teku to store the blockchain and validator data of the Consensus layer. Move the `validator_keys` directory into this folder. Then set the owner of this directory to the `teku` so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/teku_beacon
sudo chown -R teku:teku /var/lib/teku_beacon
sudo chmod 700 /var/lib/teku_beacon
```

If there are no errors, create a systemd configuration file for the Teku Consensus Client service to run in the background.

```bash
sudo nano /etc/systemd/system/tekubeacon.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Teku Beacon Node (Holesky)
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
ExecStart=/usr/local/bin/teku/bin/teku \
  --network=holesky \
  --data-path=/var/lib/teku_beacon \
  --ee-endpoint=http://127.0.0.1:8551 \
  --ee-jwt-secret-file=/var/lib/jwtsecret/jwt.hex \
  --initial-state=https://holesky.beaconstate.ethstaker.cc/ \
  --metrics-enabled=true \
  --rest-api-enabled=true \
  --rest-api-interface=<Internal_IP_address> \
  --rest-api-host-allowlist=<Internal_IP_address> \
  --rest-api-port=5052 \
  --builder-endpoint=http://127.0.0.1:18550 \
  --validators-builder-registration-default-enabled=true \
  --p2p-nat-method=UPNP 

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Teku Consensus Client configuration summary:**

1. `--network`: Run the Consensus Client service on the ETH Holesky testnet
2. `--data-path`: Specify the directory for Teku to store data related to the consensus client
3. `--ee-endpoint`: URL to connect to the execution layer client
4. `--ee-jwt-secret`: File path to locate the JWT secret we generated earlier
5. `--initial-state`: Enables nearly instant syncing of the Consensus Client by pointing to one of the checkpoint sync URLs here - [https://eth-clients.github.io/checkpoint-sync-endpoints/](https://eth-clients.github.io/checkpoint-sync-endpoints/)
6. `--metrics-enabled`: Enable monitoring of Consensus Client metrics
7. `--rest-api-enabled`: Allows the validator client to connect to this consensus client. Also allows monitoring endpoints to pull metrics from this service
8. `--rest-api-interface`: Sets the IP address to connect to the REST API of the consensus client that will be used by the Diva service. Use the internal IP address of your device here (check by running `ip a`) - e.g. `192.168.x.x`. Defaults to `127.0.0.1` otherwise
9. `--rest-api-port`: Sets the port to connect to the consensus client
10. `--builder-endpoint`: URL to connect to external builders (e.g. MEV relays)
11. `--validators-builder-registration-default-enabled`: Required when using external builders to build blocks (e.g. MEV relays)
12. `--p2p-nat-method=UPNP`: Enables your Consensus Client to better discover and connect to other Consensus Clients in the ETH network without needing to use port forwarding

## Start the Teku Consensus Client

Reload systemd to register the changes made, start the Teku Consensus Client service, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start tekubeacon.service
sudo systemctl status tekubeacon.service
```

**Expected output:** The output should say Teku Consensus Client is **“active (running)”.** Press CTRL-C to exit and Teku Consensus Client will continue to run. It should take just a few minutes for Teku to sync on the Mainnet.

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption><p>Example output of the Teku consensus client running on the mainnet </p></figcaption></figure>

Use the following command to check the logs of Teku Consensus Client’s syncing process. Watch out for any warnings or errors.

```bash
sudo journalctl -fu tekubeacon -o cat | ccze -A
```

**Expected output:**&#x20;

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption><p>This example runs on the goerli testnet. You should see a different initial state URL being printed on Holesky.</p></figcaption></figure>

Press `Ctrl+C` to exit monitoring.

If the Teku Consensus Client service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable tekubeacon.service
```

## Verify the Initial State roots (Checkpoint Sync)

1. Go to [https://holesky.beaconcha.in/](https://holesky.beaconcha.in/) on your browser and search for the slot number (`slot`).&#x20;
2.  &#x20;Verify the `Block Root` and `State Roo`t with your `journalctl` output

    <figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption><p>testnet example: prater.beaconcha.in</p></figcaption></figure>
3. `journalctl` output

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

## Resources

* Releases: [https://github.com/Consensys/teku/releases](https://github.com/Consensys/teku/releases)
* Documentation: [https://docs.teku.consensys.io/introduction](https://docs.teku.consensys.io/introduction)
* Discord: [https://discord.gg/consensys](https://discord.gg/consensys) (Select the Teku channel)
