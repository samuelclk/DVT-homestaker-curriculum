# Techne Bronze Speedrun

## Requirements

Obol Techne Bronze Credentials require node operators to run 50 testnet validator keys as a distributed validator cluster for 3 weeks, where Obol will fund the testnet ETH required to activate the validator keys.

{% hint style="info" %}
There seems to be a bug with the Obol Holesky Launchpad where we are unable to generate >1 validator key via DKG so we will be doing some workarounds using the CLI method.
{% endhint %}

### Hardware (Holesky)

* CPU: 4 cores
* RAM: 16GB
* SSD: 350GB
* OS: Ubuntu 24.04

## Set up Splitter contract

Choose your cluster members and form your cluster members by creating a Splitter contract that splits the execution layer rewards among all operators.

{% embed url="https://splits.org/" %}

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
docker run --rm -v "$(pwd):/opt/charon" -u $(id -u):$(id -g) obolnetwork/charon:v1.1.0 create enr
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

## Create the Cluster Definition file

Appoint a cluster leader among your cluster.&#x20;

The cluster leader will collect everyone's `ENR public key` and run the following command.

**Replace:**

* `--name` flag with your choice of name for your cluster
* `--fee-recipient-addresses` flag with your cluster's actual `Splitter contract address`&#x20;
* `--operator-enrs` flag with the actual `ENR public key` of your cluster members

<pre class="language-sh"><code class="lang-sh"><strong>cd
</strong><strong>docker run --rm -v "$(pwd):/opt/charon" -u $(id -u):$(id -g) obolnetwork/charon:v1.1.0 create dkg \
</strong>--name="&#x3C;Cluster_name>" --num-validators=50 \
--fee-recipient-addresses="&#x3C;Splitter_contract_address>" \
--withdrawal-addresses="0x17E6F6270A101dc7687Cc9899889819EeAF8253f" \
--network="holesky" \
--operator-enrs=\
"enr:-HW4QO5ci0ykiIxKD9CPoK0DzqrtV85jaXRgHeUKJyX8KmAhOxe5lD5MBGTNf9vJClUIeLzqj9awJtXsxWGciTI8BgSAgmlkgnY0iXNlY3AyNTZrMaEDivyOXAZbkL8sqbSuCQ0NBa3qiGxgrU_3pda02C1A0HQ",\
"enr:-HW4QFOFj99TaauvirkSRmEphR1UkevkegYJYkBzzLK3b2kwLmEHxE_E8q_BTJY0pN1vIBPq4rZ2Kih-K11MOAC6VimAgmlkgnY0iXNlY3AyNTZrMaECV0SXHBiWDjucuAdRPbJA19ExP73EvDlYJGEwyr4fYZY",\
"enr:-HW4QFRRhXE1aBufxYYqXLp5_QTCpAmct6UsKt-MMqbaNNCcNpVMC-icRYwAwXalh0Y2cIIhVocLVRPcZSQrev8osJyAgmlkgnY0iXNlY3AyNTZrMaECerwvVkvu8ZM_vALT10Rtp0YiFth7R5JrqP-iTmXwzAk",\
"enr:-HW4QIq63_axsvYq3D24gcZSFTKjrSl0nWwXVeYc29mJV-avEzVMKUcaxjM9wYnz4GWIT4JQqASJfu6M-HK5RH-zE8aAgmlkgnY0iXNlY3AyNTZrMaEDafXZh594s4ft5El40JmGt1qVsOdW5gv2qzaVtC2LTLc"
</code></pre>

{% hint style="info" %}
Keep the `--withdrawal-addresses flag` as is because we want Obol to fund our testnet validators.
{% endhint %}

A `cluster-definition.json` file will be generated and saved in the `~/.charon` directory.

Distribute this `cluster-definition.json` file to each cluster member to place within their own `~/.charon` directories.

{% hint style="info" %}
You can use any messaging app for this because only the public part of the ENR key is exposed in this file.
{% endhint %}

## Run the DKG ceremony

```sh
cd
docker run --rm -v "$(pwd):/opt/charon" -u $(id -u):$(id -g) obolnetwork/charon:v1.1.0 dkg --publish
```

{% hint style="info" %}
All cluster members need to run the DKG service within 30 minutes of one another and keep it running until the ceremony is complete.
{% endhint %}

Once the DKG ceremony is completed, set the permissions of the `charon-enr-private-key` to 666&#x20;

```sh
sudo chmod +x ~/.charon
sudo chmod 666 ~/.charon
sudo chmod 644 ~/.charon/charon-enr-private-key
```

## Setup ETH Docker

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
```

Start ETH Docker.

```sh
ethd up
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

## Securing your device

### Firewall Rules

```sh
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp # for SSH
sudo ufw allow 30303 # for the EL
sudo ufw allow 9000 # for the CL
sudo ufw allow 3000 # for the native Grafana
sudo ufw enable
```

### Other Security SOPs

{% content-ref url="../../linux-os-networking-and-security/networking-and-network-security.md" %}
[networking-and-network-security.md](../../linux-os-networking-and-security/networking-and-network-security.md)
{% endcontent-ref %}

{% content-ref url="../../linux-os-networking-and-security/device-level-security-setup.md" %}
[device-level-security-setup.md](../../linux-os-networking-and-security/device-level-security-setup.md)
{% endcontent-ref %}

## Support

{% embed url="https://t.me/stakesaurus" %}

## Donations

#### If you found this helpful, consider supporting Stakesaurus in one of few ways [here](https://dvt-homestaker.stakesaurus.com/#if-you-found-this-helpful-consider-supporting-stakesaurus-in-one-of-two-ways-below)!
