# Nethermind

### Generate the JWT file

We first need to create a JSON Web Token (JWT) that will allow the execution layer software (Nethermind) and the consensus layer software to talk to each other.

Run the following commands one line at a time to create a folder on the server to store the JWT file and generate the JWT file:

```bash
sudo mkdir -p /var/lib/jwtsecret
openssl rand -hex 32 | sudo tee /var/lib/jwtsecret/jwt.hex > /dev/null
```

We will be pointing the configuration files of the execution and consensus clients to this JWT file later.

### Install dependencies - Unzip, Snappy & the GNU C Library

```bash
sudo apt-get update
sudo apt-get install unzip libsnappy-dev libc6-dev libc6 -y
```

### Download Nethermind and configure the service

[Download](https://downloads.nethermind.io/) the latest version of Nethermind and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/NethermindEth/nethermind/releases/download/1.25.4/nethermind-1.25.4-20b10b35-linux-x64.zip
echo "05848eaab4b1b621054ff507e8592d17 nethermind-1.25.4-20b10b35-linux-x64.zip" | md5sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum (see below). Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

<figure><img src="../../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

_**Expected output:** Verify output of the checksum verification_

```
nethermind-1.25.4-20b10b35-linux-x64.zip: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
unzip nethermind-1.25.4-20b10b35-linux-x64.zip -d nethermind
sudo cp -a nethermind /usr/local/bin/nethermind
rm -r nethermind-1.25.4-20b10b35-linux-x64.zip nethermind
```

Create an account (`nethermind`) without server access for Nethermind to run as a background service. This type of user account will not have root access so it restricts potential attackers to only the Nethermind service in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false nethermind
```

Create a directory for Nethermind to store the blockchain data of the execution layer. Then set the owner of this directory to `nethermind` so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/nethermind
sudo chown -R nethermind:nethermind /var/lib/nethermind
```

Create a systemd configuration file for the Nethermind service to run in the background.

```bash
sudo nano /etc/systemd/system/nethermind.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Nethermind Execution Client (Holesky)
After=network.target
Wants=network.target

[Service]
User=nethermind
Group=nethermind
Type=simple
Restart=always
RestartSec=5
WorkingDirectory=/var/lib/nethermind
Environment="DOTNET_BUNDLE_EXTRACT_BASE_DIR=/var/lib/nethermind"
ExecStart=/usr/local/bin/nethermind/nethermind \
  --config holesky \
  --datadir /var/lib/nethermind \
  --JsonRpc.JwtSecretFile /var/lib/jwtsecret/jwt.hex \
  --Sync.SnapSync true \
  --Network.P2PPort 30304 \
  --Network.DiscoveryPort 30304 \
  --JsonRpc.Enabled true \
  --JsonRpc.Host <Internal_IP_address> \
  --JsonRpc.Port 8547 \
  --HealthChecks.Enabled true \
  --Metrics.Enabled true \
  --Metrics.PushGatewayUrl http://localhost:9091/metrics 
  
[Install]
WantedBy=default.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Nethermind configuration summary:**

1. `--config`: Run the on the Holesky testnet
2. `--datadir`: The directory for Nethermind to store the blockchain data of the execution layer
3. `--JsonRpc.JwtSecretFile`: The directory pointing to the JWT secret we generated earlier
4. `--Sync.SnapSync`: Use Nethermind's snap sync feature. More information [here](https://docs.nethermind.io/nethermind/ethereum-client/sync-modes)&#x20;
5. `--Network.P2PPort/--Network.DiscoveryPort:` Sets the port used for peer-to-peer communication. Defaults to 30303.
6. `--JsonRpc.Enabled:` Enables the JSON-RPC service on http and websocket. This is so that DVT clients such as the Diva service can connect to your execution client &#x20;
7. `--JsonRpc.Host:` Sets the IP address to connect to the JSON RPC service. Use the internal IP address of your device here (check by running `ip a`) - e.g. `192.168.x.x`. Defaults to `127.0.0.1` otherwise
8. `--JsonRpc.Port`: Sets the port to connect to the JSON RPC service that will be used by the DVT clients. You may choose any unused port number but remember to allow incoming connections into your chosen port in your firewall (`ufw`) rules. Defaults to 8545
9. `--HealthChecks.Enabled:` Enables you to test the connection to and health of your Nethermind service using the `curl` command - e.g. `curl http://<Internal_IP_address>:8545/health`
10. `--Metrics.Enabled`: Enable monitoring metrics on the Nethermind service
11. `--Metrics.PushGatewayUrl:` Pushes metrics to your monitoring suite&#x20;

### Start Nethermind

Reload the systemd daemon to register the changes made, start Nethermind, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start nethermind.service
sudo systemctl status nethermind.service
```

**Expected output:** The output should say Nethermind is **“active (running)”.** Press CTRL-C to exit and Nethermind will continue to run. It should take around 12 hours for Nethermind to sync on the Holesky testnet.

<figure><img src="../../.gitbook/assets/image (56).png" alt=""><figcaption><p>Example output on the mainnet</p></figcaption></figure>

Use the following command to check the logs of Nethermind’s syncing process. Watch out for any warnings or errors.

```bash
sudo apt install ccze -y
sudo journalctl -fu nethermind -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

Press `CTRL-C` to exit.

**Note:** You will also see the following error related to Pushgateway. This is expected because we have not installed and configured the Pushgateway service used for monitoring at this point.

<figure><img src="../../.gitbook/assets/image (154).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
For more details on interpreting the Nethermind journalctl logs, head [here](https://docs.nethermind.io/nethermind/first-steps-with-nethermind/getting-started).
{% endhint %}

If the Nethermind service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable nethermind.service
```

**Expected output:**

```
Created symlink /etc/systemd/system/default.target.wants/nethermind.service → /etc/systemd/system/nethermind.service.
```

## Resources:

1. Releases: [https://github.com/NethermindEth/nethermind/releases](https://github.com/NethermindEth/nethermind/releases)
2. Documentation: [https://docs.nethermind.io/](https://docs.nethermind.io/)
3. Discord: [https://discord.gg/MFBrZrGM32](https://discord.gg/MFBrZrGM32)
