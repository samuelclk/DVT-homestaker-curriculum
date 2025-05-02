# Techne Bronze Speedrun (Launchpad)

## Requirements

Obol Techne Bronze Credentials require node operators to run 50 testnet validator keys as a distributed validator cluster for 3 weeks, where Obol will fund the testnet ETH required to activate the validator keys.

### Hardware (Holesky)

* CPU: 4 cores
* RAM: 16GB
* SSD: 350GB
* OS: Ubuntu 24.04

## Install dependencies

Install Docker.

```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

sudo groupadd docker
sudo usermod -aG docker $USER
```

Exit and re-login.

```sh
exit
```

## Generate your Obol ENR

```sh
cd
# Use docker to create an ENR. Backup the file `.charon/charon-enr-private-key`.
docker run --rm -v "$(pwd):/opt/charon" -u $(id -u):$(id -g) obolnetwork/charon:v1.3.0 create enr
```

You should expect to see a console output like this:

```
Created ENR private key: .charon/charon-enr-private-key
enr:-JG4QGQpV4qYe32QFUAbY1UyGNtNcrVMip83cvJRhw1brMslPeyELIz3q6dsZ7GblVaCjL_8FKQhF6Syg-O_kIWztimGAYHY5EvPgmlkgnY0gmlwhH8AAAGJc2VjcDI1NmsxoQKzMe_GFPpSqtnYl-mJr8uZAUtmkqccsAx7ojGmFy-FY4N0Y3CCDhqDdWRwgg4u
```

Save the `ENR public key` in a text file on your laptop. You will need to use this in the next section.&#x20;

The `ENR public key` is denoted as `enr:-JG4QGQpV4qYe32QFUAbY1UyGNtNcrVMip83cvJRhw1brMslPeyELIz3q6dsZ7GblVaCjL_8FKQhF6Syg-O_kIWztimGAYHY5EvPgmlkgnY0gmlwhH8AAAGJc2VjcDI1NmsxoQKzMe_GFPpSqtnYl-mJr8uZAUtmkqccsAx7ojGmFy-FY4N0Y3CCDhqDdWRwgg4u` in the example output above.

### Restarting from scratch

If you want to redo the whole process, remove the existing `charon-distributed-validator-node` folder before re-running the set of commands above.&#x20;

```sh
cd
sudo rm -r .charon
```

## Register your ENRs

Appoint a cluster leader among your cluster.

The cluster leader will collect the wallet addresses of all members and configure the parameters of your cluster on the[ Obol Launchpad](https://hoodi.launchpad.obol.org/).

{% embed url="https://hoodi.launchpad.obol.org/" %}

1. Select Create a cluster with a group >> read, click through, & sign the disclaimers with your wallet
2. Choose your cluster name and size (4, 7, or 10)
3. Input the wallet address of each operator
4. Set validators field to 50 (i.e., generate 50 validator keys)
5. Select `SPLIT ONLY REWARDS` under Withdrawal Configuration.&#x20;
   1. Set the `Principal Recipient` to `0x17E6F6270A101dc7687Cc9899889819EeAF8253f` because we want Obol to fund our testnet validators.
   2. Input the wallet addresses and the percentage split of each operator&#x20;
6. `Deploy 'Split Rewards' Contract` (1 transaction) >> `Confirm and sign` (3 transactions)

<details>

<summary>Screenshots</summary>

<img src="../../.gitbook/assets/image (4) (1).png" alt="" data-size="original">

![](<../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png>)

![](<../../.gitbook/assets/image (2) (1) (1) (1) (1).png>)

![](<../../.gitbook/assets/image (3) (1) (1).png>)

</details>

Copy the group invite link and share it with your other cluster members.

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

They will need to accept the cluster configuration, input their respective `ENR Public Keys` , and then sign a message (2 transactions) with their respective wallet addresses set during the cluster creation step.

## DKG Ceremony

Once all members have accepted the cluster configuration, everyone can now proceed to the distributed key generation (DKG) phase.

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

Each cluster member copies the DKG command generated for them and runs it on their own machine.

<figure><img src="../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
All cluster members need to run the DKG service within 30 minutes of one another and keep it running until the ceremony is complete.
{% endhint %}

Once the DKG ceremony is completed, **back up the `.charon` folder containing the following important files:**&#x20;

* charon-enr-private-key
* cluster-lock.json
* validator\_keys/
* deposit-data.json

**Back up using one of the 2 methods below.**

{% tabs %}
{% tab title="secure copy" %}
This is the most secure method but requires having SSH access to your server from your laptop's terminal (i.e., after manually adding SSH public keys to your server).&#x20;

```sh
scp -i PATH_TO_SSH_PRIVATE_KEY -r USER@EXTERNAL_IP_ADDRESS:~/.charon $HOME/Documents
```

Your .charon folder will now be found on your laptop's `Documents` folder. Note that `.charon` is a hidden folder so you will not see it being displayed in your file system.
{% endtab %}

{% tab title="base64" %}
This is an alternative method if you have not set up SSH access to your server from your laptop's terminal. It is less secure as you will expose your files temporarily on your terminal.

* Zip up the .charon folder and convert the zipped file into a base64 string. Then print out the resulting contents.

```sh
sudo tar -czvf - $HOME/.charon | base64 > dot_charon.tar.gz.b64
cat dot_charon.tar.gz.b64
```

* Copy the entire output carefully and save it in a text editor on your laptop.&#x20;
* Clear your terminal and remove the bash history of your server to minimise exposure of these important files

```sh
clear
history -c
```

#### Restoring the files

To restore base64 encoded files, we basically need to reverse the process above.

Convert the base64 string back into a zipped file and then unzip the resulting archive.

```sh
base64 -d dot_charon.tar.gz.b64 > dot_charon.tar.gz
tar xvf dot_charon.tar.gz
```

The `.charon` folder will be found in the folder you ran the above command in. Run `ls -la` to see it.
{% endtab %}
{% endtabs %}

Then, set the following permissions.&#x20;

```sh
sudo chmod +x ~/.charon
sudo chmod 755 ~/.charon
sudo chmod 600 ~/.charon/charon-enr-private-key
```

## Set up ETH Docker

Each cluster member needs to spin up a "vanilla" validator node.

{% content-ref url="../../automation-tools/eth-docker.md" %}
[eth-docker.md](../../automation-tools/eth-docker.md)
{% endcontent-ref %}

**Changes to note:**

* Set the `Reward Address` to your cluster's Splitter contract.

### Edit the configuration file

Edit the `.env` file in the `eth-docker` folder.

```sh
cd 
nano ~/eth-docker/.env
```

* Append `:lido-obol.yml` in the `COMPOSE_FILE` line.

**Example:**

<figure><img src="../../.gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>

* Change the `CL_NODE` line to **http://charon:3600** (from http://consensus:5052)

**Example:**

<figure><img src="../../.gitbook/assets/image (200).png" alt=""><figcaption></figcaption></figure>

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

## Start your Obol-enabled validator node

Migrate .charon contents into ETHDocker.

```sh
cd
sudo cp -r .charon/* eth-docker/.eth
sudo chown -R $USER:$USER eth-docker/.eth/validator_keys
```

Start ETH Docker.

```sh
ethd up
```

After all your services running via Docker "warmed up" for \~5 minutes, import your validator key shards.&#x20;

```
ethd keys import
```

## Monitoring Charon

Print Obol's Charon logs.

```sh
ethd logs charon -f --tail 20
```

Other monitoring commands under "View Logs" section of the overall ETH Docker page.

{% content-ref url="../../automation-tools/eth-docker.md" %}
[eth-docker.md](../../automation-tools/eth-docker.md)
{% endcontent-ref %}

### Grafana Dashboards

Open up a browser webpage and enter the following URL.

```
http://<VM_external_IP>:3000
```

* Enter **"admin"** for both the username and password.
* Navigate to `Dashboards`. The 2 most common dashboards when running Obol DVTs are `Charon Log Dashboard` and `Charon Overview`&#x20;

## Register your cluster for funding

Cluster leader to fill up the Obol Techne Credentials registration form [here](https://airtable.com/apphbfJ552v5mHpo4/pag7JX1gSNIMzvELA/form).

Copy and paste the `cluster-lock.json` and `deposit-data.json` files from your node to a text editor your laptop so that you can upload them easily onto the form.

```sh
sudo cat ~/.charon/cluster-lock.json
```

```sh
sudo cat ~/.charon/deposit-data.json
```

## Add Obol monitoring credentials

You will receive your monitoring credentials after registrering your cluster with Obol Techne Credentials Progamme and you will need to add it into your ETH Docker configuration.

Open up the `obol-prom.yml` file for editing.

```sh
nano ~/eth-docker/prometheus/obol-prom.yml
```

Replace the `credentials` field with your actual monitoring credentials.

<figure><img src="../../.gitbook/assets/Screenshot 2024-10-12 at 10.45.11 AM.png" alt=""><figcaption><p>e.g., Replace OBOL_PROM_REMOTE_WRITE_TOKEN</p></figcaption></figure>

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Copy the contents over into the `custom-prom.yml` file that is empty by default but already incorporated in the default ETH Docker configurations.&#x20;

```sh
cp ~/eth-docker/prometheus/obol-prom.yml ~/eth-docker/prometheus/custom-prom.yml
```

Then, restart your ETH Docker services.

```sh
ethd down
ethd up
```

## Securing your device

### Firewall Rules

```sh
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp # for SSH
sudo ufw allow 30303 # for the EL
sudo ufw allow 9000 # for the CL
sudo ufw allow 3000 # for the native Grafana
sudo ufw allow 3610 # for Obol P2P
sudo ufw enable
```

Make sure to also configure port forwarding on the ports allowed above.&#x20;

{% content-ref url="../../tips/advanced-networking.md" %}
[advanced-networking.md](../../tips/advanced-networking.md)
{% endcontent-ref %}

### Other Security SOPs

{% content-ref url="../../linux-os-networking-and-security/networking-and-network-security.md" %}
[networking-and-network-security.md](../../linux-os-networking-and-security/networking-and-network-security.md)
{% endcontent-ref %}

{% content-ref url="../../linux-os-networking-and-security/device-level-security-setup.md" %}
[device-level-security-setup.md](../../linux-os-networking-and-security/device-level-security-setup.md)
{% endcontent-ref %}

## Exiting your DV

I'm still working on the way to exit for each CL client. For now I only managed to test it successfully with Lodestar & Teku so we will switch to the those CLs before signing the exit messages.

Delete all the validator keyshards currently imported onto validator client and export their slashing protection databases.

```
ethd keys delete all
```

Bring down your ETH Docker services.

```
ethd down
```

Open the `.env` file for editing and switch your CL client to `lodestar.yml` or `teku.yml` in the `COMPOSE_FILE` line.

```
nano ~/eth-docker/.env
```

Bring your ETH Docker back up.

```
ethd up
```

**Wait for 15 minutes** before re-importing your validator keyshards into the new validator client (comes with the new CL). **Note:** Select _**"N"**_ for "Do all keys have the same password.

{% hint style="info" %}
**IMPORTANT:** DO NOT IMPORT YOUR KEYS BEFORE WAITING FOR 15 MINUTES. If everyone does not wait before importing, your cluster may be slashed.
{% endhint %}

```
ethd keys import
```

Then run the following

{% tabs %}
{% tab title="Lodestar" %}
```sh
docker exec -it eth-docker-validator-1 node /usr/app/packages/cli/bin/lodestar validator voluntary-exit \
--beaconNodes="http://charon:3600" \
--dataDir="/var/lib/lodestar/validators" \
--exitEpoch=256 \
--network=hoodi \
--yes
```


{% endtab %}

{% tab title="Teku" %}
```sh
#WIP
docker exec -it eth-docker-validator-1 /opt/teku/bin/teku voluntary-exit \
--beacon-node-api-endpoint="http://charon:3600/" \
--confirmation-enabled=false \
--validator-keys="/var/lib/teku/validator/key-manager/local:/var/lib/teku/validator/key-manager/local-passwords" \
--epoch=256
```
{% endtab %}

{% tab title="Nimbus" %}
```sh
#WIP
docker exec -it eth-docker-validator-1 /bin/bash -c ' \
    mkdir -p /var/lib/nimbus/wd && \
    cp -r /var/lib/nimbus/ /var/lib/nimbus/wd && \
    /usr/local/bin/nimbus_beacon_node deposits exit --all --epoch=256 \
    --rest-url=http://charon:3600/ --data-dir=/var/lib/nimbus/wd
```
{% endtab %}
{% endtabs %}

## Support

{% embed url="https://t.me/stakesaurus" %}

## Donations

#### If you found this helpful, consider supporting Stakesaurus in one of few ways [here](https://dvt-homestaker.stakesaurus.com/#if-you-found-this-helpful-consider-supporting-stakesaurus-in-one-of-two-ways-below)!
