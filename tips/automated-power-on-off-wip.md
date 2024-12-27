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





## Configure Wake-on-LAN

Install `wakeonlan` and `ethtool`.

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
