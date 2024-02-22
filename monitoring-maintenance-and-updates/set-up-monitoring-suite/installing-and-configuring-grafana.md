# Installing & configuring Grafana

### Installing dependencies - Pushgateway

This dependency is specific to the Nethermind execution layer client to enable the Grafana monitoring dashboard to work properly.

Download the latest version and the checksums list.

```sh
curl -LO https://github.com/prometheus/pushgateway/releases/download/v1.6.2/pushgateway-1.6.2.linux-amd64.tar.gz
curl -LO https://github.com/prometheus/pushgateway/releases/download/v1.6.2/sha256sums.txt
```

Print the checksums list and look for the corresponding sha256 checksum according to your downloaded version - e.g.

```sh
cat sha256sums.txt
```

Copy the checksum string and replace it in the first string component below to verify the checksum of your downloaded zip file. For your convenience, the actual string has been pre-filled.

```sh
echo "1622ef23cb7f9120ee29e3469d0d4ea513118e53e7a713c129f65ee93ffb0cd1 pushgateway-1.6.2.linux-amd64.tar.gz" | sha256sum --check
```

_**Expected output:** Verify output of the checksum verification_

```
pushgateway-1.6.2.linux-amd64.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice.&#x20;

```sh
tar xvf pushgateway-1.6.2.linux-amd64.tar.gz
cd pushgateway-1.6.2.linux-amd64
sudo cp pushgateway /usr/local/bin
```

Then, clean up the duplicated copies.

```sh
cd
rm -r pushgateway-1.6.2.linux-amd64 pushgateway-1.6.2.linux-amd64.tar.gz
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

```sh
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
sudo journalctl -fu grafana-server
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
4. Setup the execution client (Nethermind) dashboard - On the left menu bar, click on **Dashboards >> Import**
   * On the Import screen, paste the JSON text from [here](https://raw.githubusercontent.com/NethermindEth/metrics-infrastructure/master/grafana/dashboards/nethermind.json)
5. Setup the consensus client (Teku) dashboard - On the left menu bar, click on **Dashboards >> Import**
   * On the Import screen, paste the JSON text from [here](https://raw.githubusercontent.com/sigp/lighthouse-metrics/master/dashboards/Summary.json)
