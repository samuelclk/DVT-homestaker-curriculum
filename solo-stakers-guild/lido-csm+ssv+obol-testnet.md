# Lido CSM+SSV+Obol (Testnet)

## Overall Workflow

1. **SSV:** Set up `SSV node` + `SSV DKG` services & `Execution + Consensus Clients` using **Eth Docker**
2. **Obol:** Set up `Obol Charon` service & `Validator Client` using **Eth Docker** to import Obol-compatible validator keyshares
3. **Lido CSM:** Set up a second and `Dedicated Validator Client` using **EthPillar** to import Lido CSM-compatible validator keys

## Hardware Setup (Testnet)

Spin up a virtual machine on a cloud service with the following specifications using the reference page below.

* CPU: 4 cores
* RAM: 8GB
* SSD: 250GB
* OS: Ubuntu 24.04

**Example:**

{% content-ref url="../hardware-and-systems-setup/practicing-for-free-on-cloud-vms/google-cloud.md" %}
[google-cloud.md](../hardware-and-systems-setup/practicing-for-free-on-cloud-vms/google-cloud.md)
{% endcontent-ref %}

{% hint style="success" %}
**Tip:** Open these reference pages in a new tab/window so that you can switch between them easily
{% endhint %}

## Installing ETH Docker

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

## SSV Setup

```
ethd config
```

**Follow along the prompts in the terminal UI (TUI) to:**

* Choose `Hoodi Testnet` >> `SSV node - consensus, execution and ssv-node`
* Select `yes` for _**Do you want to participate in DKG ceremonies as an operator?**_
* Once you see the screen below, select `<Cancel>` as we don't have our Operator ID yet.&#x20;

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

* _<mark style="background-color:yellow;">**Then copy your SSV node public key from your terminal output and save it on a text editor**</mark>_

<figure><img src="../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

<details>

<summary>ETH Docker TUI Navigation</summary>

* `Arrow keys & Tab key`: Cycle options

- `Space bar`: Select option

* `Enter`: Confirm option

- `CTRL+C`: Exit individual screen monitoring view

* `ESC`: Quit

</details>

### Register SSV Operator

1. Go to the [SSV webapp](https://app.ssv.network/join), connect your wallet, and set the network to Hoodi.
2. Select `Join as Operator` >> `Register Operator`
3. Paste your SSV node public key into the `Operator Public Key` field. Make sure there are no whitespaces in your pasted string.
4. Keep `Operator Status` to _**Public**_
5. Set the annual fee to **1.5 SSV** per validator key, representing \~1.5% staking rewards fee at current $ETH and $SSV prices ($2650 & $23).
6. Register operator and sign the transaction on your wallet
7. Your SSV Operator ID will then be generated. _<mark style="background-color:yellow;">**Copy it and save it in a text editor file.**</mark>_

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Setting your Operator Status to Public allows other stakers to select your SSV node as one of their DV operators, allowing them to pay you for your service. You can also easily net off the fees among your own DVT cluster members if you wish.&#x20;
{% endhint %}

### Complete ETH Docker Setup

Go back to the terminal of your VM.

```
ethd config
```

1. Choose `Hoodi Testnet` >> `SSV node - consensus, execution and ssv-node`
2. Select `yes` for _**Do you want to participate in DKG ceremonies as an operator?**_
3. Because you now have your `SSV Operator ID`, you can paste it in the requested field
4. Select the **consensus** and **execution client** of your choice
5. Use the `provided URL` for **Checkpoint Sync**, select `yes` for **MEV Boost**, `yes` for **Grafana dashboards**
6. Set `Rewards Address` to an ERC-20 wallet address that you control (e.g., Metamask, hardware wallet)
7. `use default` **Graffiti**, `yes` for **generate validator keys**

### Start ETH Docker

```sh
cd
ethd up
```

### Configure DKG endpoint

Find the external IP address of your VM on your Cloud account >> Console >> Compute Engine >> Look under "External IP".

Your **DKG endpoint** will be `<EXTERNAL_IP_ADDRESS>:3030`,without the pointy brackets. _<mark style="background-color:yellow;">**Note that down and save it in a text editor file.**</mark>_

Verify that your DKG endpoint is accessible from external sources.

```sh
cd ~/eth-docker
sudo docker compose run --rm ssv-dkg ping --ip https://<External_IP>:3030
```

**Expected output:**

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

### View logs

```
ethd logs ssv-node -f --tail 20
```

```
ethd logs consensus -f --tail 20
```

```
ethd logs execution -f --tail 20
```

### Complete SSV Operator Metadata

<details>

<summary>Complete SSV Operator Metadata</summary>

Go back to the [SSV webapp](https://app.ssv.network/join) >> Connect your wallet >> Switch to Holesky network >> go to `My Account` and click on your SSV Operator.

<img src="../.gitbook/assets/image (7).png" alt="" data-size="original">

Select the `...` drop down at the top right >> `Edit Details`

<img src="../.gitbook/assets/image (9).png" alt="" data-size="original">

Select all options under MEV Relays.

<img src="../.gitbook/assets/image (13).png" alt="" data-size="original">

The MEV Relays are actually set in your ETH Docker config and this step is just to signal the relays that you are using.

{% hint style="danger" %}
All cluster members _**must use**_ the same relays to avoid missing block proposals due to a lack of consensus!
{% endhint %}

{% hint style="info" %}
Use all available MEV Relays so that it's easier for stakers to choose your SSV Node.&#x20;
{% endhint %}

Input your DKG endpoint and append `:443` at the end if you are using a Tailscale funnel.

<img src="../.gitbook/assets/image (12).png" alt="" data-size="original">

{% hint style="info" %}
The other fields are optional but fill them up to attract stakers to select your SSV Operator!
{% endhint %}

</details>

## Obol Setup

Go back to your Home folder.

```sh
cd
```

### Cluster Creation

1. Go to the [Obol Hoodi Launchpad](https://hoodi.launchpad.obol.org/), scroll down and select **"Create a distributed validator alone"**
2. Read and click through the **Advisories**
3. Input your cluster details as follows
   1. Cluster Name: Any
   2. Cluster Size: 4
   3. Validators: 1
   4. Withdrawal & Fee Recipient Address: Your own wallet address

<details>

<summary>Example screenshot</summary>

<img src="../.gitbook/assets/Screenshot 2025-03-29 at 4.02.26 PM.png" alt="" data-size="original">

</details>

4. Create your cluster by signing an onchain transaction on your wallet
5. Copy the resulting **"Create Cluster"** command generated on the Obol Launchpad and run it on your terminal.

<details>

<summary>Example command</summary>

{% hint style="success" %}
docker run -u $(id -u):$(id -g) --rm -v "$(pwd)/:/opt/charon" obolnetwork/charon:v1.3.0-rc3 create cluster --definition-file="https://api.obol.tech/v1/definition/0xf5960ceabdb3b528e85e2e174c997e86fe48b546d2c4abbc6dfd7d00595cc72c" --network=hoodi --cluster-dir="cluster" --publish
{% endhint %}

</details>

<details>

<summary>The following folders and <code>files</code> will be created:</summary>

* cluster
  * node0
    * `charon-enr-private-key` `cluster-lock.json` `deposit-data.json` validator\_keys
      * `keystore-0.json` `keystore-0.txt`
  * node1
    * &#x20;`charon-enr-private-key` `cluster-lock.json` `deposit-data.json` validator\_keys
      * `keystore-0.json` `keystore-0.txt`
  * node2
    * `charon-enr-private-key` `cluster-lock.json` `deposit-data.json` validator\_keys
      * `keystore-0.json` `keystore-0.txt`
  * node3
    * `charon-enr-private-key` `cluster-lock.json` `deposit-data.json` validator\_keys
      * `keystore-0.json` `keystore-0.txt`

</details>

### Preparing your cluster

Set the necessary permissions to your newly generated Obol ENR private key and cluster file.

```sh
sudo chmod 644 ~/cluster/node0/charon-enr-private-key
sudo chmod 644 ~/cluster/node0/cluster-lock.json
```

Copy the following files from one of the cluster folders (e.g., `node0`) above into the `~/eth-docker/.eth` folder and set the necessary permissions.

```sh
sudo cp ~/cluster/node0/validator_keys/* ~/eth-docker/.eth/validator_keys
sudo cp ~/cluster/node0/* ~/eth-docker/.eth
sudo chown -R $USER:$USER ~/eth-docker/.eth
```

Edit the `.env` file of Eth Docker.

```sh
nano ~/eth-docker/.env
```

In the `COMPOSE_FILE` line:

* Append `:lido-obol.yml` and `:cl-shared.yml`
* Edit the `"consensus"-cl-only.yml` file to `"consensus".yml`. e.g., From `nimbus-cl-only.yml` to `nimbus.yml`

**Example:**

<figure><img src="../.gitbook/assets/image (220).png" alt=""><figcaption></figcaption></figure>

Press `CTRL+W`, type **"CL\_NODE"** and hit `ENTER`.&#x20;

* Change the `CL_NODE` line to **http://charon:3600** (from http://consensus:5052)

**Example:**

<figure><img src="../.gitbook/assets/image (200).png" alt=""><figcaption></figcaption></figure>

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

### Restart ETH Docker

```sh
ethd down && ethd up
```

Print the generated password of your Obol validator key shard and copy the output to your clipboard.

```sh
cat ~/eth-docker/.eth/validator_keys/keystore*.txt && echo
```

After all your services running via Docker "warmed up" for \~5 minutes, import your validator key shard and paste the password when prompted.

```sh
ethd keys import
```

### Monitoring Charon <a href="#monitoring-charon" id="monitoring-charon"></a>

Print the logs of the Obol Charon & Validator Client.

```sh
ethd logs charon -f --tail 20
```

```sh
ethd logs validator -f --tail 20
```

## Lido CSM Setup

Go to the [Coincashew website](https://docs.coincashew.com/ethpillar) and copy the latest 1-line installation command and paste it into your terminal.

```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/coincashew/EthPillar/main/install.sh)"
```

Then, type + enter `ethpillar` and follow along the prompts in the terminal UI (TUI) to:

1. Select the `Nimbus-Nethermind` option
2. Wait for the installation to be done and then select `2 - Hoodi` for your network (Press "`2`" & `ENTER`)
3. Select the `4 - Lido CSM Validator Client only` option
4. Enter `http://127.0.0.1:5052` as your Consensus Client endpoint
5. Generate validator keys to participate in the Lido CSM
   * **Do not** choose to disable internet connection when prompted
   * Select Hoodi
   * Enter the Lido's CSM Withdrawal Vault address as the Withdrawal Address: `0x4473dCDDbf77679A643BdB654dbd86D67F8d32f2`
   * Set the password for your validator keys
   * Save the 24-word mnemonic securely
6. Import the generated validator keys onto your validator client

{% hint style="warning" %}
Verify the fee recipient and withdrawal address on the [CSM Operator Portal](https://operatorportal.lido.fi/modules/community-staking-module)
{% endhint %}

Copy the deposit data generated by the command below for uploading onto the Lido CSM Widget.

{% hint style="success" %}
The links below include the referral IDs of the tools that we are using today to join Lido CSM. **Your rewards** **will not be affected** by using these.
{% endhint %}

* **Mainnet:** [https://csm.lido.fi/?ref=ethpillar](https://csm.lido.fi/?ref=ethpillar)
* **Hoodi:** [https://csm.testnet.fi/?ref=ethpillar](https://csm.testnet.fi/?ref=ethpillar)

```sh
cat $(find ~/ethstaker_deposit-cli -name "deposit*.json" 2>/dev/null) && echo
```

**Example output:**

{% hint style="success" %}
\[{"pubkey": "8b29b853aef47eb3da93287a83b4625b418bf5a785bb506086e9f315478170cdb452fb63f32f8134835fc4ebea3313a7", "withdrawal\_credentials": "0100000000000000000000004473dcddbf77679a643bdb654dbd86d67f8d32f2", "amount": 32000000000, "signature": "abcff77128a76dff62529f2a081f2143f77404c29087da1f4e12a8c6e8506dfb079085fdf72da8903e9c8c63ddf04129143090e92b6a17c993919e62047880cf54160f350271dff1b70e616e4210a5521836334d1139ece549ba513d503d0d90", "deposit\_message\_root": "03d0eb169a3c1a5551484185163b3e409dde01ec00515b7e594a6430ff398f69", "deposit\_data\_root": "961113829f6537828c5f06e69df8b76cf2a6904de03c4d1a25939ab1da9b5a14", "fork\_version": "10000910", "network\_name": "hoodi", "deposit\_cli\_version": "11.1.0"}]
{% endhint %}

### View logs

Run the `ethpillar` command and select the `view logs` option

<details>

<summary>ETHPillar TUI Navigation</summary>

1. `Arrow keys & Tab key`: Cycle options
2. `Space bar`: Select option
3. `Enter`: Confirm option
4. `CTRL+B`, then `D`: Exit split-screen monitoring view
5. `CTRL+C`: Exit individual screen monitoring view
6. `exit` command (type "exit" and `enter` in terminal) : Exit current terminal

</details>

## Exiting validator keys

### Lido CSM

Find the file path of your validator keystores.

```
cat $(find /var/lib -name "keystore*.json" 2>/dev/null)
```

**Copy the output file path.**

Run `ethpillar` and navigate to `validator client` >> `exit keys` and input the file path of your validator keystore.

Enter the password set for your validator keystore when prompted.

{% hint style="info" %}
You can only exit your validator keystores after they have been activated on the Ethereum beacon chain.
{% endhint %}

## Securing your device

### Firewall Rules

```sh
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp # for SSH
sudo ufw allow 30303 # for the EL
sudo ufw allow 9000 # for the CL
sudo ufw allow 3000 # for the native Grafana
sudo ufw allow 3030 # for SSV DKG
sudo ufw allow 12001/udp # for SSV node UDP
sudo ufw allow 13001/tcp # for SSV node TCP
sudo ufw allow 3610/tcp # for Obol Charon TCP
sudo ufw enable
```

Make sure to also configure port forwarding on the ports allowed above.&#x20;

{% content-ref url="../tips/advanced-networking.md" %}
[advanced-networking.md](../tips/advanced-networking.md)
{% endcontent-ref %}

### Other Security SOPs

{% content-ref url="../linux-os-networking-and-security/networking-and-network-security.md" %}
[networking-and-network-security.md](../linux-os-networking-and-security/networking-and-network-security.md)
{% endcontent-ref %}

{% content-ref url="../linux-os-networking-and-security/device-level-security-setup.md" %}
[device-level-security-setup.md](../linux-os-networking-and-security/device-level-security-setup.md)
{% endcontent-ref %}

## Support

{% embed url="https://t.me/stakesaurus" %}
