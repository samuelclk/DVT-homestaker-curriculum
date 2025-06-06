# CSM with ETH Docker

## VM/Hardware Setup

You will need to prepare your virtual machine (VM) or home staking hardware for all options below. Step-by-step guide below.

{% content-ref url="../../../hardware-and-systems-setup/practicing-for-free-on-cloud-vms/google-cloud.md" %}
[google-cloud.md](../../../hardware-and-systems-setup/practicing-for-free-on-cloud-vms/google-cloud.md)
{% endcontent-ref %}

{% content-ref url="../../../hardware-and-systems-setup/assemble-your-hardware.md" %}
[assemble-your-hardware.md](../../../hardware-and-systems-setup/assemble-your-hardware.md)
{% endcontent-ref %}

## Lido CSM testnet workflow

### Video guide

{% embed url="https://www.youtube.com/watch?v=PQ5qLfbBeTI" %}

### Prepare your VM/Hardware

Create a new Google Cloud account to unlock $300 of free cloud credits.

Create a VM on the Google Cloud Console (or any other cloud provider) with the following machine specifications.

* **CPU:** 2 vCPU
* **RAM:** 8GB
* **Boot Disk:** Ubuntu 24.04 LTS, Balanced persistent disk, 350GB SSD,
* **Identity & API access:** No service account
* **Firewall:** Enable HTTP & HTTPS traffic

{% hint style="info" %}
Estimated cost per month on Google Cloud = $84, or _**3.5 months of free practice time**_ with $300 of cloud credits&#x20;
{% endhint %}

### Download & configure ETH Docker

**SSH into your VM/hardware:** Click on the dropdown beside the **"SSH"** column and select **"Open in browser window".** Click on **"Authorize"** when prompted.

<figure><img src="../../../.gitbook/assets/Screenshot 2024-09-03 at 1.50.18 PM.png" alt=""><figcaption></figcaption></figure>

Go to [Lido's ETH Docker repository ](https://github.com/lidofinance/eth-docker)and to get and run the installation commands. Run the next 2 commands in sequence.

```sh
cd ~ && git clone https://github.com/eth-educators/eth-docker.git && cd eth-docker
```

```sh
sudo usermod -aG sudo $USER
```

Exit your virtual machine/hardware and re-login to add your host user into the docker user group.

```
exit
```

After logging in again, install ETH Docker.

```sh
cd eth-docker
./ethd install
```

After the installation is complete, run:

```sh
source ~/.profile
```

You will now be able to call `ethd` from anywhere in your terminal. Next, configure the ETH Docker service.

```sh
ethd config
```

**Follow along the prompts in the terminal UI (TUI) to:**

1. Choose `Hoodi Testnet` >> `Lido-compatible node (Community Staking / Simple DVT)` >> `[Community Staking] CSM node`
2. Select the **Nimbus** (Consensus) and **Nethermind** (Execution) for the client choices
3. Use the default **Checkpoint Sync URL**, select `yes` for **MEV Boost**, `select all` **relays**, `yes` for **Grafana dashboards**, `default` **Graffiti**, `yes` for **generate validator keys**
4. Generate suitable validator keys to participate in the Lido CSM
   * Generate `1` validator key and set the encryption password for the key
   * Save your 24-word mnemonic
   * Verify the fee recipient and withdrawal address on the [CSM Operator Portal](https://operatorportal.lido.fi/modules/community-staking-module)

{% tabs %}
{% tab title="Withdrawal Address" %}
* **Mainnet:** [`0xB9D7934878B5FB9610B3fE8A5e441e8fad7E293f`](https://etherscan.io/address/0xb9d7934878b5fb9610b3fe8a5e441e8fad7e293f)&#x20;
* **Hoodi:** [`0x4473dCDDbf77679A643BdB654dbd86D67F8d32f2`](https://hoodi.cloud.blockscout.com/address/0x4473dCDDbf77679A643BdB654dbd86D67F8d32f2)
{% endtab %}

{% tab title="Fee Recipient Address" %}
* **Mainnet:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)&#x20;
* **Hoodi:** [`0x9b108015fe433F173696Af3Aa0CF7CDb3E104258`](https://hoodi.cloud.blockscout.com/address/0x9b108015fe433F173696Af3Aa0CF7CDb3E104258)
{% endtab %}
{% endtabs %}

#### ETH Docker TUI Navigation

1. `Arrow keys & Tab key`: Cycle options
2. `Space bar`: Select option
3. `Enter`: Confirm option
4. `CTRL+C`: Exit individual screen monitoring view

### Start ETH Docker

```
ethd start
```

### Import validator keys

{% hint style="info" %}
You will need to wait around 5 minutes from starting the ETH Docker stack before importing keys without errors.&#x20;
{% endhint %}

Change the user permissions of the folder containing your validator keys. Replace \<user> with your actual username of your VM.

```sh
sudo chown -R $USER:$USER ~/eth-docker/.eth/validator_keys
```

**Tip:** To find username, look to the terminal of your sever/node. Every character before the `@` is your username

Next, import the generated validator keys onto your validator client

```
ethd keys import
```

### Upload deposit data

Copy the deposit data generated by the command below for uploading onto the Lido CSM Widget.

* **Mainnet:** [https://csm.lido.fi/](https://csm.lido.fi/)
* **Hoodi:** [https://csm.testnet.fi/](https://csm.testnet.fi/)

```
cat ~/eth-docker/.eth/validator_keys/deposit*json
```

Example:

{% hint style="success" %}
\[{"pubkey": "b72e61268081e28b583d78876cc1687d72be4c3592de1f9d585c96b4c64b25b49174f04ae6e55eb1e59247cb575c0157", "withdrawal\_credentials": "010000000000000000000000f0179dec45a37423ead4fad5fcb136197872ead9", "amount": 32000000000, "signature": "8bb6e8838d15ea0ea23ed5151436ea07b65a0530ccfa9f5154b1fa394827df5add81510cf3463b79a387b0dffbe43ae417ea53b844b43d6a249fbc153fa1deda9fc089f218a845e382aa7455d804650e0e03232d3dad36b180bbdacd908f286c", "deposit\_message\_root": "8dbda15641eb7be3573f7377d10634c93c2b1dceb9fb6e519ec54ae0286d04c7", "deposit\_data\_root": "43f215ce19df49591db322bd4966667afc2a226a0a9812e5d60e31c60223991c", "fork\_version": "01017000", "network\_name": "hoodi", "deposit\_cli\_version": "2.7.0"}]
{% endhint %}

{% content-ref url="../upload-remove-view-validator-keys.md" %}
[upload-remove-view-validator-keys.md](../upload-remove-view-validator-keys.md)
{% endcontent-ref %}

### View logs

Monitor the logs of your validator node to make sure that it is syncing (or synced) with no errors while you wait for Lido to provision your validator deposit with 32 ETH.

View logs of each docker container.

```
ethd logs <container_name> -f --tail 20
```

**Flags:**

* `-f`: Follow along the logs in real time. `CTRL+C` to exit monitoring view
* `--tail`: Print the last N lines of the logs.

Choose one to replace the `<container_name>` above.

```
blackbox-exporter          consensus                  execution                  json-exporter              node-exporter              promtail
cadvisor                   ethereum-metrics-exporter  grafana                    loki                       prometheus                 validator
```

**Grafana Dashboards:**

Bring down all ETH Docker containers.

```
ethd down
```

Edit the `.env` file of ETH Docker.

```
nano ~/eth-docker/.env
```

Scroll down to the **`GRAFANA_PORT=3000`** line and change the number to **`443`**.

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Bring ETH Docker back up.

```
ethd up
```

To access your Grafana dashboard, navigate to `PUBLIC_IP:443` on your web browser.&#x20;

### Useful commands

Run `ethd help` to print all available command line options. Common options:

* Update all clients & ETH Docker stack: `ethd update`
* Stop ETH Docker: `ethd down`
* Restart ETH Docker: `ethd restart`
* Restart from scratch: `ethd terminate`&#x20;

## Mainnet workflow

ETH Docker has an inbuilt TUI workflow for Mainnet setups. Simply follow along but **verify the withdrawal and fee recipient addresses.**

* **Mainnet withdrawal\_address:** [`0xB9D7934878B5FB9610B3fE8A5e441e8fad7E293f`](https://etherscan.io/address/0xb9d7934878b5fb9610b3fe8a5e441e8fad7e293f)
* **Mainnet fee recipient address:** [`0x388C818CA8B9251b393131C08a736A67ccB19297`](https://etherscan.io/address/0x388C818CA8B9251b393131C08a736A67ccB19297)

## Support

{% embed url="https://t.me/stakesaurus" %}
