# Device level security setup

## Configure remote access via SSH keypair

#### Generating an SSH key pair

On your client machine (e.g. working laptop), open up your terminal (Mac) or Windows Powershell (Windows) and run:

```sh
ssh-keygen -t ed25519 -C <USERNAME>
```

{% hint style="info" %}
Replace `<USERNAME>` with your actual username if you are using cloud VMs. Your username typically comes before the **"@"** symbol in your VM's terminal.
{% endhint %}

Press `Enter` and set a password to encrypt your SSH private key.

**Expected output:**

<figure><img src="../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

#### Add SSH pubkey to your Node

While the private key portion of the SSH keypair stays in your working laptop, we will need to add the public key portion to your Node.

{% tabs %}
{% tab title="Transfer remotely" %}
On your working laptop, run:

```sh
ssh-copy-id -i ~/.ssh/id_ed25519.pub <username>@<node_IP_address>
```

Enter the password of your NUC device when prompted.
{% endtab %}

{% tab title="Transfer physically" %}
Print out the SSH pubkey string and copy it. On your working laptop, run:

```
cat ~/.ssh/id_ed25519.pub
```

You will see a similar output below:

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBKRXS4XpZoBcuqvXVN2sRNNolKL+ZHy2Xnzx27Op3uY ethnode
```

SSH into your NUC and enter your password when prompted:

```
ssh <username>@<node_IP_address> -v
```

Create the authorized SSH key file manually.

```
sudo mkdir -p ~/.ssh
sudo nano ~/.ssh/authorized_keys
```

Paste your copied SSH pubkey string into the `authorized_keys` file and press `CTRL + O, enter, CTRL + X`  to save the file and exit.
{% endtab %}

{% tab title="On Google Cloud" %}
Print out the SSH pubkey string and copy it. On your working laptop, run:

```sh
cat ~/.ssh/id_ed25519.pub
```

You will see a similar output below:

```sh
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBKRXS4XpZoBcuqvXVN2sRNNolKL+ZHy2Xnzx27Op3uY sam
```

Copy this SSH pubkey and enter it into your Google Cloud VM.

{% embed url="https://youtu.be/NJQhUMqPN9A" %}

SSH into your VM with your custom SSH key.

```sh
# on your laptop
ssh <username>@<External_IP_address> -p 22 -i .ssh/id_ed25519 -v
```
{% endtab %}
{% endtabs %}

### Change the SSH port and disable remote password login

```bash
sudo nano /etc/ssh/sshd_config
```

1. Uncomment `#AuthorizedKeysFile` if it is commented (by removing the `#` in front of it)
2. Change `KbdInteractiveAuthentication yes` to `KbdInteractiveAuthentication no` and uncomment (by removing the `#` in front of it)
3. Change `PasswordAuthentication yes` to `PasswordAuthentication no` and uncomment (by removing the `#` in front of it)
4. Change `#PermitRootLogin prohibit-password` to `PermitRootLogin no` , removing the `#` prefix

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.

Now we restart the SSH server so it registers the new settings:

```bash
sudo systemctl restart sshd
sudo systemctl restart ssh
```

Now you will only be able to access your node remotely using your SSH private key. The command for your SSH connection will be amended slightly from before to:

```
ssh <username>@<node_IP_address> -p 22 -i .ssh/id_ed25519 -v
```

### Configure the firewall rules

The basic rules we will implement are as follows:

1. Deny all incoming traffic by default
2. Allow all outgoing traffic by default
3. Allow incoming traffic via port \<your\_chosen\_SSH\_port> for SSH access
4. Allow incoming traffic via port 30303 and 30304 for the execution clients to connect with other nodes
5. Allow incoming traffic via port 9000 and 9001 for the consensus clients to connect with other nodes
6. Allow incoming traffic via port 3000 and 3001 for Grafana to display monitoring dashboards for your node

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow <your_chosen_SSH_port>/tcp
sudo ufw allow 30303 
sudo ufw allow 9000
sudo ufw allow 3000 
```

With these configurations set up, you will have blocked off all but 4 possible openings for potential attackers to enter from.&#x20;

* Ports 30303 and 9000 will be used by Nethermind and Teku. We will create dedicated user accounts with root access disabled for these services
* Port 3000 is only accessible within your local area network (i.e. not exposed to the public internet
* Your chosen SSH port is secured by your SSH private key so its virtually impossible to brute force the access.

#### Turn on the firewall

Now lets turn the firewall on and check our configurations before moving on.

```bash
sudo ufw enable
sudo ufw status numbered
```

You should see something similar to the screenshot below:

<figure><img src="../.gitbook/assets/Screenshot 2023-08-09 at 3.31.44 PM.png" alt=""><figcaption></figcaption></figure>

### **Set up brute force protection**

Even though having our SSH key access implemented means an attacker will need 25 million years to try all combinations, lets go ahead and make it even harder for them - by limiting the number of attempts per IP address to 5 tries and blocking them after.

_**Install the software:**_

```bash
sudo apt install -y fail2ban
```

_**Open the configuration file:**_

```bash
sudo nano /etc/fail2ban/jail.d/ssh.local
```

_**Add the following contents to the configuration file:**_

```bash
[sshd]
enabled = true
banaction = ufw
port = <your_chosen_SSH_port>
filter = sshd
logpath = %(sshd_log)s
maxretry = 5
```

Once you're done, save and exit with `Ctrl+O`and `Enter`, then `Ctrl+X`.

_**Finally, restart the service:**_

```bash
sudo systemctl restart fail2ban
```

### Enable automatic security updates

```bash
sudo apt update
sudo apt install -y unattended-upgrades update-notifier-common
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

_**Add the following contents to the configuration file:**_

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
APT::Periodic::AutocleanInterval "7";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Remove-New-Unused-Dependencies "true";
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "02:00";
```

Once you're done, save and exit with `Ctrl+O`and `Enter`, then `Ctrl+X`.

_**Finally, restart the service:**_

```bash
sudo systemctl restart unattended-upgrades
```

#### Check the logs for any warnings:

```sh
sudo cat /var/log/unattended-upgrades/unattended-upgrades.log
```

If you see following warnings, proceed to the next step.

```
2024-02-01 04:48:24,012 WARNING System is on battery power, stopping
2024-02-01 06:19:01,972 WARNING System is on battery power, stopping
2024-02-01 17:53:48,650 WARNING System is on battery power, stopping
```

If your device is definitely connected to a power source, amend the `50unattended-upgrades` file directly.

```
sudo nano /etc/apt/apt.conf.d/50unattended-upgrades
```

Look for the following line and uncomment it by removing the `//` prefix.

```
// Unattended-Upgrade::OnlyOnACPower "false";
```
