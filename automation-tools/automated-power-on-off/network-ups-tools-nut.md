# Network UPS Tools (NUT)

## NUT Server

> **Pre-requisites:** The [Wake-on-LAN page](wake-on-lan-wol.md) must be completed in order to use this guide

{% hint style="warning" %}
You will need a UPS with a USB port for this setup.
{% endhint %}

Plug your Raspberry Pi into the UPS power socket and connect them via a USB cable.

Install the Network UPS Tools package on your Raspberry Pi.

```
sudo apt update
sudo apt install nut nut-client nut-server
```

Inspect the NUT folder.&#x20;

```
sudo ls -l /etc/nut
```

You should see the following configuration files which we will be customising in this guide.

```
nut.conf ups.conf  upsd.conf  upsd.users  upsmon.conf  upssched.conf
```

Identify key information about your UPS.

```
sudo nut-scanner
```

**Example output:**

```
Scanning USB bus.
No start IP, skipping SNMP
Scanning XML/HTTP bus.
No start IP, skipping NUT bus (old connect method)
Scanning IPMI bus.
[nutdev1]
	driver = "blazer_usb"
	port = "auto"
	vendorid = "0665"
	productid = "5161"
	product = "USB to Serial"
	vendor = "INNO TECH"
	bus = "003"
```

**Back up the exiting `ups.conf` file as a copy and edit the main file.**

<pre><code><strong>sudo cp /etc/nut/ups.conf /etc/nut/ups.conf.example
</strong><strong>sudo nano /etc/nut/ups.conf
</strong></code></pre>

Replace the file contents by matching the output from the `nut-scanner` output above. Use `CTRL+T` and then `CTRL+V` to clear all file contents.

**Example:**

```
[nutdev1]
    driver = blazer_usb
    port = auto
    desc = "USE PRODUCT OR VENDOR HERE"
    vendorid = 0665
    productid = 5161
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

**Back up the exiting `upsd.conf` file as a copy and edit the main file.**

<pre><code><strong>sudo cp /etc/nut/upsd.conf /etc/nut/upsd.conf.example
</strong><strong>sudo nano /etc/nut/upsd.conf
</strong></code></pre>

Replace the file contents with the following. This will enable your other devices (NUT clients) to talk to your NUT server for shutdown/power-on signals. Use `CTRL+T` and then `CTRL+V` to clear all file contents.

```
LISTEN 0.0.0.0 3493
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

**Back up the exiting `nut.conf` file as a copy and edit the main file.**

<pre><code><strong>sudo cp /etc/nut/nut.conf /etc/nut/nut.conf.example
</strong><strong>sudo nano /etc/nut/nut.conf
</strong></code></pre>

Replace the file contents with the following. Use `CTRL+T` and then `CTRL+V` to clear all file contents.

```
MODE=netserver
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

**Back up the exiting `upsd.users` file as a copy and edit the main file.**

<pre><code><strong>sudo cp /etc/nut/upsd.users /etc/nut/upsd.users.example
</strong><strong>sudo nano /etc/nut/upsd.users
</strong></code></pre>

Replace the file contents with the following. Use `CTRL+T` and then `CTRL+V` to clear all file contents.

```sh
[monuser]
  password = secret
  upsmon master
#"upsmon", "secret", and "master" need to match the contents of the upsd.users
# to be set below
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

**Back up the exiting `upsmon.conf` file as a copy and edit the main file.**

<pre><code><strong>sudo cp /etc/nut/upsmon.conf /etc/nut/upsmon.conf.example
</strong><strong>sudo nano /etc/nut/upsmon.conf
</strong></code></pre>

_**Add the following as new lines** to the bottom of the existing file content._

```sh
MONITOR nutdev1@localhost 1 upsmon secret master
#"upsmon", "secret", and "master" need to match the contents of the upsd.users set above
NOTIFYFLAG ONLINE SYSLOG+EXEC+WALL
NOTIFYCMD /etc/nut/online.sh # we will create this shell script later
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

**Create the `online.sh` shell script** which tells all your devices to power up when it detects that power supply to your UPS has been restored following a power outage.

{% hint style="info" %}
_**Note:** I am using a simple script to handle the automated power on sequence because I can't get my NUT to work as intended. Let me know if anyone managed to get yours working._
{% endhint %}

```sh
sudo nano /etc/nut/online.sh
```

Paste the following content.&#x20;

```sh
#!/bin/bash
# Log the start of the script
logger "NUT: Checking UPS status for ONLINE event"

# Fetch the UPS status
status=$(upsc prolink@localhost ups.status)

# Check if the UPS is on battery
if [[ "$status" == "OL" ]]; then
    logger "NUT: UPS on line power detected. Waiting 60 seconds before powering up all devices."
    
    # Wait for 60 seconds
    sleep 60
    
    # Log the shutdown initiation
    logger "NUT: Initiating power up after 60-second delay."
    /usr/local/bin/wake_devices "UPS online"
else
    logger "NUT: UPS status is not ONLINE (status: $status). No power up triggered."
fi

```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

{% hint style="info" %}
This online.sh script makes use of the wake\_devices script that we created in the Wake-on-LAN page.
{% endhint %}

Make this shell script executable.

```sh
sudo chmod +x /etc/nut/online.sh
```

Restart the NUT services.

```sh
sudo service nut-server restart
sudo service nut-client restart
sudo systemctl restart nut-monitor
sudo upsdrvctl stop
sudo upsdrvctl start
```

Inspect the `nut-server` and `nut-monitor` for errors.

```
sudo journalctl -fu nut-server  -o cat | ccze -A
sudo journalctl -fu nut-monitor -o cat | ccze -A
```

**Example output:**

```sh
#nut-server
Starting Network UPS Tools - power devices information server... 
fopen /run/nut/upsd.pid: No such file or directory 
listening on 0.0.0.0 port 3493 
listening on 0.0.0.0 port 3493 
Connected to UPS [prolink]: blazer_usb-prolink 
Connected to UPS [prolink]: blazer_usb-prolink 
Startup successful 
Started Network UPS Tools - power devices information server. 
User monuser@127.0.0.1 logged into UPS [prolink]

#nut-monitor
Starting Network UPS Tools - power device monitor and shutdown controller... 
fopen /run/nut/upsmon.pid: No such file or directory 
UPS: prolink@localhost (master) (power value 1) 
Using power down flag file /etc/killpower 
Startup successful 
nut-monitor.service: Can't open PID file /run/nut/upsmon.pid (yet?) after start: Operation not permitted 
nut-monitor.service: Supervising process 1206 which is not our child. We'll most likely not notice when it exits. 
Started Network UPS Tools - power device monitor and shutdown controller. 
nut-monitor.service: Supervising process 1206 which is not our child. We'll most likely not notice when it exits. 
nut-monitor.service: Supervising process 1206 which is not our child. We'll most likely not notice when it exits. 
```

`CTRL+C` to exit logging view.

## NUT Clients (WIP)

{% hint style="info" %}
The following steps need to be configured on all your other devices to power down gracefully when there is a power outage.
{% endhint %}

**Create the `onbatt.sh` shell script** which tells your device to shut down gracefully when it detects that power supply to your UPS has been cut off (e.g., due to a power outage).

{% hint style="info" %}
_**Note:** I am using a simple script to handle the automated shutdown sequence because I can't get my NUT to work as intended. Let me know if anyone managed to get yours working._
{% endhint %}

```sh
sudo nano /etc/nut/onbatt.sh
```

Paste the following content.&#x20;

```sh
#!/bin/bash
# Log the start of the script
logger "NUT: Checking UPS status for ONBATT event"

# Fetch the UPS status
status=$(upsc prolink@localhost ups.status)

# Check if the UPS is on battery
if [[ "$status" == "OB" ]]; then
    logger "NUT: UPS on battery power detected. Waiting 60 seconds before shutting down."
    
    # Wait for 60 seconds
    sleep 60
    
    # Log the shutdown initiation
    logger "NUT: Initiating shutdown after 60-second delay."
    sudo /sbin/shutdown -h now "UPS on battery power"
else
    logger "NUT: UPS status is not ONBATT (status: $status). No shutdown triggered."
fi

```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Make this shell script executable.

```sh
sudo chmod +x /etc/nut/onbatt.sh
```

Allow the nut user to run only the `/sbin/shutdown` to power down your device without needing the `sudo` (superuser) password.

```sh
sudo visduo
```

Add the following as a new line in the file.

```
nut ALL=(ALL) NOPASSWD: /sbin/shutdown
```

Restart the NUT services.

```sh
sudo service nut-client restart
sudo systemctl restart nut-monitor
sudo upsdrvctl stop
sudo upsdrvctl start
```

### Enable auto-power-on in BIOS (Optional)

{% hint style="info" %}
This is optional as your Raspberry Pi will send a power-on signal to all your devices when power is restored but still good to have as a backup.
{% endhint %}

1. Restart your device and **press** _**F2**_ repeatedly during boot to enter BIOS Setup.
2. **Select** `Advanced`, then **select** the `Power menu`.
3. Expand the _`Secondary Power Settings`_ sub-menu and set  `After Power Failure` to `Power On`.
4. **Press** _**F10**_ to save and exit the BIOS Setup.
