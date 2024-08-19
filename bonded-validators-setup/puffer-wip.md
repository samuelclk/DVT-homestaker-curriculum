# Puffer (WIP)

## Create VM

Create an SGX-enabled VM on Azure.

```
ResourceType	 Locations    Name	            Zones	Restrictions
virtualMachines  japaneast    Standard_DC1ds_v3       1          None
```

### Azure resources with SGX enabled

Open up your Azure console.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Print out all resources with SGX enabled.

```
az vm list-skus --size Standard_DC --all --output table

```

#### List updated as at 19/08/2024 [here](https://docs.google.com/spreadsheets/d/1CyvMuyvTzpgCJdc8Tu5dA98l-z00GPQ78mC3FjMJ\_tQ/edit?usp=sharing).

## Checks

### Update packages

```
sudo apt update
sudo apt install cpuid
```

###
