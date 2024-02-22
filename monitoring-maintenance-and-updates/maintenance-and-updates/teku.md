# Teku

## Updating Teku

### Download the latest version

[Download](https://github.com/ConsenSys/teku/releases) the latest version of Teku and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://artifacts.consensys.net/public/teku/raw/names/teku.tar.gz/versions/23.12.1/teku-23.12.1.tar.gz
echo "0dcb49ac758b79779324b28c600d299bd9b30ed6b505e4a66fbb7a8797b9832f teku-23.12.1.tar.gz" | sha256sum --check
```

_**Expected output:** Verify output of the checksum verification._

```
teku-23.12.1.tar.gz: OK
```

Stop the Teku beacon node and validator client services.

```bash
sudo systemctl stop tekubeacon.service tekuvalidator.service
```

Extract the files, delete the previous version, and move the new version into the `(/usr/local/bin)` directory. Then, clean up the duplicated copies.

```bash
tar xvf teku-23.12.1.tar.gz
sudo rm -r /usr/local/bin/teku
sudo cp -a teku-23.12.1 /usr/local/bin/teku
rm -r teku-23.12.1.tar.gz teku-23.12.1
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
