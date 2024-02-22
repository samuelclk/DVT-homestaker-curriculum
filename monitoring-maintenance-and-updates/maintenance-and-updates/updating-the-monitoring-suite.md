# Updating the monitoring suite

## Updating Prometheus

### Download the latest version

[Download](https://prometheus.io/download/) the latest version of Prometheus and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/prometheus/prometheus/releases/download/v2.45.0/prometheus-2.45.0.linux-amd64.tar.gz
echo "1c7f489a3cc919c1ed0df2ae673a280309dc4a3eaa6ee3411e7d1f4bdec4d4c5 prometheus-2.45.0.linux-amd64.tar.gz" | sha256sum --check
```

_**Expected output:** Verify output of the checksum verification_

```
prometheus-2.45.0.linux-amd64.tar.gz: OK
```

### Replace existing version

If checksum is verified, extract the files and move them into the `/usr/local/bin` and `/etc/prometheus` directories for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf prometheus-2.45.0.linux-amd64.tar.gz
sudo cp prometheus-2.45.0.linux-amd64/prometheus /usr/local/bin/
sudo cp prometheus-2.45.0.linux-amd64/promtool /usr/local/bin/
sudo cp -r prometheus-2.45.0.linux-amd64/consoles /etc/prometheus
sudo cp -r prometheus-2.45.0.linux-amd64/console_libraries /etc/prometheus
sudo rm prometheus-2.45.0.linux-amd64.tar.gz
sudo rm -r prometheus-2.45.0.linux-amd64
```

### Restart the service

Reload the systemd daemon, restart the service, and monitor the journal logs.

```bash
sudo systemctl daemon-reload
sudo systemctl restart prometheus.service
sudo systemctl status prometheus.service
```

**Expected output:** The service should say it is **"active (running)".**

Check the journal logs to make sure there are no error messages.

```bash
sudo journalctl -fu prometheus -o cat | ccze -A
```

## Updating Node Exporter

### Download the latest version

[Download](https://prometheus.io/download/#node\_exporter) the latest version of Node Exporter and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.6.1/node_exporter-1.6.1.linux-amd64.tar.gz
echo "ecc41b3b4d53f7b9c16a370419a25a133e48c09dfc49499d63bcc0c5e0cf3d01 node_exporter-1.6.1.linux-amd64.tar.gz" | sha256sum --check
```

_**Expected output:** Verify output of the checksum verification_

```
node_exporter-1.6.1.linux-amd64.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf node_exporter-1.6.1.linux-amd64.tar.gz
sudo cp node_exporter-1.6.1.linux-amd64/node_exporter /usr/local/bin
rm node_exporter-1.6.1.linux-amd64.tar.gz
rm -r node_exporter-1.6.1.linux-amd64
```

### Restart the service

Reload the systemd daemon, restart the service, and monitor the journal logs.

```bash
sudo systemctl daemon-reload
sudo systemctl restart node_exporter.service
sudo systemctl status node_exporter.service
```

**Expected output:** The service should say it is **"active (running)".**

Check the journal logs to make sure there are no error messages.

```bash
sudo journalctl -fu node_exporter -o cat | ccze -A
```

## Updating Grafana

Updating Grafana is done via the Linux APT packages as part of the overall OS update. Run the following command:

```bash
sudo apt update -y && sudo apt upgrade -y
```

Then, reboot the system.

```bash
sudo reboot
```
