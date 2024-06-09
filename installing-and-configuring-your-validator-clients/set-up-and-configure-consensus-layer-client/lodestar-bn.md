# Lodestar BN

{% hint style="info" %}
**IMPORTANT:** For mainnet usage, the Lodestar (Chainsafe) team only recommends installing via Docker to prevent supply chain attacks on the npm package manager. More details here - [https://chainsafe.github.io/lodestar/install/npm/](https://chainsafe.github.io/lodestar/install/npm/)

Hence, we will be using the Docker method to install Lodestar even for the testnet version so that you can familiarise yourself with this method.
{% endhint %}

Don't feel apprehensive even if this is the first time you are hearing about Docker!&#x20;

We highly encourage the use of all minority clients so the way this setup is designed will be very familiar to the `systemd` method used in this curriculum.

## Installing dependencies - Docker

The script below performs the following:

1. Download and run the official Docker installation script
2. Creates a new user group called "docker"
3. Adds your current Linux user account to this new docker group

```sh
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

sudo groupadd docker
sudo usermod -aG docker $USER
```

Log out and then back in again for the new user group settings to take effect.

```sh
exit
```

```sh
ssh <user>@<IP_address> -p <port_no.> -i <SSH_key> -v
```

## Create a new Lodestar user account

```sh
sudo useradd --no-create-home --shell /bin/false lodestar
```

## Preparing the Lodestar beacon directory

1\) Create a new folder to store the Lodestar consensus client data

2\) Change the owner of this new folder to the `lodestar` user

3\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

4\) Retrieve the UID and GID of the `lodestar` user account to be used in your `docker-compose.yml` file in the next step

```sh
sudo mkdir -p /var/lib/lodestar_beacon
sudo chown -R lodestar:lodestar /var/lib/lodestar_beacon
sudo chmod 700 /var/lib/lodestar_beacon
id lodestar
```

_**Expected output:**_

```
uid=1004(lodestar) gid=1005(lodestar) groups=1005(lodestar)
```

**\*Note:** your actual ids may be different here.&#x20;

## Downloading Lodestar

Create a new folder for the Lodestar consensus client.&#x20;

```sh
cd
sudo mkdir lodestar_beacon
```

Create a `docker-compose.yml` file in the Lodestar folder.

```sh
cd ~/lodestar_beacon
sudo nano docker-compose.yml
```

Paste the following configuration into the `docker-compose.yml` file. **Note:** This is similar to the `systemd` configuration file used in the setup of other clients in this curriculum.

```yaml
services:
  beacon_node:
    image: chainsafe/lodestar:latest
    container_name: lodestar_beacon
    user: <UID>:<GID>
    restart: unless-stopped
    volumes:
      - /var/lib/lodestar_beacon:/var/lib/lodestar_beacon
      - /var/lib/jwtsecret:/var/lib/jwtsecret
    command:
      - beacon
      - --dataDir
      - /var/lib/lodestar_beacon
      - --network
      - holesky
      - --checkpointSyncUrl
      - https://checkpoint-sync.holesky.ethpandaops.io
      - --jwt-secret
      - /var/lib/jwtsecret/jwt.hex
      - --execution.urls
      - http://host.docker.internal:8551
      - --builder
      - --builder.urls
      - http://host.docker.internal:18550
      - --port
      - "9001"
      - --rest
      - --rest.address
      - 0.0.0.0
      - --rest.port
      - "5051"
      - --metrics
      - --metrics.port
      - "8009"
    environment:
      NODE_OPTIONS: --max-old-space-size=8192
    ports:
      - "5051:5051"
      - "9001:9001"
      - "8009:8009"
    extra_hosts:
      - "host.docker.internal:host-gateway"
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.&#x20;

**Now, let's break down what we are configuring in this `yml` file.**

1. `image: chainsafe/lodestar:latest`: Pull and run the latest stable release of Lodestar
2. `container_name: lodestar_beacon`: Name given to this docker container, which can be up to you.
3. `restart: unless-stopped`: Automatically restarts this container when your device restarts unless explicitly stopped by the user.
4. `volumes:`: Binds the folders on your host machine to the folders in the docker container so that they are accessible by the docker container. Here, we are binding the folders used by the `--dataDir` and `--jwt-secret` flags
5. `network_mode: host`: Enables the docker container to share the network namespace with the host -&#x20;
   * i.e. `localhost` refers to both the host and the docker container, ports are shared between the host and the docker container
6. `command:`: The flags to run the Lodestar with. Similar to flags used in the systemd configuration method.
   * Each `-` indicates a line break
   * Variables with a `--` prefix are flags and the others are the values to the flags
   * The first value (`beacon`) instructs Lodestar to run only the consensus client and without the validator client&#x20;
   * `--dataDir`: Specify the directory for Lodestar to store data related to the consensus client
   * `--network`: Run the Consensus Client service on the ETH Holesky testnet
   * `--checkpointSyncUrl`: Enables nearly instant syncing of the Consensus Client by pointing to one of the checkpoint sync URLs here - [https://eth-clients.github.io/checkpoint-sync-endpoints/](https://eth-clients.github.io/checkpoint-sync-endpoints/)
   * `--jwt-secret`: File path to locate the JWT secret we generated earlier
   * `--execution.urls`: URL to connect to the execution layer client
   * `--builder`: Enable connection to external block builders (e.g. via MEV relays)
   * `--builder.urls`: URL to connect to external block builders (e.g. via MEV relays)
   * `--port:` Sets the port for peer-to-peer communication. Defaults to 9000.
   * `--rest`: Allows the validator client to connect to this consensus client. Also allows monitoring endpoints to pull metrics from this service
   * `--rest.address`: Sets the IP address to connect to the REST API of the consensus client that will be used by the DVT clients. Use the internal IP address of your device here (check by running `ip a`) - e.g. `192.168.x.x`. Defaults to `127.0.0.1` otherwise
   * `--rest.port`: Sets the port to connect to the consensus client
   * `--metrics`: Enable monitoring of Consensus Client metrics
   * `--metrics.port`: Sets the port to pull metrics from
7. `environment`: Tells Node.js to allow a larger amount of memory to be used before it starts garbage collection processes to free up memory.
8. `ports:` Maps the ports used by the docker container to the ports of the host device so that they are reachable via the 127.0.0.1  or localhost name spaces.
9. `extra_hosts:` Maps the 127.0.0.1  or localhost name spaces of the host device to host.docker.internal

## Start the Lodestar Consensus Client

1\) Make sure you are in the same folder as the `docker-compose.yml` file you created earlier.

```sh
cd ~/lodestar_beacon
```

&#x20;2\) Start the docker container.

```sh
docker compose up -d
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

3\) Make sure there are no error messages by monitoring the logs for a few minutes.

```sh
docker logs lodestar_beacon -f
```

<figure><img src="../../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

4\) Once you see the `info: Syncing` lines start to appear, you can test the beacon API endpoint directly to make sure the docker container is running correctly.&#x20;

```sh
curl "http://127.0.0.1:5052/eth/v1/node/identity" | jq
```

Expected output:

<figure><img src="../../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

## Verify roots of Checkpoint Sync

* Checkpoint sync URL list: [https://eth-clients.github.io/checkpoint-sync-endpoints/](https://eth-clients.github.io/checkpoint-sync-endpoints/)
* Go to [beaconcha.in](https://beaconcha.in/) on your browser and search for the slot number (`slot`)
* Verify the `State Root` with your output

<figure><img src="../../.gitbook/assets/image (141).png" alt=""><figcaption></figcaption></figure>

## Resources

* Git repository: [https://github.com/ChainSafe/lodestar-quickstart.git](https://github.com/ChainSafe/lodestar-quickstart.git)
* Documentation: [https://chainsafe.github.io/lodestar/](https://chainsafe.github.io/lodestar/)
* Discord: [https://discord.gg/7Gdb4nFh](https://discord.gg/7Gdb4nFh)
