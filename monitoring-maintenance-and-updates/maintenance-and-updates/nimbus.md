# Nimbus

## Updating Nimbus

### Download the latest version

[Download](https://github.com/status-im/nimbus-eth2/releases) the latest version of Nimbus and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/status-im/nimbus-eth2/releases/download/v23.11.0/nimbus-eth2_Linux_amd64_23.11.0_3a527d62.tar.gz
tar xvf nimbus-eth2_Linux_amd64_23.11.0_3a527d62.tar.gz
cd nimbus-eth2_Linux_amd64_23.11.0_3a527d62/build
echo "1f53f58373fa3540028ff17f2a46254f4d9236f844a01fb548359e3241bd9e9791abc3637b474b4e834a08c36d259b84032db01975944d5eb92aef4fbab14821 nimbus_beacon_node" | sha512sum --check
echo "efd1d5f0261b30cfb7e81c3e19ae5f2e2828a1af37a6f85c3151545a1725c68003d7331390ab4b24ac583cf62ccae448755b607c4717a7ec660bb95b4981d9a3  nimbus_validator_client" | sha512sum --check
```

_**Expected output:** Verify output of the checksum verification._

```
nimbus_beacon_node: OK
nimbus_validator_client: OK
```

Stop the Nimbus beacon node and validator client services.

```bash
sudo systemctl stop nimbusbeacon.service nimbusvalidator.service
```

Extract the files, delete the previous version, and move the new version into the `(/usr/local/bin)` directory. Then, clean up the duplicated copies.

```bash
cd ~/nimbus-eth2_Linux_amd64_23.11.0_3a527d62/build
sudo cp nimbus_beacon_node nimbus_validator_client /usr/local/bin
cd
rm -r nimbus-eth2_Linux_amd64_23.11.0_3a527d62 nimbus-eth2_Linux_amd64_23.11.0_3a527d62.tar.gz
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
