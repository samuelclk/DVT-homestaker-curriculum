# Install and prepare the OS

## Flash Ubuntu OS on USB Drive

### Download the latest version of Ubuntu

Now that you have assembled your hardware, you will need to install the Ubuntu OS onto your device. To do that, we will need to **create a bootable USB drive flashed with the latest Ubuntu OS&#x20;**<mark style="color:red;">**(Currently 24.04.2 LTS)**</mark>**.** Follow the steps below:

1. Prepare a new USB drive of at least 8GB
2. On your working laptop, download the latest version of Ubuntu here (this might take around 30 minute&#x73;**)** - [https://ubuntu.com/download/desktop](https://ubuntu.com/download/desktop) -&#x20;

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

1. Once the download is complete, you will need to verify the checksum of the downloaded file to ensure it has not been tampered with during the download&#x20;

### Verify the checksum of download

Click on "verify your download" and you should see a window appearing.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Open up your terminal (Mac) or Windows Power Shell (Windows) and run the following commands.

**Mac:**

```sh
cd Downloads
echo "d7fe3d6a0419667d2f8eff12796996328daa2d4f90cd9f87aa9371b362f987bf *ubuntu-24.04.2-desktop-amd64.iso" | shasum -a 256 --check
```

**Windows:**

```sh
cd ~\Downloads; "d7fe3d6a0419667d2f8eff12796996328daa2d4f90cd9f87aa9371b362f987bf" -eq (Get-FileHash ubuntu-24.04.2-desktop-amd64.iso -Algorithm SHA256).Hash.ToLower() | ForEach-Object { if($_){"OK"}else{"FAILED"} }
```

You are good to go if you see an "OK" in the output.

```sh
ubuntu-24.04.2-desktop-amd64.iso: OK
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

We need to make sure the time on our device is the same with all other nodes so that we are able to sync with everyone else. If our timekeeping is off, we will start missing attestations (and rewards!).&#x20;

First, install `chrony`

```
sudo apt install chrony
```

Stop and disable the default timekeeping service.

```
sudo systemctl stop systemd-timesyncd
sudo systemctl disable systemd-timesyncd
```

Start and enable `chrony`

```
sudo systemctl enable chronyd
sudo systemctl start chronyd
```

Verify that `chrony` is running:

```
chronyc tracking
```

**Expected output:** Check that the time shown is correct and has low offset. i.e., system vs benchmark (NTP)  time.&#x20;

```
Reference ID    : 192.168.1.1 (time1.google.com)
Stratum         : 3
Ref time (UTC)  : Fri May 09 10:15:23 2025
System time     : 0.000024685 seconds slow of NTP time
Last offset     : +0.000031502 seconds
RMS offset      : 0.000031502 seconds
Frequency       : 10.141 ppm slow
Residual freq   : -0.004 ppm
Skew            : 0.012 ppm
Root delay      : 0.001655 seconds
Root dispersion : 0.000544 seconds
Update interval : 64.2 seconds
Leap status     : Normal
```
