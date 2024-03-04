# Besu

## Updating Besu

### Download Besu and configure the service

[Download](https://github.com/hyperledger/besu/releases) the latest version of Besu and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://hyperledger.jfrog.io/artifactory/besu-binaries/besu/24.1.2/besu-24.1.2.tar.gz
echo "082db8cf4fb67527aa0dd757e5d254b3b497f5027c23287f9c0a74a6a743bf08 besu-24.1.2.tar.gz" | sha256sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum (see below). Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to verify the correct checksum according to the downloaded version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

<figure><img src="../../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

_**Expected output:** Verify output of the checksum verification_

```
besu-24.1.2.tar.gz: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf besu-24.1.2.tar.gz
sudo cp -a besu-24.1.2 /usr/local/bin/besu
sudo rm -r besu-24.1.2.tar.gz besu-24.1.2
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
