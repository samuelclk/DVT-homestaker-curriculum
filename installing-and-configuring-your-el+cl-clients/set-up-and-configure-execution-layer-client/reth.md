# Reth

### Generate the JWT file

We first need to create a JSON Web Token (JWT) that will allow the execution layer software (Reth) and the consensus layer software to talk to each other.

Run the following commands one line at a time to create a folder on the server to store the JWT file and generate the JWT file:

```bash
sudo mkdir -p /var/lib/jwtsecret
openssl rand -hex 32 | sudo tee /var/lib/jwtsecret/jwt.hex > /dev/null
```

We will be pointing the configuration files of the execution and consensus clients to this JWT file later.

### Download Reth and configure the service

[Download](https://github.com/paradigmxyz/reth/releases) the latest version of Reth and it's digital signature (.asc) file. for verifying the checksum to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/paradigmxyz/reth/releases/download/v0.1.0-alpha.23/reth-v0.1.0-alpha.23-x86_64-unknown-linux-gnu.tar.gz
curl -LO https://github.com/paradigmxyz/reth/releases/download/v0.1.0-alpha.23/reth-v0.1.0-alpha.23-x86_64-unknown-linux-gnu.tar.gz.asc
```

Run the checksum verification process.

```
gpg --keyserver keyserver.ubuntu.com --recv-keys A3AE097C89093A124049DF1F5391A3C4100530B4
gpg --verify reth-v0.1.0-alpha.23-x86_64-unknown-linux-gnu.tar.gz.asc reth-v0.1.0-alpha.23-x86_64-unknown-linux-gnu.tar.gz
```

Verify the release signing key (`--recv-keys`) in the first command above [here](https://reth.rs/installation/binaries.html).

{% hint style="info" %}
Each downloadable file comes with it's own checksum (see below). Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

_**Expected output:** Verify output of the checksum verification_

```
-linux-gnu.tar.gz.asc reth-v0.1.0-alpha.23-x86_64-unknown-linux-gnu.tar.gz
gpg: Signature made Mon Apr 22 16:57:36 2024 UTC
gpg:                using EDDSA key A3AE097C89093A124049DF1F5391A3C4100530B4
gpg: Good signature from "Georgios Konstantopoulos (This is the key used by gakonst to sign Reth releases) <georgios@paradigm.xyz>" [expired]
gpg: Note: This key has expired!
Primary key fingerprint: A3AE 097C 8909 3A12 4049  DF1F 5391 A3C4 1005 30B4
```

{% hint style="info" %}
It seems the release key `A3AE097C89093A124049DF1F5391A3C4100530B4` used by the developers has expired so there should be a new key used for future releases.
{% endhint %}

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf reth-v0.1.0-alpha.23-x86_64-unknown-linux-gnu.tar.gz
sudo cp reth /usr/local/bin
rm -r reth reth-v0.1.0-alpha.23-x86_64-unknown-linux-gnu.tar.gz.asc reth-v0.1.0-alpha.23-x86_64-unknown-linux-gnu.tar.gz
```

Create an account (`reth`) without server access for Reth to run as a background service. This type of user account will not have root access so it restricts potential attackers to only the Reth service in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false reth
```

Create a directory for Reth to store the blockchain data of the execution layer. Then set the owner of this directory to `reth` so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/reth
sudo chown -R reth:reth /var/lib/reth
```

Create a systemd configuration file for the Reth service to run in the background.

```bash
sudo nano /etc/systemd/system/reth.service
```

Paste the configuration parameters below into the file:

<pre class="language-bash"><code class="lang-bash">[Unit]
Description=Reth Execution Client (Holesky)
After=network.target
Wants=network.target

[Service]
User=reth
Group=reth
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/reth node \
  --chain holesky \
  --datadir=/var/lib/reth \
  --log.file.directory=/var/lib/reth/logs \
  --authrpc.jwtsecret=/var/lib/jwtsecret/jwt.hex \
  --full \
  --port 30304 \
  --http \
  --http.api eth,web3,net,txpool,debug,trace \
<strong>  --http.addr &#x3C;Internal_IP_address> \
</strong>  --http.port 8547 \
  --ws \
  --ws.addr &#x3C;Internal_IP_address> \
  --ws.port 8548 \
  --metrics 127.0.0.1:6060
  
[Install]
WantedBy=default.target
</code></pre>

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Reth configuration summary:**

1. `--chain`: Run the on the Holesky testnet
2. `--datadir`: The directory for Reth to store the blockchain data of the execution layer
3. `--log.file.directory`: The path to put log files in
4. `--authrpc.jwtsecret`: The directory pointing to the JWT secret we generated earlier
5. `--full:` Run full node.
6. `--port`: Sets the port used for peer-to-peer communication. Defaults to 30303.
7. `--http`: Enables the HTTP-RPC service on http and websocket. This is so that DVT clients such as the Diva service can connect to your execution client &#x20;
8. `--http.api`: Rpc modules to be configured for the HTTP server.
9. `--http.addr`: Sets the IP address to connect to the JSON RPC service. Use the internal IP address of your device here (check by running `ip a`) - e.g. `192.168.x.x`. Defaults to `127.0.0.1` otherwise
10. `--http.port`: Sets the port to connect to the HTTP-RPC service that will be used by the DVT services. You may choose any unused port number but remember to allow incoming connections into your chosen port in your firewall (`ufw`) rules. Defaults to 8545.
11. `--ws`: ws=Websocket. Enable the WS-RPC server. This is so that DVT clients can connect to your execution client.
12. `--ws.addr`: Websocket server address to listen on. Used by DVT clients.
13. `--ws.port`: Websocket server port to listen on. Used by DVT clients.
14. `--metrics`: Enable monitoring metrics on the Reth service.

### Start Reth

Reload the systemd daemon to register the changes made, start Reth, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start reth.service
sudo systemctl status reth.service
```

**Expected output:** The output should say Reth is **“active (running)”.** Press CTRL-C to exit and Reth will continue to run. It should take around 6 hours for Reth to sync on the Holesky testnet.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Use the following command to check the logs of Reth’s syncing process. Watch out for any warnings or errors.

```bash
sudo apt install ccze -y
sudo journalctl -fu reth -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Press `CTRL-C` to exit.

{% hint style="info" %}
For more details on interpreting the Reth journalctl logs, head [here](https://geth.ethereum.org/docs/fundamentals/logs).
{% endhint %}

If the Reth service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable reth.service
```

**Expected output:**

```
Created symlink /etc/systemd/system/default.target.wants/reth.service → /etc/systemd/system/reth.service.
```

## Resources:

1. Releases: [https://github.com/paradigmxyz/reth/releases](https://github.com/paradigmxyz/reth/releases)
2. Documentation: [https://reth.rs/](https://reth.rs/)
3. Telegram: [https://t.me/paradigm\_reth](https://t.me/paradigm\_reth)
