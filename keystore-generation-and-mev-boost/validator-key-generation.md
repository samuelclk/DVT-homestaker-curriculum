# Validator key generation

## Important

* It is highly recommended that you perform this step using an air-gapped machine - i.e. a device that has never connected to the public internet before. We will describe a few methods below.
* If this is not available, turn off all internet and wireless connection (e.g. Ethernet, WiFi, Bluetooth) before proceeding with the key generation step
* In both cases above, make sure you are in a safe environment (e.g. home or office) with a trusted WiFi network for building the validator key generation tool from source. Make sure to also physically block all camera devices - e.g. laptop cameras, Webcams, people standing behind you during this process

## Creating an air-gapped machine

1. The least technical way is to buy a cheap single board computer like the Raspberry Pi from official distributors for less than S$100 SGD
2. **"OS-on-a-stick":** For more technical workarounds, we can flash a new USB drive with either Ubuntu or TailsOS and run a completely fresh OS from this USB drive itself. This system will be completely isolated from your host device (e.g. working laptop) and the described method below will not store any files after you remove the USB drive

#### _**We will cover Method 2 in this guide.**_&#x20;

## Flash and install OS

1\) Download latest Ubuntu OS [here](https://ubuntu.com/download) or TailsOS [here](https://tails.net/install/download/) and follow the respective instructions to verify the checksums of the downloaded file.

2\) Download an ISO flasher (e.g. [BalenaEtcher](https://etcher.balena.io/)) and flash your USB drive with your preferred OS. Refer to the previous section for steps (1) and (2) if needed.

{% content-ref url="../linux-os-networking-and-security/install-and-prepare-the-os.md" %}
[install-and-prepare-the-os.md](../linux-os-networking-and-security/install-and-prepare-the-os.md)
{% endcontent-ref %}

3\) Once your USB drive is flashed with your preferred OS, plug it into your working device and reboot the device to go into the boot menu. Depending on your system, you might need to hold `F2`, `F10`, `F12`, or `ESC` during the rebooting process to bring up the boot menu.

<details>

<summary>For an extensive list of laptop models, refer to the links below:</summary>

1\) Non-Apple/Mac models:

[https://techofide.com/blogs/boot-menu-option-keys-for-all-computers-and-laptops-updated-list-2021-techofide/#:\~:text=The%20keys%20that%20are%20generally,of%20the%20computers%20or%20motherboards.](https://techofide.com/blogs/boot-menu-option-keys-for-all-computers-and-laptops-updated-list-2021-techofide/)

2\) Apple/Mac

[https://support.apple.com/en-sg/102603](https://support.apple.com/en-sg/102603)

</details>

4\) Once you see the boot menu, select the option to boot up from your USB drive instead of your usual storage volume and you should see the following screen.

<figure><img src="../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

5\) Select `*Try or Install Ubuntu` and then `Try Ubuntu` when you get to the next screen

<figure><img src="../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

{% tabs %}
{% tab title="Executable binaries" %}
## Downloading the executable binary file

Download the latest version of the Ethereum validator deposit key generation binary file [here](https://github.com/ethereum/staking-deposit-cli/releases) and verify the checksum of the downloaded zipped file.

```sh
cd
curl -LO https://github.com/ethereum/staking-deposit-cli/releases/download/v2.7.0/staking_deposit-cli-fdab65d-linux-amd64.tar.gz
echo "ac3151843d681c92ae75567a88fbe0e040d53c21368cc1ed1a8c3d9fb29f2a3a staking_deposit-cli-fdab65d-linux-amd64.tar.gz" | sha256sum --check
```

**Expected output:**

```
staking_deposit-cli-fdab65d-linux-amd64.tar.gz: OK
```

After the checksum verification, extract the contents of the zipped file and change directory into the extracted folder.

```sh
tar xvf staking_deposit-cli-fdab65d-linux-amd64.tar.gz
cd staking_deposit-cli-fdab65d-linux-amd64
```

## Generate your validator signing keys

### \*BEFORE PROCEEDING TO THE NEXT STEP

1. #### TURN OFF YOUR ETHERNET, WIFI, AND BLUETOOTH ACCESS&#x20;
2. #### PHYSICALLY COVER ALL CAMERA DEVICES - e.g. PHONES, WEBCAMS, LAPTOP CAMERAS, PEOPLE STANDING BEHIND YOU

Run the following command to generate your validator keys. Replace `<number>` with the number of validators you want to set up and `<YourWithdrawalAddress>` with the actual withdrawal address depending on your setup choice.

```
./deposit new-mnemonic --num_validators <number> --chain <network> --eth1_withdrawal_address <YourWithdrawalAaddress>
```

1. **Native Solo Staking Setup:** Use a secure Ethereum wallet address that you own--e.g., cold wallet address, SAFE multi-sig address
2. **Diva Staking:** Skip this section. The validator key shares will be assigned to you by the Diva client.
3. **Lido CSM:** Set your withdrawal address to the following.
   * **Mainnet:** [`0xB9D7934878B5FB9610B3fE8A5e441e8fad7E293f`](https://etherscan.io/address/0xb9d7934878b5fb9610b3fe8a5e441e8fad7e293f)
   * **Holesky:** [`0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9`](https://holesky.etherscan.io/address/0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9)
4. **RocketPool (WIP):**
5. **Stader (WIP):**
{% endtab %}

{% tab title="Build from source" %}
## Building the validator key generation tool from source

Connect your OS-on-a-stick to a trusted WiFi network and fire up the Linux terminal using `Ctrl + Alt + T` .

Install system dependencies - `pip3` & `virtualenv`. _**Note:** Remove the `sudo` prefix for each line if your system returns an error._

```bash
sudo apt-get update -y && sudo apt-get upgrade -y
sudo apt install python3-venv python3-pip
sudo apt install python3-virtualenv
sudo apt-get install git
```

Create a python virtual environment to run the tool under:

```bash
virtualenv venv
source venv/bin/activate
```

Download the git repository of the tool by running. Reference and verify the URL of the git repository [here](https://github.com/ethereum/staking-deposit-cli).

```bash
git clone -b master --single-branch https://github.com/ethereum/staking-deposit-cli.git
```

Install the dependency packages for running the tool:

```bash
python3 setup.py install
pip3 install -r requirements.txt
```

**Expected output:** You should see some progress bars after a few seconds.

<figure><img src="../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>

### \*BEFORE PROCEEDING TO THE NEXT STEP

1. #### TURN OFF YOUR ETHERNET, WIFI, AND BLUETOOTH ACCESS&#x20;
2. #### PHYSICALLY COVER ALL CAMERA DEVICES - e.g. PHONES, WEBCAMS, LAPTOP CAMERAS, PEOPLE STANDING BEHIND YOU

## Generate your validator signing keys

Run the following command to generate your validator keys. Replace `<number>` with the number of validators you want to set up and `<YourWithdrawalAddress>` with the actual withdrawal address depending on your setup choice.

```bash
python3 ./staking_deposit/deposit.py new-mnemonic --num_validators <number> --chain holesky --eth1_withdrawal_address <YourWithdrawalAddress>
```

1. **Native Solo Staking Setup:** Use a secure Ethereum wallet address that you own--e.g., cold wallet address, SAFE multi-sig address
2. **Diva Staking:** Skip this section. The validator key shares will be assigned to you by the Diva client.
3. **Lido CSM:** On the `Holesky` testnet, set your withdrawal address to the Lido CSM contract address --[`0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9`](https://holesky.etherscan.io/address/0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9)&#x20;
4. **RocketPool (WIP):**
5. **Stader (WIP):**
{% endtab %}
{% endtabs %}

You will be prompted to key in the following. Select accordingly.

1. Choose your language (for the session)
2. Confirm your execution address (your withdrawal address)
3. Choose the language of your mnemonic word list (seed phrase)
4. Create a password to encrypt your validator signing keystores
5. Confirm password created in step 4

**Expected output:**

<figure><img src="../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

Next, your mnemonic word list will be generated. Write it down on a piece of paper or notebook  -\***Never store this online or on any device that is connected to the internet**.

&#x20;**Expected output:**

<figure><img src="../.gitbook/assets/image (71).png" alt=""><figcaption></figcaption></figure>

Press any key once you have written your mnemonic down and the tool will prompt you to key in your mnemonic in the same order to verify that you have recorded it correctly.

If you typed in your mnemonic correctly, you will be greeted by an ASCII art of a Rhino!

**Expected output:**

<figure><img src="../.gitbook/assets/image (72).png" alt=""><figcaption></figcaption></figure>

**There will be 2 files generated.**&#x20;

1. A `keystore-m_<timestamp>.json` file: This is your validator signing keystore that your validator node will use to sign attestations. Keep this file extremely secure.
2. A `deposit_data-<timestamp>.json`: This is the file that links your ETH deposit to your validator. You will only use this once, during the deposit process.

Store both files on a new USB drive by copying the entire staking-deposit-cli folder into it. After that, remove the original copy by running:

```
sudo rm -r $HOME/staking-deposit-cli/validator_keys
```

Restart your host device (e.g. working laptop) and remove the OS-on-a-stick. There will not be any persistent memory stored on it.

## Add validator key to the Node

Now that we have our validator signing keystore, we will need to place it in our validator node itself so that the node can sign attestations and propose blocks.

Plug in the USB drive with your validator signing keystores into your node device. Once the USB drive is plugged in, we will need to identify it. On the terminal of your node, run:

```
lsblk
```

**Expected output:**

<figure><img src="../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>

Look for your USB drive in the output list. It will take a name similar to the screenshot above - i.e. `sdx`.

After you find it, you can proceed to mount your USB drive onto the `/media` folder.

```sh
sudo mount /dev/sda1 /media
```

**Note:** Replace `sda1` with the actual name of your USB drive.

You will now be able to access your USB drive via the terminal by going into the `/media` folder.

Go into your USB drive and copy your validator signing keystore into the HOME directory of your node.

```sh
cd /media/staking-deposit-cli
sudo cp -r validator_keys ~
```

Unmount and eject your USB drive.

```sh
cd
sudo umount /media
```

Now you need to create a plain text password file for your validator node to decrypt your validator signing keystores.

First let's print and copy the file name of your validator signing keystore.

```
cd ~/validator_keys
ls
```

With the `validator_signing_keystore_file_name` copied, create the password file.

<pre><code>sudo nano <a data-footnote-ref href="#user-content-fn-1">&#x3C;validator_signing_keystore_file_name></a>.txt
</code></pre>

Type in the password you used when generating your validator keys in the earlier step. Then save and exit the file with `CTRL + O, enter, CTRL + X`.

[^1]: 
