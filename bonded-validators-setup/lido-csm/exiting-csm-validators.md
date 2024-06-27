# Exiting CSM validators

{% hint style="info" %}
<mark style="color:red;">**This page is still WIP**</mark>
{% endhint %}

## Exit requests from Lido

CSM operators can receive exit requests from the Lido Protocol when their bond amounts fall below the minimum threshold for the number of validator keys funded by the CSM.

Exit requests can be observed in the number `Unbonded` keys on the operator dashboard of the CSM Web App.

<figure><img src="../../.gitbook/assets/Screenshot 2024-06-27 at 3.48.39 PM.png" alt="" width="375"><figcaption></figcaption></figure>

When this happens, CSM operators must initiate an exit on the requested number of validator keys as soon as possible (within 96 hours) to avoid penalty measures from having `Stuck Keys`.

## Stuck Keys

`Stuck Keys` accrue when CSM operators do not perform timely (within 96 hours) exits on the required number of CSM-funded validator keys when requested by the Lido Protocol.&#x20;

**Penalties of having `Stuck Keys` include:**

1. New validator keys of the CSM operator will not be funded&#x20;
2. New staking rewards stop accruing for the CSM operator

Penalties are lifted when there are no more `Stuck Keys`

## How to exit

### Manual exit

Exiting CSM-funded validator keys works the same way as exiting solo staking validator keys.

Refer to this dedicated guide for exiting validators put together by Remy Roy.

{% embed url="https://github.com/eth-educators/ethstaker-guides/blob/main/voluntary-exit.md" %}

You can also follow the steps extracted from Remy's guide below (Linux only).

#### Overview of the validator exit process

<figure><img src="../../.gitbook/assets/Screenshot 2024-06-27 at 5.57.48 PM.png" alt=""><figcaption></figcaption></figure>

### Automated exit (WIP)

Because exit requests need to be fulfilled within 96 hours, CSM operators can choose to run a service called the `validator-ejector` to automate this process.

#### Using the `validator-ejector` package

Download the [latest git repository](https://github.com/lidofinance/validator-ejector) of `validator-ejector`.

```sh
cd
git clone https://github.com/lidofinance/validator-ejector.git
cd validator-ejector
```

Create the environment file and open it up for editing.

```sh
sudo cp sample.env .env
sudo nano .env
```

Replace the following environment variables in the .env file with the details below.

```
EXECUTION_NODE=http://<Internal_IP_Address>:<RPC_Port>
CONSENSUS_NODE=http://<Internal_IP_Address>:<REST_Port>
LOCATOR_ADDRESS=0x1eDf09b5023DC86737b59dE68a8130De878984f5
STAKING_MODULE_ID=1
OPERATOR_ID=123

MESSAGES_LOCATION=messages
MESSAGES_PASSWORD=pass


```
