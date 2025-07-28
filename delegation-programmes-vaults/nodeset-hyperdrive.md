---
description: >-
  For existing Ethereum Mainnet node operators: Solo Stakers, Rocketpool, Lido
  CSM, SSV, Obol, Stakewise etc
---

# Nodeset Hyperdrive

{% hint style="warning" %}
This guide assumes you have an existing Ethereum Mainnet execution and consensus client already synced and running.
{% endhint %}

## Install dependencies

General updates, curl, git, docker.&#x20;

```sh
sudo apt update -y && sudo apt upgrade -y
sudo apt install git curl -y
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

Add your current user to the docker group, then restart your device to apply the change.&#x20;

```sh
sudo usermod -aG docker $USER
sudo reboot 0
```

## Installing Hyperdrive

Download the latest binary release of Hyperdrive into `/usr/bin` and make this binary file executable.

```sh
sudo wget https://github.com/nodeset-org/hyperdrive/releases/latest/download/hyperdrive-cli-linux-amd64 -O /usr/bin/hyperdrive && sudo chmod +x /usr/bin/hyperdrive
```

Install Hyperdrive.

```sh
hyperdrive service install
```

## Configuring Hyperdrive

```sh
hyperdrive service config
```

