# Wake-on-LAN (WoL)

## Configure the Wake-on-LAN (WoL) Clients

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

<figure><img src="../../.gitbook/assets/image (205).png" alt=""><figcaption></figcaption></figure>

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
      dhcp4: true
      wakeonlan: true
```

`CTRL+O`, `ENTER`, `CTRL+X` to save and exit.

Apply the new configuration.

```
sudo netplan apply
```

### Enable Wake-on-LAN in BIOS

{% hint style="info" %}
Wake-on-LAN may not be enabled on your devices by default. If so, you will need to plug in a monitor and keyboard into your device to enable it.
{% endhint %}

1. Restart your device and **press** _**F2**_ repeatedly during boot to enter BIOS Setup.
2. **Select** `Advanced`, then **select** the `Power menu`.
3. Expand the _`Secondary Power Settings`_ sub-menu and set _Wake-on-LAN from S4/S5_ to: _**Power On - Normal Boot**_.
4. **Press** _**F10**_ to save and exit the BIOS Setup.

## Set up Wake-on-LAN server

{% hint style="info" %}
You will need to use a Raspberry Pi or a similar low-powered device without a standby power mode for this setup. _**i.e., no on/off button, turns on once connected to a power source.**_&#x20;

This device will serve as the **Wake-on-LAN server** that sends "power on" signals to all your other devices in the same network after recovering from a power failure.
{% endhint %}

#### OPTIONAL: Connect your Raspberry Pi to your WiFi network

Install the network manager package.

```
sudo apt install network-manager -y
```

Connect to your WiFi network.

```
sudo nmtui
```

Follow the terminal UI to `Activate a connection`>>**Choose WiFi SSID**>>**Enter password**. _Just like how you would normally connect to a WiFi network!_

Once you are connected to your WiFi network, press `ESC` to exit the `nmtui` terminal UI.

#### Configure the Wake-on-LAN server&#x20;

Create a wake-on-LAN script on your Raspberry Pi that covers all your other devices.

```
sudo nano /usr/local/bin/wake_devices
```

Paste the following content:

{% hint style="info" %}
Add more devices as needed as new lines in the format \["Name"]="MAC Address" within the `declare -A devices=(...)` segment.
{% endhint %}

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
    # Add more devices as needed as new lines here in the format ["Name"]="MAC"
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
User=raspberrypi 
#use your actual system user above

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

### Manual Wake-on-LAN via Telegram Bot (Optional)

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
     * Send a message in the Telegram group with your bot&#x20;
     * Navigate to `https://api.telegram.org/bot<YourBOTToken>/getUpdates` on your browser while replacing `<YourBOTToken>` with your actual Telegram bot API token

Install dependencies on your **WoL server (Raspberry Pi).**

```
sudo apt install python3-pip
sudo apt install python3-dotenv
sudo apt install python3-python-telegram-bot
```

Create a new folder to store the bot files.

```sh
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

Secure the `.env` file so that only your current user can access the file.

```sh
sudo chmod 600 /usr/local/bin/TG_WOL_BOT/.env
sudo chown -R $USER:$USER /usr/local/bin/TG_WOL_BOT
```

Create the Telegram bot script.

```
sudo nano /usr/local/bin/TG_WOL_BOT/WOL_bot.py
```

Paste the following content:

```python
from telegram.ext import Application, CommandHandler
import subprocess
import os
from dotenv import load_dotenv

# Load environment variables
load_dotenv("/usr/local/bin/TG_WOL_BOT/.env")

BOT_TOKEN = os.getenv("BOT_TOKEN")
ALLOWED_CHAT_ID = int(os.getenv("ALLOWED_CHAT_ID"))

async def start(update, context):
    """Send a welcome message."""
    chat_id = update.effective_chat.id
    if chat_id != ALLOWED_CHAT_ID:
        await update.message.reply_text("Unauthorized!")
        return
    await update.message.reply_text("Hi! I'm your Wake-on-LAN bot.")

async def wol_command(update, context):
    """Run the Wake-on-LAN script."""
    chat_id = update.effective_chat.id
    # Check if chat_id is authorised
    if chat_id != ALLOWED_CHAT_ID:
        await update.message.reply_text("Unauthorized!")
        return
    try:
        await update.message.reply_text("Running Wake-on-LAN script...")
        # Run the WoL script
        result = subprocess.run(
            ["/usr/local/bin/wake_devices"],
            stdout=subprocess.PIPE,
            stderr=subprocess.PIPE,
            text=True
        )
        # Log results & notify user
        if result.returncode == 0:
            await update.message.reply_text("Wake-on-LAN script executed successfully!")
        else:
            await update.message.reply_text(f"Script failed with error:\n{result.stderr}")
    except Exception as e:
        await update.message.reply_text(f"An error occurred: {str(e)}")

def main():
    """Start the bot."""
    if not BOT_TOKEN:
        print("Error: BOT_TOKEN environment variable not set.")
        return
    if not ALLOWED_CHAT_ID:
        print("Error: ALLOWED_CHAT_ID environment variable not set.")
        return

    # Create the application instance
    application = Application.builder().token(BOT_TOKEN).build()

    # Add command handlers
    application.add_handler(CommandHandler("start", start))
    application.add_handler(CommandHandler("wol", wol_command))

    # Start the bot
    application.run_polling()

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
sudo chown -R $USER:$USER /usr/local/bin/TG_WOL_BOT
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
User=raspberrypi
#use your actual system user above
WorkingDirectory=/usr/local/bin/TG_WOL_BOT

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
sudo journalctl -fu WOL_bot.service
```

Use `CTRL+C` to exit the monitoring/logging view.
