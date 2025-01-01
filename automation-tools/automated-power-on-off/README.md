# Automated power on/off

> My method involves some custom shell scripts to work around the limitations of my UPS model and/or my current understanding of NUT. Though I try my best to implement security best practices here, please use this with caution as you will need to run custom scripts on your validator node.

### Summary of setup

* Your device (e.g., validator node) will now gracefully shut down when your UPS is cut off from it's power supply and turn itself back on when power is restored.&#x20;
* If it doesn't (like mine), then you will need to configure a separate Wake-on-LAN (WOL) server to handle automatic power-on signals. Fortunately, this can be done using a $50 Raspberry Pi.
