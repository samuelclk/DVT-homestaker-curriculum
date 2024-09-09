---
description: Highly condensed version for now
---

# Stakewise V3

## Gnosis

### Overview

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Vault Setup

[https://app.stakewise.io/operate](https://app.stakewise.io/operate)

### Validator Node Setup

Download Eth Docker for a quick and easy setup.

```sh
cd ~ && git clone https://github.com/eth-educators/eth-docker.git && cd eth-docker
```

Install dependencies.

```sh
./ethd install
```

Exit and re-log in to your machine.

Configure Eth Docker and set the `fee recipient address` to your `Stakwise vault fee recipient address`.

```sh
cd eth-docker
./ethd config
```

Open your `.env` file.

<pre class="language-sh"><code class="lang-sh"><strong>nano .env #within the eth-docker folder
</strong></code></pre>

Append the following parameters into the compose line

```
COMPOSE_FILE=<other_flags>:el-shared.yml:cl-shared.yml
```

Start your Gnosis validator node.

```sh
./ethd up
```

View all your docker containers.

```sh
docker ps -a
```

View logs of each docker container (choose one).

```sh
ethd logs <container_name> -f
```

```
blackbox-exporter          consensus                  execution                  json-exporter              node-exporter              promtail
cadvisor                   ethereum-metrics-exporter  grafana                    loki                       prometheus                 validator
```

Configure firewall rules.

```sh
sudo ufw default allow outgoing
sudo ufw default deny incoming
sudo ufw allow 22/tcp
sudo ufw allow 9000
sudo ufw allow 30303
sudo ufw enable
```

### Stakewise Operator Setup

Download the Stakewise Operator binary file & checksum [here](https://github.com/stakewise/v3-operator/releases)

<pre class="language-sh"><code class="lang-sh"><strong>cd
</strong><strong>curl -LO https://github.com/stakewise/v3-operator/releases/download/v2.0.5/operator-v2.0.5-linux-amd64.tar.gz
</strong>curl -LO https://github.com/stakewise/v3-operator/releases/download/v2.0.5/operator-v2.0.5-linux-amd64.sha256
</code></pre>

Print the checksum.

```sh
cat operator-v2.0.5-linux-amd64.sha256
```

Run the checksum verification process.

```sh
echo "<checksum> operator-v2.0.5-linux-amd64.tar.gz" | sha256sum --check
```

Extract the `operator` file and move it into `/usr/local/bin`

```sh
tar xvf operator-v2.0.5-linux-amd64.tar.gz
sudo cp operator-v2.0.5-linux-amd64/operator /usr/local/bin
```

Initiatlise the `operator` service.

```sh
/usr/local/bin/./operator init
```

Create the validator keys.

<pre class="language-sh"><code class="lang-sh"><strong>/usr/local/bin/./operator create-keys
</strong></code></pre>

Create a hot wallet for your vault to pay for gas when activating new validator keys.

<pre class="language-sh"><code class="lang-sh"><strong>/usr/local/bin/./operator create-wallet
</strong></code></pre>

{% hint style="info" %}
Top up your hot wallet with some `XDAI` so that new validator keys can be activated once there is sufficient GNO staked in your vault
{% endhint %}

#### Start your Stakewise Operator service

Create a systemd configuration file.

```sh
sudo nano /etc/systemd/system/stakewiseOperator.service
```

Enter the following contents.&#x20;

```
[Unit]
Description=StakewiseOperator
After=network.target

[Service]
User=<user>
Group=<user>
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/operator start \
  --network=gnosis \
  --verbose \
  --vault=<your_vault_address> \
  --max-fee-per-gas-gwei=30 \
  --consensus-endpoints=http://127.0.0.1:5052 \
  --execution-endpoints=http://127.0.0.1:8545

[Install]
WantedBy=multi-user.target
```

**Notes:**

* Replace `<user>` with your machine's actual user. This can be found in your terminal before the `@` symbol
* Replace `<your_vault_address>`with your actual vault address

Start the `operator` service.

```sh
sudo systemctl daemon-reload
sudo systemctl start stakewiseOperator
sudo systemctl status stakewiseOperator
sudo systemctl enable stakewiseOperator
```

View the logs.

<pre class="language-sh"><code class="lang-sh"><strong>sudo apt install ccze
</strong><strong>sudo journalctl -fu stakewiseOperator -o cat | ccze -A
</strong></code></pre>

Move keystores into `ethdocker/.eth/validator_keystores` folder.

<pre class="language-sh"><code class="lang-sh"><strong>sudo mv ~/.stakewise/&#x3C;vault_address>/keystores/* ~/eth-docker/.eth/validator_keys 
</strong></code></pre>

Print the keystores password.

```sh
cat ~/eth-docker/.eth/validator_keys/password.txt 
```

Run the keys import process with `eth-docker` and enter the keystores password when prompted.

```sh
ethd keys import
```

#### Upload the deposit data generated onto the [Stakewise V3 operator UI](https://app.stakewise.io/operate).&#x20;

Print the `deposit-data.json` file.

```sh
cat ~/.stakewise/<vault_address>/deposit_data.json
```

Copy the `deposit-data` file contents and save it as a `.json` file on your working device. Then, upload the file.

<figure><img src="../.gitbook/assets/image (195).png" alt=""><figcaption></figcaption></figure>

### Get support

<figure><img src="../.gitbook/assets/image (196).png" alt=""><figcaption></figcaption></figure>

### Support Stakesaurus

If you found this guide helpful, consider staking some GNO in my Stakewise Vault below!

{% embed url="https://app.stakewise.io/vault/gnosis/0x3cb4692177525db38d983da0445d4eb25c3826de" %}
