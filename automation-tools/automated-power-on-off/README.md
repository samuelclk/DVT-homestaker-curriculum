# Automated power on/off

## Credits

This section was inspired by Trevorn.eth's work: [**Protecting your Rocketpool node from power outages on the cheap**](https://github.com/trevhub/guides/blob/main/CheapUPS.md) and my adaptation attempts to generalise the automatic power down/up setup for a wide range of UPS models.

## Some context...

We want our validator node to gracefully shut down when the Uninterruptible Power Supply (UPS) is cut off from it's power supply and turn itself back on when power is restored.&#x20;

However, I have not tested my UPS setup with an actual power outage followed by a power restoration. I also encountered several restrictions with my particular Prolink UPS model which makes testing of actual scenarios extremely time consuming. Further, all of my simulated power outage and recovery scenarios so far have not been successful using the Network UPS Tools (NUT) default settings. _**Hence I will be using custom shell scripts to respond to power events retrieved from the UPS.**_

To do this, I use a separate Raspberry Pi device to serve as a NUT server for automated shutdowns and as a Wake-on-LAN (WoL) server for automated power-ons. Fortunately, we can get Raspberry Pis from $35 onward.

An added benefit of having a WoL server is having more control over unexpected scenarios where your devices simply do not automatically turn themselves back on after power has been restored.

In general, my goal for this guide is to be applicable to as many USB-enabled UPS models as possible. As a result, it may not be the most optimal method for the pros.

### My Constraints

* My UPS does not turn itself back on after main line power has been restored following an automatic shutdown
* The "low battery" threshold of my UPS is fixed at 35% and cannot be changed for testing
* My UPS does not turn my NUC back on when main line power is restored to it

**I reckon there will be more people who may face the same constraints as me so this guide optimises for a wide coverage of scenarios.**

## The Setup

<figure><img src="../../.gitbook/assets/Screenshot 2025-01-09 at 12.08.07 AM.png" alt=""><figcaption></figcaption></figure>

My UPS model (Prolink Pro1201sfcu) has 4 outgoing power sockets and 1 USB port.&#x20;

* **4 power sockets:** Modem, Router, Validator Node, Raspberry Pi
* **USB Socket:** Connected to my Raspberry Pi to continuously monitor for when power has been cut off or restored to the UPS

The Raspberry Pi serves as both the NUT and WoL server and "talks" to the validator node via the router.

* **NUT server:** Automatically shuts down the validator node during a power outage
* **WoL server:** Automatically powers on the validator node after power has been restored

{% hint style="info" %}
The Raspberry Pi automatically turns on when connected to a power source by default. It also does not store any critical data so I do not configure automated shutdowns for it.
{% endhint %}

A Telegram chatbot is also configured to manually and remotely trigger the Wake-on-LAN signal to power on the validator node in case the automation fails.

## Disclaimer

{% hint style="warning" %}
My method involves some custom shell scripts (with the help of AI) to work around the limitations of my UPS model and my current understanding of NUT.&#x20;

Though I try my best to implement security best practices here, please use this with caution as you will need to run custom scripts to gracefully shutdown your validator node during a power outage.
{% endhint %}
