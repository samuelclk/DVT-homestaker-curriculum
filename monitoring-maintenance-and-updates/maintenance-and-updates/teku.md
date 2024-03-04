# Teku

## Download Teku

[Download](https://github.com/ConsenSys/teku/releases) the latest version of Teku and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://artifacts.consensys.net/public/teku/raw/names/teku.tar.gz/versions/24.2.0/teku-24.2.0.tar.gz
echo "f7da4109b180e1f1118d6fa13e4d48a964d0f58724d1e6d3fd4a92ddccabab58 teku-24.2.0.tar.gz" | sha256sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum. Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

_**Expected output:** Verify output of the checksum verification._

```
teku-24.2.0.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf teku-24.2.0.tar.gz
sudo cp -a teku-24.2.0 /usr/local/bin/teku
rm -r teku-24.2.0.tar.gz teku-24.2.0
```

### Restart the Teku services

```bash
sudo systemctl start tekubeacon.service tekuvalidator.service
sudo systemctl status tekubeacon.service tekuvalidator.service
```

Monitor journal logs using

```bash
sudo journalctl -fu tekubeacon -o cat | ccze -A
sudo journalctl -fu tekuvalidator -o cat | ccze -A
```

## Pruning Teku

Consensus clients take up a small amount of disk space when compared to execution clients. However, you can still free up \~200GB by pruning it if your validator node has been running for a while.

To prune consensus clients, simply delete the existing database and restart the beacon service with `checkpoint sync` enabled.&#x20;

```sh
sudo systemctl stop tekubeacon.service
sudo rm -r /var/lib/teku_beacon/*
sudo systemctl start tekubeacon.service
sudo systemctl status tekubeacon.service
```

Monitor logs for errors.

```sh
sudo journalctl -fu tekubeacon -o cat | ccze -A
```
