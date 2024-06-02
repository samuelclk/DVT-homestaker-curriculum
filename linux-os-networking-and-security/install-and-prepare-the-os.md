# Install and prepare the OS

## Flash Ubuntu OS on USB Drive

### Download the latest version of Ubuntu

Now that you have assembled your hardware, you will need to install the Ubuntu OS onto your device. To do that, we will need to **create a bootable USB drive flashed with the latest Ubuntu OS.** Follow the steps below:

1. Prepare a new USB drive of at least 8GB
2.  On your working laptop, download the latest version of Ubuntu here (this might take around 30 minutes**)** - [https://ubuntu.com/download/desktop](https://ubuntu.com/download/desktop) -&#x20;

    <figure><img src="../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>
3. Once the download is complete, you will need to verify the checksum of the downloaded file to ensure it has not been tampered with during the download&#x20;

### Verify the checksum of download

Click on "verify your download" and you should see a window appearing.&#x20;

<figure><img src="../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

Open up your terminal (Mac) or Windows Power Shell (Windows) and run the following commands.

```sh
cd Downloads
echo "a435f6f393dda581172490eda9f683c32e495158a780b5a1de422ee77d98e909 *ubuntu-22.04.3-desktop-amd64.iso" | shasum -a 256 --check
```

You are good to go if you see an "OK" in the output.

```sh
ubuntu-22.04.3-desktop-amd64.iso: OK
```

### Download an ISO writer

After we download the ISO file of the latest Ubuntu version, we will need a tool to write this ISO file into the USB drive so that it is bootable when plugged into your NUC.&#x20;

1. Download and install BalenaEtcher - [https://etcher.balena.io/](https://etcher.balena.io/)
2.  Open up BalenaEtcher and choose select flash from file&#x20;

    <figure><img src="../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>
3.  Select your new USB drive under the "Select target" option&#x20;

    <figure><img src="../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>


4.  Hit the "Flash!" button and wait for the process to complete&#x20;

    <figure><img src="../.gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

## Install Ubuntu on your NUC

{% hint style="info" %}
You will need to connect your NUC device to a keyboard and monitor for the installation process
{% endhint %}

Plug your bootable USB drive into your NUC device and turn it on. Select `Try or Install Ubuntu` from the boot menu.&#x20;

<figure><img src="../.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>

Choose the following options when prompted:

1. Install Ubuntu (not Try)
2. _**Connect to the WIFI network of your Node Router**_
3. Minimal installation + Download updates while installing Ubuntu
4. Erase disk and install Ubuntu
5. Set your username and password + "Require my password to login"
6. Restart your device
7. Skip connecting to your online accounts
8. Skip setting up Livepatch
9. Select "No, don't send system info"
10. Disable location services

Your NUC device is now installed with the Ubuntu OS.

### Install the SSH Server

Open up the Ubuntu terminal on your NUC by pressing `CTRL + ALT + T` and perform the following:

1\) General updates

```sh
sudo apt update -y && sudo apt upgrade -y
```

2\) Install the ssh server

```sh
sudo apt install openssh-server
```

3\) Get the IP address of your NUC device within your Node Router subnet.

```sh
ip a
```

**Expected output:**

<figure><img src="../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>

Your NUC's IP address will be located under the `wl01` interface - e.g. 192.168.xx.xx.&#x20;

Write this down as you will need to use this IP address to access your NUC remotely and we will call this the `node_IP_address` moving forward.

You can now access your NUC (`Node`) remotely by running the following command while you are in the Node Router subnet and entering the password of the Node when prompted.

```sh
ssh <username>@<node_IP_address> -v
```

**Note:** This command will change slightly once you properly secure your Node device in the next section.

## Prepare your OS

### Install useful packages

```sh
sudo apt install curl jq htop
```

* `curl` allows you to query IP addresses, URLs, and the endpoints of internal services directly to test for connectivity and downloading files from the internet
* `jq` is a formatting package
* `htop` is a system monitoring tool&#x20;

### Configure timekeeping

We need to make sure the time on our device is the same with all other nodes so that we are able to sync with everyone else. If our timekeeping is off, we will start missing attestations (and rewards!). Verify this by running:

```bash
timedatectl
```

And check that NTP service is “active”. See screenshot below.

<figure><img src="../.gitbook/assets/Untitled (6).png" alt=""><figcaption></figcaption></figure>

If not, turn it on by running:

```bash
sudo timedatectl set-ntp on
```

### **Create a Swap Space**

A swap space _(”back-up” memory space carved out from disk space)_ is used to prevent out-of-memory errors.

Recommended swap space:

```bash
RAM     Swap Size
  8GB           3GB
 12GB           3GB
 16GB           4GB
 24GB           5GB
 32GB           6GB
 64GB           8GB
128GB          11GB
```

Disable existing swap:

```sh
sudo swapoff /swapfile
```

Create swap file:

```bash
sudo fallocate -l 6G /swapfile 
sudo chmod 600 /swapfile 
sudo mkswap /swapfile 
sudo swapon /swapfile
```

Make your OS remember the swap space settings even after rebooting: &#x20;

```bash
sudo cp /etc/fstab /etc/fstab.bak
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
sudo sysctl vm.swappiness=10
sudo sysctl vm.vfs_cache_pressure=50
sudo nano /etc/sysctl.conf
```

Add the following to the end of the `sysctl.conf` configuration file:

```bash
vm.swappiness=10
vm.vfs_cache_pressure=50
```

Save and exit the file with `CTRL + O, enter, CTRL + X`

Check your new swap space with the following commands.

```bash
htop
free -h
```

_**Expected output:**_

<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption><p><code>htop</code></p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (58).png" alt=""><figcaption><p><code>free -h</code></p></figcaption></figure>
