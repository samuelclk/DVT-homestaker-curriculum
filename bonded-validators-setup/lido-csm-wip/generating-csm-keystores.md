# Generating CSM keystores

## Pre-flight checks for existing solo stakers

1. Make sure that you have the mnemonic (24-word seed phrase) of your existing validator keys in case you overwrite them by mistake while going through this process
2. Make sure you have removed duplicates of your existing solo staking validator keys on the `$HOME` directory of your node machine. Otherwise, remove them now.

```sh
#on your node machine
sudo rm -r ~/validator_keys
```

{% hint style="info" %}
This is to reduce the risk of importing your existing solo staking keystores by mistake into your CSM validator client, which can lead to slashing.&#x20;
{% endhint %}

## Generate CSM keystores

Visit the Validator key generation page and follow the steps accordingly.&#x20;

{% content-ref url="../../native-solo-staking-setup/validator-key-generation.md" %}
[validator-key-generation.md](../../native-solo-staking-setup/validator-key-generation.md)
{% endcontent-ref %}

{% hint style="info" %}
Recall that the steps for both native solo staking and CSM are the same except for the requirement to use a specific`withdrawal_address`**:** [`0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9`](https://holesky.etherscan.io/address/0xF0179dEC45a37423EAD4FaD5fCb136197872EAd9)( **Holesky)**
{% endhint %}
