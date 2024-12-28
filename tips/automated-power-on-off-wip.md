# Automated power on/off (WIP)

## NUT

```
sudo apt install libneon27-dev libipmimonitoring-dev nut-client
```

```
sudo nut-scanner
```

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



## Configure Wake-on-LAN (WOL)

{% hint style="info" %}
This configuration is applied to any device that needs to be remotely powered on automatically after recovering from a power failure. _**i.e., the Wake-on-LAN clients**_
{% endhint %}

Install `wakeonlan` and `ethtool` on your device.

```
sudo apt install wakeonlan
sudo apt install ethtool
```

Identify the ethernet interface of your device.

```
ip a
```

It will be the one that has the 192.168.xx.xx IP address assigned. **For example:**

<figure><img src="../.gitbook/assets/image (205).png" alt=""><figcaption></figcaption></figure>

Check your existing Wake-on-LAN status. Replace `enp2s0`with the actual ethernet interface of your device.

```
sudo ethtool enp2s0 | grep "Wake-on"
```

If you see the following, you can proceed to the next sub-section. **Else, continue following along.**

```
	Supports Wake-on: pumbg
	Wake-on: g
```

If you see `Wake-on: d` or any other letter here, it means wake-on-lan is disabled or not optimally configured so you need to change this letter to `g`.

```
sudo ethtool -s enp2s0 wol g
```

Next, make this configuration persistent even after rebooting your system.

```
sudo nano /etc/netplan/01-network-manager-all.yaml
```

Add the following lines to your file.

```
  ethernets:
    <ETHERNET_INTERFACE>:
      wakeonlan: true

```

Example of how your file should look like.

```
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    enp2s0:
      wakeonlan: true
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Apply the new configuration.

```
sudo netplan apply
```

### Enable Wake-on-LAN in BIOS

WIP

### Setup an automated Wake-on-LAN server

{% hint style="info" %}
You will need to use a Raspberry Pi or a similar low-powered device without a standby power mode for this setup. _**i.e., no on/off button, turns on once connected to a power source.**_&#x20;

This device will serve as the **Wake-on-LAN server** that sends "power on" signals to all your other devices in the same network after recovering from a power failure.
{% endhint %}

Create a wake-on-LAN script on your Raspberry Pi that covers all your other devices.

```
sudo nano /usr/local/bin/wake_devices
```

Paste the following content:

```sh
#!/bin/bash
# Wake all devices by sending magic packets using wakeonlan
# Logs are saved to a designated folder, including device names

# Define the folder to store logs
LOG_FOLDER="$HOME/wol_logs"
LOG_FILE="$LOG_FOLDER/wol_$(date '+%Y-%m-%d_%H-%M-%S').log"

# Create the log folder if it doesn't exist
mkdir -p "$LOG_FOLDER"

# Define a list of devices with their names and MAC addresses
declare -A devices=(
    ["testnode"]="aa:bb:cc:dd:ee:ff" # replace with your actual device name and MAC address 
    # Add more devices as needed in the format ["Name"]="MAC"
)

# Start logging
echo "WOL Script started at $(date)" > "$LOG_FILE"
echo "Log file: $LOG_FILE" >> "$LOG_FILE"

# Send a WOL packet to each device
for name in "${!devices[@]}"; do
    mac="${devices[$name]}"
    echo "Sending WOL packet to $name ($mac)..." | tee -a "$LOG_FILE"
    wakeonlan "$mac" >> "$LOG_FILE" 2>&1
    if [ $? -eq 0 ]; then
        echo "Successfully sent WOL packet to $name ($mac)" | tee -a "$LOG_FILE"
    else
        echo "Failed to send WOL packet to $name ($mac)" | tee -a "$LOG_FILE"
    fi
done

# End logging
echo "WOL Script finished at $(date)" >> "$LOG_FILE"
echo "Logs saved to $LOG_FILE"
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Make this script executable.

```sh
sudo chmod +x /usr/local/bin/wake_devices
```

We want this script to run automatically whenever our WOL server restarts after a power failure. Create a new systemd service file to run the script at startup.

```sh
sudo nano /etc/systemd/system/wake_devices.service
```

Add the following content:

```sh
[Unit]
Description=Wake-on-LAN script to wake all devices
After=network.target

[Service]
ExecStartPre=/bin/sleep 300
ExecStart=/usr/local/bin/wake_devices
Restart=on-failure
User=raspberrypi #use your actual system user here

[Install]
WantedBy=multi-user.target
```

{% hint style="info" %}
We want the WOL script to run only after all your other devices have completely turned off in the event of a instant recovery following a power failure, which will cause this script to fail its purpose. Hence the deliberate 300 second delay imposed in this service file via `/bin/sleep 300`
{% endhint %}

Load and run the WOL service.

```sh
sudo systemctl daemon-reload
sudo systemctl enable wake_devices.service
sudo systemctl start wake_devices.service
sudo systemctl status wake_devices.service
sudo journalctl -u wake_devices
```

Use `CTRL+C` to exit the monitoring/logging view.

### Manual Wake-on-LAN via Telegram Bot

This is useful as a backup to the automated WOL setup above in case you need to manually "wake up" your devices remote after recovery from a power failure.

{% hint style="info" %}
**Key Features:**

1. **Does not require opening ports** to each of your devices
2. Conveniently "wakes up" all your devices via Telegram. _**i.e., without needing to download new apps**_
3. Run on your Wake-on-LAN server. _**e.g., Raspberry Pi that runs 24/7**_
4. Requires the **"Setup an automated Wake-on-LAN server"** sub-section above to be completed
{% endhint %}

Create a new Telegram bot by following the steps below.

1. **Open Telegram and Message the BotFather:**
   * Search for "BotFather" in Telegram and start a conversation.
2. **Create a New Bot:**
   * Send `/newbot` to the BotFather.
   * Follow the instructions in the BotFather chat to name your bot and get its **API token**.
3. **Save the API Token:**
   * Example token: `123456789:ABCDEFGHIJKLMNOPQRSTUVWXYZ`.
4. **Add the Bot to Your Private Group:**
   * Invite the bot to your Telegram group.
5. **Get Your Chat ID:**
   * Use the bot to retrieve the **chat ID:**
     * Send a message in your group.
     * Navigate to `https://api.telegram.org/bot<YourBOTToken>/getUpdates` on your browser while replaceing `<YourBOTToken>` with your actual Telegram bot API token

Install dependencies on your WOL server.

```
sudo apt install python3-pip
pip install python-dotenv
pip install python-telegram-bot
```

Create a new folder to store the bot files.

```
sudo mkdir -p /usr/local/bin/TG_WOL_BOT
```

Create the `.env` file to store private and sensitive information such as your Telegram bot token and chat ID.

```
sudo nano /usr/local/bin/TG_WOL_BOT/.env
```

Add your **Bot API Token** and **Chat ID** as variables into `.env`

```sh
#example
BOT_TOKEN=123456789:ABCDEFGHIJKLMNOPQRSTUVWXYZ
ALLOWED_CHAT_ID=-123456789
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Secure the .env file so that only your current user can access the file.

```sh
sudo chmod 600 .env
```

Create the Telegram bot script.

```
sudo nano /usr/local/bin/TG_WOL_BOT/WOL_bot.py
```

Paste the following content:

```python
import os
from telegram.ext import Updater, CommandHandler
import subprocess
import logging

# Load .env variables    
load_dotenv()

# Get environment variables
BOT_TOKEN = os.environ.get("BOT_TOKEN")
ALLOWED_CHAT_ID = int(os.environ.get("ALLOWED_CHAT_ID"))

# Configure logging
logging.basicConfig(format='%(asctime)s - %(message)s', level=logging.INFO)

def start(update, context):
    """Send a welcome message."""
    chat_id = update.effective_chat.id
    if chat_id != ALLOWED_CHAT_ID:
        update.message.reply_text("Unauthorized!")
        return
    update.message.reply_text("Hi! I'm your Wake-on-LAN bot.")

def wol_command(update, context):
    """Run the Wake-on-LAN script."""
    chat_id = update.effective_chat.id
    if chat_id != ALLOWED_CHAT_ID:
        update.message.reply_text("Unauthorized!")
        return
    try:
        update.message.reply_text("Running Wake-on-LAN script...")
        # Run the WoL script
        result = subprocess.run(
            ["/usr/local/bin/wake_devices"],
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE,
            text=True
        )
        if result.returncode == 0:
            update.message.reply_text("Wake-on-LAN script executed successfully!")
        else:
            update.message.reply_text(f"Script failed with error:\n{result.stderr}")
    except Exception as e:
        update.message.reply_text(f"An error occurred: {str(e)}")

def main():
    """Start the bot."""
    if not BOT_TOKEN:
        print("Error: BOT_TOKEN environment variable not set.")
        return
    if not ALLOWED_CHAT_ID:
        print("Error: ALLOWED_CHAT_ID environment variable not set.")
        return

    updater = Updater(BOT_TOKEN, use_context=True)
    dp = updater.dispatcher

    # Command handlers
    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(CommandHandler("wol", wol_command))

    # Start the bot
    updater.start_polling()
    updater.idle()

if __name__ == "__main__":
    main()

```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

{% hint style="info" %}
**How to use the Telegram Bot:** Type `/start` and then `/wol` in the chat group created with your bot.

* **`/start`:** Greets the user and checks if they are authorized.
* **`/wol`:** Executes the `wake_devices` script

The **Authorization Check** ensures only messages from the allowed **Chat ID** trigger actions.
{% endhint %}

Make this python file executable.

```
sudo chmod +x /usr/local/bin/TG_WOL_BOT/WOL_bot.py
```

Create a systemd service file for the bot:

```
sudo nano /etc/systemd/system/WOL_bot.service
```

Add the following content:

```sh
[Unit]
Description=Telegram Bot for Wake-on-LAN
After=network.target

[Service]
ExecStart=/usr/bin/python3 /usr/local/bin/TG_WOL_BOT/WOL_bot.py
Restart=always
User=raspberrypi #use your actual system user here

[Install]
WantedBy=multi-user.target

```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Load and run the WOL bot service.

```sh
sudo systemctl daemon-reload
sudo systemctl enable WOL_bot.service
sudo systemctl start WOL_bot.service
sudo systemctl status WOL_bot.service
sudo journalctl -u WOL_bot.service
```

Use `CTRL+C` to exit the monitoring/logging view.
