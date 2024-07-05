# Besu

### Generate the JWT file

We first need to create a JSON Web Token (JWT) that will allow the execution layer software (Besu) and the consensus layer software to talk to each other.

Run the following commands one line at a time to create a folder on the server to store the JWT file and generate the JWT file:

```bash
sudo mkdir -p /var/lib/jwtsecret
openssl rand -hex 32 | sudo tee /var/lib/jwtsecret/jwt.hex > /dev/null
```

We will be pointing the configuration files of the execution and consensus clients to this JWT file later.

### Install dependencies - JRE & Jemalloc

```bash
sudo apt-get update
sudo apt -y install openjdk-17-jre libjemalloc-dev
```

### Download Besu and configure the service

[Download](https://github.com/hyperledger/besu/releases) the latest version of Besu and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/hyperledger/besu/releases/download/24.5.2/besu-24.5.2.tar.gz
echo "4049bf48022ae073065b46e27088399dfb22035e9134ed4ac2c86dd8c5b5fbe9 besu-24.5.2.tar.gz" | sha256sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum (see below). Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to verify the correct checksum according to the downloaded version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

<figure><img src="../../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

_**Expected output:** Verify output of the checksum verification_

```
besu-24.5.2.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf besu-24.5.2.tar.gz
sudo cp -a besu-24.5.2 /usr/local/bin/besu
sudo rm -r besu-24.5.2.tar.gz besu-24.5.2
```

Create an account (`besu`) without server access for Besu to run as a background service. This type of user account will not have root access so it restricts potential attackers to only the Besu service in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false besu
```

Create a directory for Besu to store the blockchain data of the execution layer. Then set the owner of this directory to `Besu` so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/besu
sudo chown -R besu:besu /var/lib/besu
```

Create a systemd configuration file for the Besu service to run in the background.

```bash
sudo nano /etc/systemd/system/besu.service
```

Paste the configuration parameters below into the file:

```
[Unit]
Description=Besu Execution Client (Holesky)
Wants=network-online.target
After=network-online.target

[Service]
User=besu
Group=besu
Type=simple
Restart=always
RestartSec=5
Environment="JAVA_OPTS=-Xmx6g"
ExecStart=/usr/local/bin/besu/bin/besu \
  --network=holesky \
  --sync-mode=X_SNAP \
  --data-path=/var/lib/besu \
  --data-storage-format=BONSAI \
  --engine-jwt-secret=/var/lib/jwtsecret/jwt.hex \
  --Xplugin-rocksdb-high-spec-enabled \
  --p2p-port=30304 \
  --rpc-ws-enabled \
  --rpc-ws-host=<internal_IP_address> \
  --rpc-ws-port=8547 \
  --rpc-http-enabled=true \
  --metrics-enabled=true
  
[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Besu configuration summary:**

1. `--network`: Run the on the Holesky testnet
2. `--sync-mode`: Uses the recommended Snap Sync mode to speed up syncing time. Checkpoint Sync is still an early access feature.
3. `--data-path`: The directory for Besu to store the blockchain data of the execution layer.
4. `--data-storage-format`: Bonsai Tries is a data storage layout policy designed to reduce storage requirements and increase read performance.
5. `--engine-jwt-secret`: The directory pointing to the JWT secret we generated earlier.
6. `--Xplugin-rocksdb-high-spec-enabled`: Speeds up sync time and performance. Use only if you have 32GB of RAM or more.
7. `--p2p-port:` Sets the port used for peer-to-peer communication. Defaults to 30303.
8. `--rpc-ws-enabled`: Enables the JSON-RPC service on websocket. This is so that DVT clients can connect to your execution client &#x20;
9. `--rpc-ws-host`: Sets the IP address to connect to the JSON-RPC service that will be used by the DVT client. Use the internal IP address of your device here (check by running `ip a`) - e.g. `192.168.x.x`. Defaults to `127.0.0.1` otherwise
10. `--rpc-ws-port`: Sets the port to connect to the JSON-RPC service. You may choose any unused port number but remember to allow incoming connections into your chosen port in your firewall (`ufw`) rules. Defaults to 8546
11. `--rpc-http-enabled`: Enables the JSON-RPC service over http. This allows you to query the execution client endpoint directly to test for sync status and overall health. Default IP address (host) = 127.0.0.1; Default port = 8545
12. `--metrics-enabled`: Enable monitoring metrics on the besu service

### Start Besu

Reload the systemd daemon to register the changes made, start Besu, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start besu.service
sudo systemctl status besu.service
```

**Expected output:** The output should say Besu is **“active (running)”.** Press CTRL-C to exit and Besu will continue to run. It should take just a few minutes for Besu to sync on Holesky.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the following command to check the logs of Besu’s syncing process. Watch out for any warnings or errors.

```bash
sudo apt install ccze -y
sudo journalctl -fu besu -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Press `CTRL-C` to exit.

{% hint style="info" %}
For more details on interpreting the Besu journalctl logs, head [here](https://besu.hyperledger.org/23.4.0/public-networks/concepts/events-and-logs).
{% endhint %}

If the Besu service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable besu.service
```

**Expected output:**

```
Created symlink /etc/systemd/system/default.target.wants/besu.service → /etc/systemd/system/besu.service.
```

## Resources:

1. Releases: [https://github.com/hyperledger/besu/releases](https://github.com/hyperledger/besu/releases)
2. Documentation: [https://besu.hyperledger.org/public-networks](https://besu.hyperledger.org/public-networks)
3. Discord: [https://discord.gg/consensys](https://discord.gg/consensys) (Select the Besu channel)
