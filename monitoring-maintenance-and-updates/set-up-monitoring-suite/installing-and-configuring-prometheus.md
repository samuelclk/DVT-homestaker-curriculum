# Installing & configuring Prometheus

### Download and install Prometheus

[Download](https://prometheus.io/download/) the latest version of Prometheus and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/prometheus/prometheus/releases/download/v2.50.1/prometheus-2.50.1.linux-amd64.tar.gz
echo "936f3777f8c3a4a90d3c58a6f410350d5932c79367b99771d002bd36e48bd05b prometheus-2.50.1.linux-amd64.tar.gz" | sha256sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum. Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

_**Expected output:** Verify output of the checksum verification_

```
prometheus-2.50.1.linux-amd64.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `/usr/local/bin` and `/etc/prometheus` directories for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf prometheus-2.50.1.linux-amd64.tar.gz
sudo cp prometheus-2.50.1.linux-amd64/prometheus /usr/local/bin/
sudo cp prometheus-2.50.1.linux-amd64/promtool /usr/local/bin/
sudo cp -r prometheus-2.50.1.linux-amd64/consoles /etc/prometheus
sudo cp -r prometheus-2.50.1.linux-amd64/console_libraries /etc/prometheus
sudo rm -r prometheus-2.50.1.linux-amd64 prometheus-2.50.1.linux-amd64.tar.gz
```

### Configure Prometheus&#x20;

Create an account (`prometheus`) without server access for Prometheus to run as a background service. This restricts potential attackers to only the Prometheus service in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false prometheus
```

Create a directory for Prometheus to store the monitoring data. Then set the owner of this and the `/etc/prometheus` directory to `prometheus` so that this user can read and write to the directories.

```bash
sudo mkdir -p /var/lib/prometheus
sudo chown -R prometheus:prometheus /var/lib/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus
```

Create a configuration file so that Prometheus knows where to pull data from.

```bash
sudo nano /etc/prometheus/prometheus.yml
```

Paste the configuration parameters below into the file:

**1) General + execution client parameters:**

{% tabs %}
{% tab title="Nethermind" %}
```
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets:
          - localhost:9090
  - job_name: node_exporter
    static_configs:
      - targets:
          - localhost:9100
  - job_name: nethermind
    static_configs:
      - targets:
          - localhost:9091
```
{% endtab %}

{% tab title="Besu" %}
```
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets:
          - localhost:9090
  - job_name: node_exporter
    static_configs:
      - targets:
          - localhost:9100
  - job_name: 'besu'
    scrape_interval: 15s
    scrape_timeout: 10s
    metrics_path: /metrics
    scheme: http
    static_configs:
      - targets:
          - localhost:9545
```
{% endtab %}
{% endtabs %}

**2) Consensus client parameters:**

According to your selected consensus client, append the following block to the general + execution client parameters above.

{% tabs %}
{% tab title="Teku" %}
```
  - job_name: "teku-dev"
    scrape_timeout: 10s
    metrics_path: /metrics
    scheme: http
    static_configs:
      - targets: ["localhost:8008"]

```
{% endtab %}

{% tab title="Nimbus" %}
```
  - job_name: 'Nimbus'
    metrics_path: /metrics
    static_configs:
      - targets: ['localhost:8008']
```
{% endtab %}

{% tab title="Lodestar" %}
```
  - job_name: 'lodestar_beacon'
    metrics_path: /metrics
    static_configs:
      - targets: ['localhost:8008']
  - job_name: 'lodestar_validator'
    metrics_path: /metrics
    static_configs:
      - targets: ['localhost:5064']
```
{% endtab %}
{% endtabs %}

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.

Next, create a systemd configuration file for the Prometheus service to run in the background.

```bash
sudo nano /etc/systemd/system/prometheus.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target
[Service]
Type=simple
User=prometheus
Group=prometheus
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries
[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.

### Start the Prometheus service

Reload the systemd daemon to register the changes made, start Prometheus, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start prometheus.service
sudo systemctl status prometheus.service
```

**Expected output:** The output should say Prometheus is **“active (running)”.** Press CTRL-C to exit and Prometheus will continue to run.

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-10 at 5.14.03 PM.png" alt=""><figcaption><p>sudo systemctl status prometheus.service</p></figcaption></figure>

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu prometheus -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-10 at 5.15.25 PM.png" alt=""><figcaption></figcaption></figure>

Press `CTRL-C` to exit.

If the Prometheus service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable prometheus.service
```
