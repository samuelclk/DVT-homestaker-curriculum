# Besu

## Updating Besu

### Download the latest version

[Download](https://github.com/hyperledger/besu/releases) the latest version of Besu and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://hyperledger.jfrog.io/artifactory/besu-binaries/besu/23.10.2/besu-23.10.2.tar.gz
echo "255818a5c6067a38aa8b565d8f32a49a172a7536a1d370673bbb75f548263c2c besu-23.10.2.tar.gz" | sha256sum --check
```

_**Expected output:** Verify output of the checksum verification_

```
besu-23.10.2.tar.gz: OK
```

Stop the Besu service

```
sudo systemctl stop besu.service
```

Extract the files, delete the previous version, and move the new version into the `(/usr/local/bin)` directory. Then, clean up the duplicated copies.

```bash
tar xvf besu-23.10.2.tar.gz
sudo rm -r /usr/local/bin/besu
sudo cp -a besu-23.10.2 /usr/local/bin/besu
rm -r besu-23.10.2.tar.gz besu-23.10.2
```

### Restart the Besu service

Reload the systemd daemon to register the changes made, start Besu, and check its status to make sure its running.

```bash
sudo systemctl start besu.service
sudo systemctl status besu.service
```

Use the following command to check the logs of Besu’s syncing process. Watch out for any warnings or errors.

```bash
sudo journalctl -fu besu -o cat | ccze -A
```

Press `CTRL-C` to exit.

## Pruning Besu

Besu does not provide a pruning mode and requires a full resync when your node runs out of space. Disk consumption grows by \~8GB/week.

Fortunately, Besu is able to sync in a very short time (\~1.5 days) if you have at least 32GB of RAM by enabling the `--Xplugin-rocksdb-high-spec-enabled` flag.

To resync, simply delete the existing Besu database and restart the service.

```sh
sudo systemctl stop besu.service
sudo rm -r /var/lib/besu/*
sudo systemctl start besu.service
```

Monitor logs for errors.

```sh
sudo journalctl -fu besu -o cat | ccze -A
```
