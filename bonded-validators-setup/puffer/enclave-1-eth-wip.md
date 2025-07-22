---
hidden: true
---

# Enclave: 1 ETH (WIP)

## Create VM

Create an SGX-enabled VM on Azure.

```
ResourceType	 Locations    Name	            Zones	Restrictions
virtualMachines  japaneast    Standard_DC1ds_v3       1          None
```

### Azure resources with SGX enabled

Open up your Azure console.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Print out all resources with SGX enabled.

```
az vm list-skus --size Standard_DC --all --output table

```

#### List updated as at 19/08/2024 [here](https://docs.google.com/spreadsheets/d/1CyvMuyvTzpgCJdc8Tu5dA98l-z00GPQ78mC3FjMJ_tQ/edit?usp=sharing).

## Checks

### Update packages

```
sudo apt update
sudo apt install cpuid
```

### Dependencies

Rust

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Cargo

```
sudo apt install cargo
```

OpenSSL & pkg-config

```
sudo apt-get install libssl-dev pkg-config
```

#### **Set the `PKG_CONFIG_PATH` Environment Variable**

```
export PKG_CONFIG_PATH=/usr/lib/x86_64-linux-gnu/pkgconfig:$PKG_CONFIG_PATH
```

## Non scp registration file transfer

&#x20;



### Method 1: Manual copy-paste

Print out the contents of the `registration_docker_001.json` file.

```
cd
cat ~/coral/output/registration_docker_001.json
```

Copy the entire output starting from the opening curly brackets `{` to the closing curly brackets `}` and paste it onto a text editor / notepad on your laptop.

**Example:**

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Save this plain text file as `registration_docker_001.json`.&#x20;

{% hint style="info" %}
On Windows notepad, select `"All file types"` before saving.
{% endhint %}
