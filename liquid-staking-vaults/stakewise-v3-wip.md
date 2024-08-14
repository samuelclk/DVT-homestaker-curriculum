# Stakewise V3 (WIP)

## &#x20;

### Vault Setup

WIP

### Validator Node Setup

Download Eth Docker for a quick and easy setup.

```sh
cd ~ && git clone https://github.com/eth-educators/eth-docker.git && cd eth-docker
```

Install dependencies.

```sh
./ethd install
```

`./ethd install`

Configure Eth Docker and set the `fee recipient address` to your `Stakwise vault fee recipient address`.

```sh
./ethd config
```

Exit and re-log in to your machine.

Start your Gnosis validator node.

```sh
./ethd up
```

View all your docker containers.

```sh
docker ps -a
```

View logs of each docker container.

```sh
docker logs <container_name> -f --tail 20
```

### Stakewise Operator Setup

Download the Stakewise Operator binary file & checksum [here](https://github.com/stakewise/v3-operator/releases)

```sh
curl -LO https://github.com/stakewise/v3-operator/releases/download/v2.0.5/operator-v2.0.5-linux-amd64.tar.gz
curl -LO https://github.com/stakewise/v3-operator/releases/download/v2.0.5/operator-v2.0.5-linux-amd64.sha256
```

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

Create a hot wallet for your vault to receive staking fees.

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
User=<user> #replace with your actual user
Group=<user> #replace with your actual user
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/operator start \
  --network=gnosis \
  --verbose \
  --vault=<your_vault_address> \ #replace with your actual vault address
  --max-fee-per-gas-gwei=40 \
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
```

View the logs.

```sh
sudo journalctl -fu stakewisOperator -o cat | ccze -A
```

#### Upload the deposit data generated onto the [Stakewise V3 operator UI](https://app.stakewise.io/operate).&#x20;
