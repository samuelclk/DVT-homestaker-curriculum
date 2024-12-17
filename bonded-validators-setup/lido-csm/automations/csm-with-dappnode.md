# CSM with Dappnode

## Accessing your Dappnode

Connect your Dappnode to your home router using a LAN cable and turn it on.

There are 3 different ways to access your dappnode.

1. **Dappnode's own WiFi hotspot:** Connect your working computer/laptop to the Dappnode WiFi hotspot and paste `http://my.dappnode` in your browser
2. **Local network access:** Connect your working computer/laptop to the same WiFi network as the router the Dappnode is connected to and paste `http://dappnode.local` in your browser
3. **VPN access:** This method requires additional configuration after initially logging in to your Dappnode using either of the above 2 methods. This is covered in the video guide below.

**Video Guide by Dappnode**

{% embed url="https://www.youtube.com/watch?v=Z1uDv_J7wlg" %}

## Overall guide

The Dappnode team has put together a guide for using Dappnode to setup and run ETH validators via Lido CSM.

{% embed url="https://docs.dappnode.io/docs/user/staking/ethereum/lsd-pools/lido/overview" %}
(Open this link in a new tab)
{% endembed %}

## Validator Key Generation

Note that the steps for [**Generate Keystores and Deposit Data**](https://docs.dappnode.io/docs/user/staking/ethereum/lsd-pools/lido/register) on testnet and mainnet will differ.&#x20;

## Testnet KeyGen Process

Follow along the Dappnode CSM guide in the embedded link above and simply generate your testnet validator keystores + deposit data on your working laptop.

## Mainnet KeyGen Process

Because the keystore generation process for Mainnet needs to be a lot more secure, we will have to perform additional steps as detailed in the section linked below.

{% content-ref url="../../../keystore-generation-and-mev-boost/validator-key-generation.md" %}
[validator-key-generation.md](../../../keystore-generation-and-mev-boost/validator-key-generation.md)
{% endcontent-ref %}

Refer back to the [**Dappnode CSM guide for Steps 3 & 4**](https://docs.dappnode.io/docs/user/staking/ethereum/lsd-pools/lido/register#3-install-the-lido-csm-package) once your **validator keystores** and **deposit data** have been generated.
