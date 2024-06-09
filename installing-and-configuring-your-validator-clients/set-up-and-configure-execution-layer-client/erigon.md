# Erigon

{% hint style="info" %}
You will need a 4TB NVME SSD to run the Erigon execution client.&#x20;
{% endhint %}

{% hint style="info" %}
The Erigon execution client is optimised to run as an Archival Node, which stores the full state of the Ethereum blockchain instead of just the most recent 128 blocks in Full Nodes. Running a Ethereum validator only requires a full node.&#x20;
{% endhint %}

### Generate the JWT file

We first need to create a JSON Web Token (JWT) that will allow the execution layer software (Erigon) and the consensus layer software to talk to each other.

Run the following commands one line at a time to create a folder on the server to store the JWT file and generate the JWT file:

```bash
sudo mkdir -p /var/lib/jwtsecret
openssl rand -hex 32 | sudo tee /var/lib/jwtsecret/jwt.hex > /dev/null
```

We will be pointing the configuration files of the execution and consensus clients to this JWT file later.

### Download Erigon and configure the service

[Download](https://geth.ethereum.org/downloads) the latest version of Erigon and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/ledgerwatch/erigon/releases/download/v2.60.0/erigon_2.60.0_linux_amd64.tar.gz
echo "27acb08c65c18128e78faa1c7edfae169e53f58db7bb347bc7145ce8ea1013a0 erigon_2.60.0_linux_amd64.tar.gz" | sha256sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum (see below). Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

<figure><img src="../../.gitbook/assets/image (161).png" alt=""><figcaption></figcaption></figure>

_**Expected output:** Verify output of the checksum verification_

```
erigon_2.60.0_linux_amd64.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf erigon_2.60.0_linux_amd64.tar.gz
sudo cp erigon /usr/local/bin
rm -r erigon README.md erigon_2.60.0_linux_amd64.tar.gz
```

Create an account (`erigon`) without server access for Erigon to run as a background service. This type of user account will not have root access so it restricts potential attackers to only the Geth service in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false erigon
```

Create a directory for Erigon to store the blockchain data of the execution layer. Then set the owner of this directory to `erigon` so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/erigon
sudo chown -R erigon:erigon /var/lib/erigon
```

Create a systemd configuration file for the Erigon service to run in the background.

```bash
sudo nano /etc/systemd/system/erigon.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Erigon Execution Client (Holesky)
After=network.target
Wants=network.target

[Service]
User=erigon
Group=erigon
Type=simple
Restart=on-failure
RestartSec=5
ExecStart=/usr/local/bin/erigon \
  --chain holesky \
  --datadir /var/lib/erigon \
  --authrpc.jwtsecret=/var/lib/jwtsecret/jwt.hex \
  --port 30304 \
  --http \
  --http.addr value <Internal_IP_address> \
  --http.port 8547 \
  --pprof \
  --prune htc \
  --private.api.addr "" \
  --metrics
  
[Install]
WantedBy=default.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Erigon configuration summary:**

1. `--chain holesky`: Run the on the Holesky testnet
2. `--datadir`: The directory for Erigon to store the blockchain data of the execution layer
3. `--authrpc.jwtsecret`: The directory pointing to the JWT secret we generated earlier
4. `--port`: Sets the port used for peer-to-peer communication. Defaults to 30303.
5. `--http`: Enables the HTTP-RPC service on http and websocket. This is so that DVT clients such as the Diva service can connect to your execution client &#x20;
6. `--http.addr`: Sets the IP address to connect to the JSON RPC service. Use the internal IP address of your device here (check by running `ip a`) - e.g. `192.168.x.x`. Defaults to `127.0.0.1` otherwise
7. `--http.port`: Sets the port to connect to the HTTP-RPC service that will be used by the DVT services. You may choose any unused port number but remember to allow incoming connections into your chosen port in your firewall (`ufw`) rules. Defaults to 8545.
8. `--pprof:` Enables the pprof HTTP server, providing profiling data about the Erigon process. Includes CPU usage, memory allocation, blocking profiles, etc. Useful for debugging and optimizing performance.
9. `--prune htc`: Chooses which ancient data delete from DB. h=history, t=transaction, c=call traces
10. `--private.api.addr`: Disables the private API typically used by developers (txpool, rpcdaemon, sentry, downloader data). Defaults to 127.0.0.1:9090, which conflicts with Prometheus Node Exporter is not disabled&#x20;
11. `--metrics`: Enable monitoring metrics on the Erigon service.

### Start Erigon

Reload the systemd daemon to register the changes made, start Erigon, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start erigon.service
sudo systemctl status erigon.service
```

**Expected output:** The output should say Erigon is **“active (running)”.** Press CTRL-C to exit and Erigon will continue to run. It should take around 6 hours for Erigon to sync on the Holesky testnet.

<figure><img src="../../.gitbook/assets/image (162).png" alt=""><figcaption></figcaption></figure>

Use the following command to check the logs of Erigon’s syncing process. Watch out for any warnings or errors.

```bash
sudo apt install ccze -y
sudo journalctl -fu erigon -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

Press `CTRL-C` to exit.

If the Erigon service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable erigon.service
```

**Expected output:**

```
Created symlink /etc/systemd/system/default.target.wants/erigon.service → /etc/systemd/system/erigon.service.
```

## Resources:

1. Releases:[ ](https://github.com/ledgerwatch/erigon/releases)[https://github.com/ledgerwatch/erigon/releases](https://github.com/ledgerwatch/erigon/releases)
2. Documentation: [https://github.com/ledgerwatch/erigon#documentation](https://github.com/ledgerwatch/erigon#documentation)
3. Discord: Request access via their website [here](https://erigon.tech/)
