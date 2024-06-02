# Installing & configuring Node Exporter

### Download and install Node Exporter

[Download](https://prometheus.io/download/#node\_exporter) the latest version of Node Exporter and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.8.1/node_exporter-1.8.1.linux-amd64.tar.gz
echo "fbadb376afa7c883f87f70795700a8a200f7fd45412532cc1938a24d41078011  node_exporter-1.8.1.linux-amd64.tar.gz" | sha256sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum. Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

_**Expected output:** Verify output of the checksum verification_

```
node_exporter-1.8.1.linux-amd64.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf node_exporter-1.8.1.linux-amd64.tar.gz
sudo cp node_exporter-1.8.1.linux-amd64/node_exporter /usr/local/bin
rm -r node_exporter-1.8.1.linux-amd64 node_exporter-1.8.1.linux-amd64.tar.gz
```

### Configure the Node Exporter service

Create an account (`node_exporter`) without server access for Node Exporter to run as a background service. This restricts potential attackers to only the Node Exporter service in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false node_exporter
```

Create a systemd configuration file for the Node Exporter service to run in the background.

```bash
sudo nano /etc/systemd/system/node_exporter.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target
[Service]
User=node_exporter
Group=node_exporter
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/node_exporter
[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.

### Start the Node Exporter service

Reload systemd to register the changes made, start the Node Exporter service, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start node_exporter.service
sudo systemctl status node_exporter.service
```

**Expected output:** The output should say Node Exporter is **“active (running)”.** Press CTRL-C to exit and Node Exporter will continue to run.

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-10 at 5.20.13 PM.png" alt=""><figcaption><p>sudo systemctl status node_exporter.service</p></figcaption></figure>

Use the following command to check the logs of Teku Beacon Node’s syncing process. Watch out for any warnings or errors.

```bash
sudo journalctl -fu node_exporter -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-10 at 5.20.47 PM.png" alt=""><figcaption></figcaption></figure>

Press `Ctrl+C` to exit monitoring.

If the Node Exporter service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable node_exporter.service
```
