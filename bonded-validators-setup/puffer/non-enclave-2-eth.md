# Non-Enclave: 2 ETH

## Installing dependencies

Rust

```sh
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Cargo

```sh
sudo apt install cargo
```

OpenSSL & pkg-config

```sh
sudo apt-get install libssl-dev pkg-config
```

Set the `PKG_CONFIG_PATH` Environment Variable

```sh
export PKG_CONFIG_PATH=/usr/lib/x86_64-linux-gnu/pkgconfig:$PKG_CONFIG_PATH
```

Exit your VM and re-login.

```sh
exit
```

## Installing Puffer

Download the latest Coral repository and enter into this directory.

```sh
git clone https://github.com/PufferFinance/coral
cd coral
```

Build the Coral image ("install").

```sh
cargo build --release
```

## Generate validator keys and Puffer registration file

First, make sure you are in the `coral` folder.

```
cd ~/coral
```

Create a password file to encrypt your validator keys.

```
mkdir output
nano output/password.txt
```

Enter your password in the plain text file and use `CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

### Retrieve latest Module name

Visit [puffer.fi](https://www.puffer.fi/) and select "Become a validator".

Connect your wallet. Disable the "Enclave" option. Then, copy + paste the command on a text editor and set the`--password-file` and `--output-file` to `output/password.txt` and `output/registration_001.json`.

<figure><img src="../../.gitbook/assets/Screenshot 2024-08-28 at 11.53.24 PM (1).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Validator enclaves using Intel SGX is not live on mainnet yet and the current version on testnet will be deprecated in favour of the upcoming V2 SGX.
{% endhint %}

**Example Command:**

```
cargo run --bin coral-cli validator keygen --guardian-threshold 1 --module-name <0xXXX...> --withdrawal-credentials <0x0100...> --guardian-pubkeys 0x04097a98928ed79c443d03714d4073baecf21928102c7f8d1b34420d358c9b625da61d237034919de8c690cf4992e098311a449e41e655ee0b270486b7bc613fa2 --fork-version 0x01017000 --password-file output/password.txt --output-file output/registration_docker_001.json
```

Follow the prompts to select the number of validator keys to generate.

If successful, you should see the following files in your `~/coral/output`  and `~/coral/etc/keys/bls_keys` folders.

```sh
ls ~/coral/output
```

**Expected output:**

```
password.txt    registration_docker_001.json
```

```sh
ls ~/coral/etc/keys/bls_keys
```

**Expected output:**

_\*Example only. You should see a file with it's validator public key as its file name._

```
800000b3884235f70b06fec68c19642fc9e81e34fbe7f1c0ae156b8b45860dfe5ac71037ae561c2a759ba83401488e18
```

## Transfer Puffer registration file

Open up your terminal/powershell on your Mac/Windows laptop and run:

```
ssh-keygen -t ed25519 -C <google_cloud_username>
```

Your `<google_cloud_username>` can be found in the email you used to sign up for Google Cloud. e.g., if `sam@gmail.com` is the email used, then `sam` is your username.&#x20;

Print the ssh public key.

```
cat ~/.ssh/id_ed25519.pub
```

Copy the output, click into your Google Cloud instance>>`EDIT`, scroll down to `SSH Keys`>>`ADD ITEM`, and paste it here. Then `Save`.

Open up the terminal app on your laptop and run the secure copy command to retrieve your Puffer registration file. We will upload this file onto the Puffer launchpad in the next step to register your validator.

```sh
cd ~
scp -i .ssh/id_ed25519 <username>@<IP_address>:~/coral/output/registration_docker_001.json Downloads/registration_docker_001.json
```

**Tip:** Replace `<username>` & `<IP_address>` with the actual username & IP address of your server/node.

* To find username, look to the terminal of your sever/node. Every character before the `@` is your username
* To find IP address of a virtual machine (VM), go to your cloud service console, click into your VM and find the "Public IP"&#x20;
* To find IP address of a self-hosted machine, SSH into the machine and run `ip a`on the terminal. The IP address will take the form of `192.168.xx.xx`

{% hint style="info" %}
Do not use any other method to transfer the registration.json file onto your laptop as the data might be corrupted during transfer. Uploading invalid registration files will result in penalties which will be deducted from your Validator Tickets.
{% endhint %}

## Mint pufETH and Validator Tickets (VTs)

Purchase 2 ETH worth of pufETH and at least 28 days worth of validator tickets.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* Non-enclave users must deposit 2 ETH worth of pufETH to register
* A minimum of 28 Validator Tickets (VTs) are required to be deposited when registering a validator. The Guardians will exit validators if their VTs expire after 28 days without being refilled.

## Validator Registration

Upload the registration.json file that you retrieved from your server/node onto your laptop here.&#x20;

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

You will then be prompted to sign 3 transactions on your wallet.

* Sign a `Permit` message to deposit your VTs to the PufferProtocol contract
* Sign a `Permit` message to deposit your pufETH bond to the PufferProtocol contract
* Sign the final transaction to register your validator

### Await deposit by Puffer

The Guardians will provision pending validators when there is 32 ETH of liquidity in the PufferVault.

Invalid registrations will be skipped by the Guardians. Your bond will be returned but your VTs are penalized to prevent griefing.

## Import validator keys

Now that your validator is registered with Puffer, you will need to import the validator key into your validator client.

### ETH Docker users

Follow the steps in the page below until before the **"Import validator keys"** section.&#x20;

{% content-ref url="../../automation-tools/eth-docker.md" %}
[eth-docker.md](../../automation-tools/eth-docker.md)
{% endcontent-ref %}

Move your validator key(s) into the `~/eth-docker/.eth/validator_keys` folder.

```sh
cd ~/coral/etc/keys/bls_keys
sudo cp * ~/eth-docker/.eth/validator_keys
```

Import validator keys into your validator client

<pre class="language-sh"><code class="lang-sh"><strong>ethd keys import
</strong></code></pre>

Alternatively,

```sh
~/eth-docker/./ethd keys import
```

{% hint style="info" %}
If you have not configured your validator node go to the eth-docker page to complete the setup.
{% endhint %}

#### Monitor logs

Monitor the logs of your validator node to make sure that it is syncing (or synced) with no errors while you wait for Puffer to provision your validator deposit with 32 ETH.

View logs of each docker container.

```sh
ethd logs <container_name> -f
```

Choose one to replace the `<container_name>` above.

```
blackbox-exporter          consensus                  execution                  json-exporter              node-exporter              promtail
cadvisor                   ethereum-metrics-exporter  grafana                    loki                       prometheus                 validator
```

### Systemd users (WIP)

## Support

{% embed url="https://t.me/stakesaurus" %}

## Donations

#### If you found this helpful, consider supporting Stakesaurus in one of few ways [here](https://dvt-homestaker.stakesaurus.com/#if-you-found-this-helpful-consider-supporting-stakesaurus-in-one-of-two-ways-below)!
