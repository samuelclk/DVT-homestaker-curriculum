# ETH Docker

## VM/Hardware Setup

You will need to prepare your virtual machine (VM) or home staking hardware for all options below. Step-by-step guide below.

{% content-ref url="../hardware-and-systems-setup/assemble-your-hardware/practicing-on-cloud-vms.md" %}
[practicing-on-cloud-vms.md](../hardware-and-systems-setup/assemble-your-hardware/practicing-on-cloud-vms.md)
{% endcontent-ref %}

{% content-ref url="../hardware-and-systems-setup/assemble-your-hardware/" %}
[assemble-your-hardware](../hardware-and-systems-setup/assemble-your-hardware/)
{% endcontent-ref %}

## Solo Staking testnet workflow

### Prepare your VM/Hardware

Create a new Google Cloud account to unlock $300 of free cloud credits.

Create a VM on the Google Cloud Console (or any other cloud provider) with the following machine specifications.

* **CPU:** 4 vCPU
* **RAM:** 16GB
* **Boot Disk:** Ubuntu 24.04 LTS, Balanced persistent disk, 350GB SSD,
* **Identity & API access:** No service account
* **Firewall:** Enable HTTP & HTTPS traffic

{% hint style="info" %}
Estimated cost per month on Google Cloud = $149, or **2&#x20;**_**months of free practice time**_ with $300 of cloud credits&#x20;
{% endhint %}

### Download & configure ETH Docker

**SSH into your VM/hardware:** Click on the dropdown beside the **"SSH"** column and select **"Open in browser window".** Click on **"Authorize"** when prompted.

<figure><img src="https://dvt-homestaker.stakesaurus.com/~gitbook/image?url=https%3A%2F%2F1628445806-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FoML8XLjdWBoYbtGBoQ9R%252Fuploads%252F67CExXgVAXX8He9QB978%252Fimage.png%3Falt%3Dmedia%26token%3D76052e50-b3d8-4ef4-b143-ac07a32f45fb&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=8d7c4d17&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Go to the [ETH Docker repository ](https://ethdocker.com/Usage/QuickStart)and to get and run the installation commands. Run the next 2 commands in sequence.

```sh
cd ~ && git clone https://github.com/eth-educators/eth-docker.git && cd eth-docker
```

```sh
sudo usermod -aG sudo $USER
```

Exit your virtual machine/hardware and re-login to add your host user into the sudo & docker user group.

```sh
exit
```

```sh
cd eth-docker
./ethd install
```

Enable ethd to be called from anywhere on your terminal.

```sh
source ~/.profile
```

Next, configure the ETH Docker service. **Tip:** You can now call ethd from anywhere in your VM.

```sh
ethd config
```

**Follow along the prompts in the terminal UI (TUI) to:**

1. Choose `Holešovice Testnet` >> `Ethereum node - consensus, execution and validator client`
2. Select the **consensus + validator client** and **execution client** and  of your choice
3. Use the `provided URL` for **Checkpoint Sync**, select `yes` for **MEV Boost**, `use default` **relay**, `yes` for **Grafana dashboards**
4. Set `Rewards Address` to an ERC-20 wallet address that you control (e.g., Metamask, hardware wallet)
5. `use default` **Graffiti**, `yes` for **generate validator keys**

#### ETH Docker TUI Navigation

1. `Arrow keys & Tab key`: Cycle options
2. `Space bar`: Select option
3. `Enter`: Confirm option
4. `CTRL+C`: Exit individual screen monitoring view

### Optional: Make EL and CL endpoints accessible on host

Edit the `.env` file in the `eth-docker` folder.

```sh
cd 
nano ~/eth-docker/.env
```

Append `:el-shared.yml` and `:cl-shared.yml` in the `COMPOSE_FILE` line.

**Example:**

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Now, the REST/HTTP/WS endpoints of your execution and consensus client can be accessed via:

* Execution Client (HTTP): http://127.0.0.1:8545
* Execution Client (WS): http://127.0.0.1:8546
* Consensus Client: http://127.0.0.1:5052

### Generate validator keys

```sh
ethd cmd run --rm deposit-cli-new --execution_address 0x4D496CcC28058B1D74B7a19541663E21154f9c84 --uid $(id -u)
```

{% hint style="danger" %}
Replace **--execution\_address** with your actual ERC-20 wallet address (e.g., Metamask, hardware wallet) on mainnet. The pre-filled execution address here enables the ETHStaker community to fund the validator key on our behalf.
{% endhint %}

**Follow the TUI prompts:**

* Choose language
* Confirm withdrawal (execution layer) address
* Number of validator keys to generate
* Set password to encrypt validator keys - No "\*\*\*\*" will be displayed so make sure to type your password carefully.
* Save the 24-word mnemonic securely

Your validator keys will be saved in the `~/eth-docker/.eth/validator_keys` folder.

### Start ETH Docker

<pre class="language-sh"><code class="lang-sh"><strong>ethd up
</strong></code></pre>

### Import validator keys

Import the generated validator keys onto your validator client

```sh
ethd keys import
```

### Whitelist your wallet address

{% hint style="info" %}
This section only applies to the Holeksy testnet
{% endhint %}

1. Join the discord server here - [https://discord.gg/ethstaker](https://discord.gg/ethstaker)
2. Join the #cheap-holesky-validator channel
3. Type “/cheap-holesky-deposit `<your ETH address>` ” in the text box and press enter
4.  Click on the link generated (ie. the [**Signer.is**](http://signer.is) text shown below)&#x20;

    <figure><img src="../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>
5. Connect your Metamask wallet and sign the message
6. Copy the URL and paste it in the Enter Signature box.

### Upload deposit data

Copy the deposit data generated by the command below and paste it in a plain text file on your laptop (e.g., Notepad, Textedit), then it save as `deposit_data_001.json`.

```sh
cat ~/eth-docker/.eth/validator_keys/deposit*json
```

**Example:**

{% hint style="success" %}
\[{"pubkey": "b72e61268081e28b583d78876cc1687d72be4c3592de1f9d585c96b4c64b25b49174f04ae6e55eb1e59247cb575c0157", "withdrawal\_credentials": "010000000000000000000000f0179dec45a37423ead4fad5fcb136197872ead9", "amount": 32000000000, "signature": "8bb6e8838d15ea0ea23ed5151436ea07b65a0530ccfa9f5154b1fa394827df5add81510cf3463b79a387b0dffbe43ae417ea53b844b43d6a249fbc153fa1deda9fc089f218a845e382aa7455d804650e0e03232d3dad36b180bbdacd908f286c", "deposit\_message\_root": "8dbda15641eb7be3573f7377d10634c93c2b1dceb9fb6e519ec54ae0286d04c7", "deposit\_data\_root": "43f215ce19df49591db322bd4966667afc2a226a0a9812e5d60e31c60223991c", "fork\_version": "01017000", "network\_name": "holesky", "deposit\_cli\_version": "2.7.0"}]
{% endhint %}

Go to the [Holesky Ethereum Staking Launchpad](https://holesky.launchpad.ethereum.org/en/upload-deposit-data) and select **"Become a Validator".**

Scroll down and skip through until you see this page. Then, upload your `deposit_data_001.json` file here and sign the transaction on your wallet.

<figure><img src="../.gitbook/assets/Screenshot 2024-09-02 at 4.53.57 PM.png" alt=""><figcaption></figcaption></figure>

### View logs

Monitor the logs of your validator node to make sure that it is syncing (or synced) with no errors while you wait for Lido to provision your validator deposit with 32 ETH.

View logs of each docker container.

```sh
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

### Useful commands

Within the `~/eth-docker` folder, run `./ethd help` to print all available command line options.&#x20;

```sh
ethd help
```

**Common options:**

* Update all clients & ETH Docker stack: `ethd update`
* Stop ETH Docker: `ethd down`
* Restart ETH Docker: `ethd restart`
* Restart from scratch: `ethd terminate`&#x20;
* Resync consensus client: `ethd resync-consensus`
* Resync execution client: `ethd resync-execution`

## Support

{% embed url="https://t.me/stakesaurus" %}

## Donations

#### If you found this helpful, consider supporting Stakesaurus in one of few ways [here](https://dvt-homestaker.stakesaurus.com/#if-you-found-this-helpful-consider-supporting-stakesaurus-in-one-of-two-ways-below)!
