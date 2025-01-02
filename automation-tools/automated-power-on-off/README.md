# Automated power on/off

## Credits

This section was inspired by Trevorn.eth's work: [**Protecting your Rocketpool node from power outages on the cheap**](https://github.com/trevhub/guides/blob/main/CheapUPS.md) and my adaptation attempts to generalise the automatic power down/up setup.

## Disclaimer

{% hint style="warning" %}
My method involves some custom shell scripts to work around the limitations of my UPS model and/or my current understanding of NUT. Though I try my best to implement security best practices here, please use this with caution as you will need to run custom scripts to gracefully shutdown your validator node during a power outage.
{% endhint %}

## Context

We want our validator node to gracefully shut down when the UPS is cut off from it's power supply and turn itself back on when power is restored.&#x20;

However, I have not tested my UPS setup with an actual power outage followed by a power restoration. I also encountered several restrictions with my particular Prolink UPS model which makes testing of actual scenarios extremely time consuming. Further, all of my simulated power outage-recovery scenarios so far have not been successful using the Network UPS Tools (NUT) default settings. Hence I will be using custom scripts to respond to power events retrieved from the UPS.

To do this, I use a separate Raspberry Pi device to serve as a NUT server for automated shutdowns and a Wake-on-LAN server for automated power-ons.

Fortunately, we can get Raspberry Pis from $35 onward.

Because my goal for this guide is to be applicable to as many USB-enabled UPS models as possible, my method may not be the most optimal for the pros.

## Setup Overview

WIP
