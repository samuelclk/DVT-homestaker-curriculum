# Google Cloud

### Here are some logistics you will need to prepare before hand:

1. Laptop
2. Google Cloud account with free trial activated. Credit card details are needed but don’t have to charge it
   * [https://cloud.google.com/free](https://cloud.google.com/free)
3.  Go into your Google Cloud console&#x20;

    <figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
4.  Go to **Create a VM**&#x20;



    <figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
5.  Enable the **Compute Engine API**&#x20;

    <figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Create a new VM

Go to your google cloud console and create a new VM.

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

Choose the following settings:

1. **Name:** Choose your own name or leave it as the default setting
2. **Region:** Choose your preferred region but it is recommended to diversify away from the popular regions (e.g. US, EU) for mainnet setups to minimise the risk of <mark style="color:red;">correlated downtime.</mark> I am going with Singapore in this example.
3. **Zone:** Choose any
4. **Machine configuration:** E2
5. **Machine type:** e2-standard-2, 8GB memory

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Under **OS and Storage**>>**Boot disk**, click on the **"change"** button.

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

Select `Ubuntu` for the operating system, `Ubuntu 24.04 LTS x86` for the version, `SSD persistent disk` for the boot disk type, and `300 GB` for the size of the storage. <mark style="color:blue;">**Note:**</mark> <mark style="color:blue;"></mark><mark style="color:blue;">Use</mark> <mark style="color:blue;"></mark><mark style="color:blue;">`250 GB`</mark> <mark style="color:blue;"></mark><mark style="color:blue;">storage if 300 GB does not work for you.</mark>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Select `No service account` under **Security**>>**Identity and API access**.

<figure><img src="../../.gitbook/assets/Screenshot 2024-09-02 at 2.55.20 PM.png" alt=""><figcaption></figcaption></figure>

Check the first 2 boxes under the **"Firewall"** section.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Once you are done, click on the **"Create"** button at the bottom of the screen.

**Expected output:** You should see your VM instance coming online after loading for a few seconds

<figure><img src="../../.gitbook/assets/image (38).png" alt=""><figcaption></figcaption></figure>

&#x20;Click on the dropdown beside the **"SSH"** column and select **"Open in browser window".** Click on **"Authorize"** when prompted.

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption></figcaption></figure>

**Expected output:** Wait for the new window to load and your Ubuntu terminal will appear.

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>

Once you are logged into your VM via SSH, run a general update using the command below.

```sh
sudo apt update -y && sudo apt upgrade -y
```

## Next Steps:

1. Lido CSM
   1. [Eth-Docker](../../bonded-validators-setup/lido-csm/automations/csm-with-eth-docker.md)
   2. [Ethpillar](../../bonded-validators-setup/lido-csm/automations/csm-with-ethpillar.md)
   3. [Dappnode](../../bonded-validators-setup/lido-csm/automations/csm-with-dappnode.md)
2. [Obol Techne Bronze Speedrun](../../dvt-setup/obol/techne-bronze-speedrun-launchpad.md)
3. [SSV Operator](../../dvt-setup/ssv/ssv-operator.md)
4. **"Vanilla" Solo Staking:** Jump to the [Device level security setup](../../linux-os-networking-and-security/device-level-security-setup.md) section
