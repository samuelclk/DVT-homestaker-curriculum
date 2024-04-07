# Installing & configuring Grafana

### Installing dependencies - Pushgateway

This dependency is specific to the Nethermind execution layer client to enable the Grafana monitoring dashboard to work properly.

Download the latest version and the checksums list.

```sh
curl -LO https://github.com/prometheus/pushgateway/releases/download/v1.7.0/pushgateway-1.7.0.linux-amd64.tar.gz
curl -LO https://github.com/prometheus/pushgateway/releases/download/v1.7.0/sha256sums.txt
```

Print the checksums list and look for the corresponding sha256 checksum according to your downloaded version - e.g.

```sh
cat sha256sums.txt
```

Copy the checksum string and replace it in the first string component below to verify the checksum of your downloaded zip file. For your convenience, the actual string has been pre-filled.

```sh
echo "6fedc802c07f4d83a70d45b73bae9d3ab026f827ecf99c026fc896335e5211b7  pushgateway-1.7.0.linux-amd64.tar.gz" | sha256sum --check
```

_**Expected output:** Verify output of the checksum verification_

```
pushgateway-1.7.0.linux-amd64.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice.&#x20;

```sh
tar xvf pushgateway-1.7.0.linux-amd64.tar.gz
cd pushgateway-1.7.0.linux-amd64
sudo cp pushgateway /usr/local/bin
```

Then, clean up the duplicated copies.

```sh
cd
rm -r pushgateway-1.7.0.linux-amd64 pushgateway-1.7.0.linux-amd64.tar.gz
```

Create an account (`pushgateway`) without server access for Pushgateway to run as a background service.

```sh
sudo useradd --no-create-home --shell /bin/false pushgateway
```

Create the systemd configuration file to run Pushgateway.

```sh
sudo nano /etc/systemd/system/pushgateway.service
```

Paste the following contents into the configuration file.

```
[Unit]
Description=Prometheus Pushgateway
After=network.target
Wants=network.target

[Service]
User=pushgateway
Group=pushgateway
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/pushgateway

[Install]
WantedBy=default.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.

Start the Pushgateway service.

```sh
sudo systemctl daemon-reload
sudo systemctl start pushgateway
sudo systemctl enable pushgateway
sudo systemctl status pushgateway
```

**Expected output:** The output should say Pushgateway is **“active (running)”.** Press CTRL-C to exit and Pushgateway will continue to run.

Monitor for causes of error messages otherwise.&#x20;

```sh
sudo journalctl -fu pushgateway -o cat | ccze -A
```

### Download and install Grafana

Install Grafana using the APT package manager - Download the Grafana GPG key, add Grafana to the APT sources, refresh the apt cache, and check that Grafana has been added to the APT repository.

```bash
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo apt update
apt-cache policy grafana
```

**Expected output:** Ensure the top-most version matches with latest version here - [https://grafana.com/grafana/download](https://grafana.com/grafana/download)

```bash
grafana:
  Installed: (none)
  Candidate: 10.0.3
  Version table:
     10.0.3 500
        500 <https://packages.grafana.com/oss/deb> stable/main amd64 Packages
     10.0.2 500
        500 <https://packages.grafana.com/oss/deb> stable/main amd64 Packages
     10.0.1 500
        500 <https://packages.grafana.com/oss/deb> stable/main amd64 Packages
```

Run the installation command.

```bash
sudo apt install -y grafana
```

### Start the Grafana server.

```bash
sudo systemctl start grafana-server
sudo systemctl status grafana-server
```

The output should say Grafana is **“active (running)”.** Press CTRL-C to exit and Grafana will continue to run.

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu grafana-server -o cat | ccze -A
```

Press `CTRL-C` to exit.

If the Grafana service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable grafana-server
```

### Configure the Grafana Dashboard

1. go to `http://<yourserverip>:3000/`
2. Enter `admin` for both username and password
3. Select `Data Sources` and click on `Add data source` , then choose **Prometheus** and enter [**http://localhost:9090**](http://localhost:9090) for the URL
4. Setup dashboards - On the left menu bar, click on **Dashboards >> Import**
   * Execution client dashboard&#x20;
     * **Nethermind:** Paste the JSON text from [here](https://github.com/samuelclk/ETH\_full\_home\_staking\_guide/blob/main/monitoring-maintenance-and-updates/set-up-monitoring-suite/Nethermind-grafana-JSON)
     * **Besu:** Enter the dashboard ID - `10273`
     * **Geth:** Enter the dashboard ID - `13877`
   * Consensus client dashboard&#x20;
     * **Teku:** Enter the dashboard ID - `16737`
       * **Nimbus:** Paste the JSON text from the options below
         * [Nimbus Github](https://raw.githubusercontent.com/status-im/nimbus-eth2/stable/grafana/beacon\_nodes\_Grafana\_dashboard.json)
         * [Metanull](https://github.com/metanull-operator/eth2-grafana/blob/master/nimbus/eth2-grafana-nimbus-dashboard.json)
       * **Lodestar:** Paste the JSON text from [here](https://raw.githubusercontent.com/ChainSafe/lodestar/stable/dashboards/lodestar\_summary.json)
       * **Lighthouse**: Paste the JSON text from [here](https://raw.githubusercontent.com/sigp/lighthouse-metrics/master/dashboards/Summary.json)
       * **Prysm:** Paste the JSON text from [here](https://raw.githubusercontent.com/GuillaumeMiralles/prysm-grafana-dashboard/master/less\_10\_validators.json)
   * Node Exporter dashboard - Paste the JSON text [here](https://github.com/samuelclk/ETH\_full\_home\_staking\_guide/blob/main/monitoring-maintenance-and-updates/set-up-monitoring-suite/Node-exporter-grafana-json)
5. Select `Prometheus` from the "Select a Prometheus data source here" drop down field.

### Screenshot samples of Grafana Dashboard

#### Execution client:

<figure><img src="../../.gitbook/assets/image (156).png" alt=""><figcaption></figcaption></figure>

#### Consensus client:

<figure><img src="../../.gitbook/assets/image (157).png" alt=""><figcaption></figcaption></figure>

#### Node Exporter:

<figure><img src="../../.gitbook/assets/image (158).png" alt=""><figcaption></figcaption></figure>
