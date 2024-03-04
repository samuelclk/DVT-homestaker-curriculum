# Nimbus

## Updating Nimbus

## Download Nimbus

[Download](https://github.com/status-im/nimbus-eth2/releases) the latest version of Nimbus, extract the zipped file, and then run the checksum verification process to ensure that the "nimbus\_beacon\_node" and "nimbus\_validator\_client" files have not been tampered with during download.

<pre class="language-bash"><code class="lang-bash">cd
curl -LO https://github.com/status-im/nimbus-eth2/releases/download/v24.2.2/nimbus-eth2_Linux_amd64_24.2.2_fc9c72f0.tar.gz
<strong>tar xvf nimbus-eth2_Linux_amd64_24.2.2_fc9c72f0.tar.gz
</strong><strong>cd nimbus-eth2_Linux_amd64_24.2.2_fc9c72f0/build
</strong>echo "ad062a475edbabb79882a85e1ba93d739d6614fdece382b65381211d07b9dc11487aedca15df0d540ba384866ed0ff0044989ce8d7c39054b2cad40d92022719 nimbus_beacon_node" | sha512sum --check
echo "1a5bfc3e5ba6e8b682572b8c7f74894b785191a4ba1ebf3d59203dc9dc1ec47b49c4d128aa433b0b44315ac574cbb7d924ab4241d60e2b9b141c61e504ab8dab  nimbus_validator_client" | sha512sum --check
</code></pre>

{% hint style="info" %}
Each downloadable file comes with it's own checksum. Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

_**Expected output:** Verify output of the checksum verification._

```
nimbus_beacon_node: OK
nimbus_validator_client: OK
```

If checksum is verified, extract the consensus client and validator client binaries into the `(/usr/local/bin)` directory (as a best practice). Then, clean up the duplicated copies.

```bash
cd ~/nimbus-eth2_Linux_amd64_24.2.2_fc9c72f0/build
sudo cp nimbus_beacon_node nimbus_validator_client /usr/local/bin
cd
rm -r nimbus-eth2_Linux_amd64_24.2.2_fc9c72f0 nimbus-eth2_Linux_amd64_24.2.2_fc9c72f0.tar.gz
```

### Restart the Nimbus services

```bash
sudo systemctl start nimbusbeacon.service nimbusvalidator.service
sudo systemctl status nimbusbeacon.service nimbusvalidator.service
```

Monitor journal logs using

```bash
sudo journalctl -fu nimbusbeacon -o cat | ccze -A
sudo journalctl -fu nimbusvalidator -o cat | ccze -A
```

## Pruning Nimbus

Consensus clients take up a small amount of disk space when compared to execution clients. However, you can still free up \~200GB by pruning it if your validator node has been running for a while.

To prune consensus clients, simply delete the existing database and restart the beacon service with `checkpoint sync` enabled.&#x20;

```sh
sudo systemctl stop nimbusbeacon.service
sudo rm -r /var/lib/nimbus_beacon/*
sudo systemctl start nimbusbeacon.service
sudo systemctl status nimbusbeacon.service
```

Monitor logs for errors.

```sh
sudo journalctl -fu tekubeacon -o cat | ccze -A
```
