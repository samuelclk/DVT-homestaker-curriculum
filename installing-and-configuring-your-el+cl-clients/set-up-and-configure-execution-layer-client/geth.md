# Geth

### Generate the JWT file

We first need to create a JSON Web Token (JWT) that will allow the execution layer software (Geth) and the consensus layer software to talk to each other.

Run the following commands one line at a time to create a folder on the server to store the JWT file and generate the JWT file:

```bash
sudo mkdir -p /var/lib/jwtsecret
openssl rand -hex 32 | sudo tee /var/lib/jwtsecret/jwt.hex > /dev/null
```

We will be pointing the configuration files of the execution and consensus clients to this JWT file later.

### Download Geth and configure the service

[Download](https://geth.ethereum.org/downloads) the latest version of Geth and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.14.3-ab48ba42.tar.gz
echo "734653ddbc3a6877cba40583dcaf26f5 geth-linux-amd64-1.14.3-ab48ba42.tar.gz" | md5sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum (see below). Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

_**Expected output:** Verify output of the checksum verification_

```
geth-linux-amd64-1.14.3-ab48ba42.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf geth-linux-amd64-1.14.3-ab48ba42.tar.gz
sudo cp geth-linux-amd64-1.14.3-ab48ba42/geth /usr/local/bin
rm -r geth-linux-amd64-1.14.3-ab48ba42 geth-linux-amd64-1.14.3-ab48ba42.tar.gz
```

Create an account (`geth`) without server access for Geth to run as a background service. This type of user account will not have root access so it restricts potential attackers to only the Geth service in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false geth
```

Create a directory for Geth to store the blockchain data of the execution layer. Then set the owner of this directory to `geth` so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/geth
sudo chown -R geth:geth /var/lib/geth
```

Create a systemd configuration file for the Geth service to run in the background.

```bash
sudo nano /etc/systemd/system/geth.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Geth Execution Client (Holesky)
After=network.target
Wants=network.target

[Service]
User=geth
Group=geth
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/geth \
  --holesky \
  --datadir=/var/lib/geth \
  --authrpc.jwtsecret=/var/lib/jwtsecret/jwt.hex \
  --state.scheme=path \
  --port 30304 \
  --http \
  --http.addr <Internal_IP_address> \
  --http.port 8547 \
  --pprof \
  --metrics
  
[Install]
WantedBy=default.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Geth configuration summary:**

1. `--holesky`: Run the on the Holesky testnet
2. `--datadir`: The directory for Geth to store the blockchain data of the execution layer
3. `--authrpc.jwtsecret`: The directory pointing to the JWT secret we generated earlier
4. `--state.scheme=path`: Use Geth's path-based state storage feature that is faster and prunes continuously. More information [here](https://blog.ethereum.org/2023/09/12/geth-v1-13-0)&#x20;
5. `--port`: Sets the port used for peer-to-peer communication. Defaults to 30303.
6. `--http`: Enables the HTTP-RPC service on http and websocket. This is so that DVT clients such as the Diva service can connect to your execution client &#x20;
7. `--http.addr`: Sets the IP address to connect to the JSON RPC service. Use the internal IP address of your device here (check by running `ip a`) - e.g. `192.168.x.x`. Defaults to `127.0.0.1` otherwise
8. `--http.port`: Sets the port to connect to the HTTP-RPC service that will be used by the DVT services. You may choose any unused port number but remember to allow incoming connections into your chosen port in your firewall (`ufw`) rules. Defaults to 8545.
9. `--pprof:` Enables the pprof HTTP server, providing profiling data about the Geth process. Includes CPU usage, memory allocation, blocking profiles, etc. Useful for debugging and optimizing performance.
10. `--metrics`: Enable monitoring metrics on the Geth service.

### Start Geth

Reload the systemd daemon to register the changes made, start Geth, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start geth.service
sudo systemctl status geth.service
```

**Expected output:** The output should say Geth is **“active (running)”.** Press CTRL-C to exit and Geth will continue to run. It should take around 6 hours for Geth to sync on the Holesky testnet.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the following command to check the logs of Geth’s syncing process. Watch out for any warnings or errors.

```bash
sudo apt install ccze -y
sudo journalctl -fu geth -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Press `CTRL-C` to exit.

{% hint style="info" %}
For more details on interpreting the Geth journalctl logs, head [here](https://geth.ethereum.org/docs/fundamentals/logs).
{% endhint %}

If the Geth service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable geth.service
```

**Expected output:**

```
Created symlink /etc/systemd/system/default.target.wants/geth.service → /etc/systemd/system/geth.service.
```

## Resources:

1. Releases: [https://geth.ethereum.org/downloads](https://geth.ethereum.org/downloads)
2. Documentation: [https://geth.ethereum.org/docs](https://geth.ethereum.org/docs)
3. Discord: [https://discord.com/invite/nthXNEv](https://discord.com/invite/nthXNEv)
