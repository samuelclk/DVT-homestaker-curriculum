# Lodestar VC

### Create a new user account

```sh
sudo useradd --no-create-home --shell /bin/false lodestar_validator
```

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

\*Skip this step if you are using the Lodestar consensus client.

```sh
sudo useradd --no-create-home --shell /bin/false lodestar_validator
```

## Prepare the validator data directory

1\) Create 3 new folders to store the validator client data, validator keystore, and the validator keystore password

2\) Copy the validator keystores and it's password file into their respective folders

3\) Change the owner of these new folders to the `lodestar` user

4\) Restrict permissions on this new folder such that only the owner is able to read, write, and execute files in this folder

5\) Retrieve the UID and GID of the `lodestar` user account to be used in your `docker-compose.yml` file in the next step

<pre class="language-sh"><code class="lang-sh">sudo mkdir -p /var/lib/lodestar_validator/validator_keystores /var/lib/lodestar_validator/keystore_password
<strong>sudo cp ~/validator_keys/&#x3C;validator_keystore.json> /var/lib/lodestar_validator/validator_keystores
</strong>sudo cp ~/validator_keys/&#x3C;validator_keystore_password.txt> /var/lib/lodestar_validator/keystore_password
sudo chown -R lodestar_validator:lodestar_validator /var/lib/lodestar_validator
sudo chmod 700 /var/lib/lodestar_validator
id lodestar_validator
</code></pre>

{% hint style="info" %}
**Aside from the file extension, the validator\_keystore\_password file will need to be named identically as the validator signing keystore file (e.g. keystore-m-123.json, keystore-m-123.txt)**
{% endhint %}

_**Expected output:**_

```
uid=1004(lodestar) gid=1005(lodestar) groups=1005(lodestar)
```

_**New folders created:**_

```
/var/lib/lodestar_validator/
/var/lib/lodestar_validator/validator_keystores
/var/lib/lodestar_validator/keystore_password
```

## Downloading Lodestar

Create a new folder for the Lodestar validator client.&#x20;

```sh
cd
sudo mkdir lodestar_validator
```

Create a `docker-compose.yml` file in the Lodestar folder.

```sh
cd ~/lodestar_validator
sudo nano docker-compose.yml
```

Paste the following configuration into the `docker-compose.yml` file. **Note:** This is similar to the `systemd` configuration file used in the setup of other clients in this curriculum.

```yaml
services:
  validator_client:
    image: chainsafe/lodestar:latest
    container_name: lodestar_validator
    user: <UID>:<GID>
    restart: unless-stopped
    volumes:
      - /var/lib/lodestar_validator:/var/lib/lodestar_validator
    command:
      - validator
      - --dataDir
      - /var/lib/lodestar_validator
      - --importKeystores
      - /var/lib/lodestar_validator/validator_keystores
      - --importKeystoresPassword
      - /var/lib/lodestar_validator/keystore_password/<validator_signing_keystore_password_file_name>.txt
      - --network
      - holesky
      - --beaconNodes
      - http://<Internal_IP_address>:5052
      - --builder
      - --suggestedFeeRecipient
      - "<your_designated_ETH_wallet_address>"
      - --doppelgangerProtection
      - --metrics
      - --metrics.port
      - "5064"
      - --graffiti
      - "your_graffiti_of_choice"
    environment:
      NODE_OPTIONS: --max-old-space-size=2048
    ports:
      - "5064:5064"
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.&#x20;

**Now, let's break down what we are configuring in this `yml` file.**

1. `image: chainsafe/lodestar:latest`: Pull and run the latest stable release of Lodestar
2. `container_name: lodestar_validator`: Name given to this docker container, which can be up to you.
3. `restart: unless-stopped`: Automatically restarts this container when your device restarts unless explicitly stopped by the user.
4. `volumes:`: Binds the folders on your host machine to the folders in the docker container so that they are accessible by the docker container. Here, we are binding the folders used by the `--dataDir,` `--importKeystores`, and `--importKeystoresPassword` flags
5. `network_mode: host`: Enables the docker container to share the network namespace with the host -&#x20;
   * i.e. `localhost` refers to both the host and the docker container, ports are shared between the host and the docker container
6. `command:`: The flags to run the Lodestar with. Similar to flags used in the systemd configuration method.
   * Each `-` indicates a line break
   * Variables with a `--` prefix are flags and the others are the values to the flags
   * The first value (`validator`) instructs Lodestar to run only the consensus client and without the validator client&#x20;
   * `--dataDir`: Specify the directory for Lodestar to store data related to the validator client
   * `--network`: Run the Validator Client service on the ETH Holesky testnet.
   * `--beacon-nodes`: URLs to connect to the main and backup consensus clients if any. This needs to be the same IP address set in your consensus client. Refer back [here](../../installing-and-configuring-your-el+cl-clients/set-up-and-configure-consensus-layer-client/) if you don't remember it.&#x20;
   * `--builder`: Required when using external builders to build blocks (e.g. MEV relays)
   * `--suggestedFeeRecipient`: ETH wallet address to receive rewards from block proposals and MEV bribes
   * `--doppelganger-detection`: Helps prevents slashing due to double signing by checking if your validator keys are already active on the network. _**Not a fool-proof solution.**_
   * `--metrics`: Enable metrics for monitoring
   * `--metrics.port`: Set the port for retrieving metrics
   * `--graffiti`: Optional text to display on-chain when your validator proposes a block
7. `environment`: Tells Node.js to allow a larger amount of memory to be used before it starts garbage collection processes to free up memory.
8. `ports:` Maps the ports used by the docker container to the ports of the host device so that they are reachable via the 127.0.0.1  or localhost name spaces.

### Start the Lodestar Validator Client container

1\) Make sure you are in the same folder as the `docker-compose.yml` file you created earlier.

```sh
cd ~/lodestar_validator
```

&#x20;2\) Start the docker container.

```sh
docker compose up -d
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

3\) Make sure there are no error messages by monitoring the logs for a few minutes.

```sh
docker logs lodestar_validator -f
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Remove duplicates of validator keystores

To prevent configuration mistakes leading to double signing in the future, remove duplicate copies of the validator signing keystores once everything is running smoothly.

```sh
sudo rm -r ~/validator_keys
```

## Resources

* Git repository: [https://github.com/ChainSafe/lodestar-quickstart.git](https://github.com/ChainSafe/lodestar-quickstart.git)
* Documentation: [https://chainsafe.github.io/lodestar/](https://chainsafe.github.io/lodestar/)
* Discord: [https://discord.gg/7Gdb4nFh](https://discord.gg/7Gdb4nFh)
