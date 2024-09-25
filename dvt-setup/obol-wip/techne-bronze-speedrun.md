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

## Install dependencies

Install Docker.

```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

sudo groupadd docker
sudo usermod -aG docker $USER
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
"enr:-HW4QOGRKbbooOpJkT5QbMnpWKT8_Uz6c3e4t9PuuNPC9M_DdCyAskXetVeqZ8RJxDvBXzWPZHiPWn0G32W09RW6t4GAgmlkgnY0iXNlY3AyNTZrMaEDNPesuI4Gi4uMb85qNnydaZDfKZw0LFwSjun4JTHvLaY",\
"enr:-HW4QNl3vZHbiBbwn-fUKxDGSwCEEhJP7NK64iH_dT7oOF4lJ5XOSqlfwFG4FsEjjb_1hrQdu56JH-Fr1X2DyH493mWAgmlkgnY0iXNlY3AyNTZrMaECcd98w7Xlubs7PubSZvQj6uSmSVpheG3UhPwaQqQ3A58",\
"enr:-HW4QFhNE5dSDytMpp9frzxkME5RtXkfRpsfllWjASfEComrYluy-8t-Vc01fZ4VyhSCqmA98pej7W2CA7TtqdWl8ZqAgmlkgnY0iXNlY3AyNTZrMaEDMe3QaRxnmidfOsPx8nZq5lKYeUaDuzW0mR0lXFGW4P4",\
"enr:-HW4QEUElnbbHlkMR7tSV4mgXTWLFuO2yXw6vNHhLxG4ThkKIMKWgUa2kh4TGmUluvmxYILByCGTTJuw9PKyA453JdaAgmlkgnY0iXNlY3AyNTZrMaEDzturQ9VYUTJQleFXvSPfAYU0Unw_6Dn-lxAJQ3UqcUk"
</code></pre>

{% hint style="info" %}
Keep the `--withdrawal-addresses flag` as is because we want Obol to fund our testnet validators.
{% endhint %}

A `cluster-definition.json` file will be generated and saved in the `~/.charon` directory.

Distribute this `cluster-definition.json` file to each cluster member to place within their own `~/.charon` directories.

{% hint style="info" %}
You can use&#x20;
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
```

## Setup ETH Docker

First, each cluster member needs to spin up a "vanilla" validator node.

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

Append `:lido-obol.yml` in the `COMPOSE_FILE` line.

**Example:**

<figure><img src="../../.gitbook/assets/image (198).png" alt=""><figcaption></figcaption></figure>

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

## Start your Obol-enabled validator node

Migrate .charon contents into ETHDocker.

```sh
cd
cp -r .charon/* eth-docker/.eth
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
