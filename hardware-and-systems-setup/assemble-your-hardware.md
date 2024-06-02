# Assemble your hardware

{% hint style="info" %}
If your hardware has not arrived yet, follow the link below to spin up a Google Cloud virtual machine (VM) to perform the **"Prepare your OS"** steps. Return to this page once you have your VM set up and are able to log in via SSH.
{% endhint %}

{% embed url="https://stakesaurus.gitbook.io/stakesaurus-testnet-eth-validator-practice/hardware-vms-and-systems-setup/preparing-your-vm-validator-node" %}

Once you are logged into your VM via SSH, run a general update using the command below.

```sh
sudo apt update -y && sudo apt upgrade -y
```

Then, jump straight into the `Device level security setup` section next.&#x20;

{% content-ref url="../linux-os-networking-and-security/device-level-security-setup.md" %}
[device-level-security-setup.md](../linux-os-networking-and-security/device-level-security-setup.md)
{% endcontent-ref %}

## Inspect your hardware

Inspecting your hardware physically is an important step to remove the possibility of supply chain attacks. This happens when the hardware is somehow compromised (e.g. with a keylogger) before it reaches you.

Open up your node device - the Intel NUC in this case - and make sure there are no unknown components that look badly soldered onto your motherboard (the big green plate). &#x20;

<figure><img src="../.gitbook/assets/image (97).png" alt=""><figcaption><p>Basic anatomy of the Intel NUC device</p></figcaption></figure>

## Assembling your hardware

#### RAM:

* Align the gold plates of your 16GB Lexar DDR4-3200 SODIMM memory chip with each memory slot on the NUC _- starting first from the inner slot -_ and slot it into the slot at a diagonal elevation.&#x20;
* Once the gold plates of the memory chip is snugly slotted in to the memory slot, gently push the memory chip downwards until you hear it "click" into place

#### **Storage:**&#x20;

* Unscrew the small screw near the WIFI chip
* Align the gold plates of your NVME SSD chip with the SSD slot on the NUC and slot it in
* Gently push and hold the SSD chip down and screw the small screw back into its original slot to hold the SSD chip in place. **Note:** You do not have to screw it on too tightly   &#x20;
