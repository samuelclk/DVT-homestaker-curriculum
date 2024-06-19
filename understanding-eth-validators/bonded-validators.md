# Bonded Validators

## What are bonded validators?

Conventionally, running an Ethereum validator node as a solo staker required you to deposit 32 ETH into the [beacon deposit contract](https://etherscan.io/address/0x00000000219ab540356cBB839Cbe05303d7705Fa).

However, there are options available today to significantly reduce this capital requirement in the form of bonded validators. The basic mechanism works as follows:

1. **Liquid stakers** stakes native ETH into smart contracts of liquid staking protocols
2. **Node operators** provide some amount of ETH as collateral or bond (e.g., 1/2/4/8 ETH) to serve as the first line of defense against slashing events, poor performance, & MEV theft.
3. **Liquid staking protocols** assign some amount of staked ETH of liquid stakers to node operators to fulfill the 32 ETH minimum requirement per active validator key
4. **Fee distribution:** Liquid staker pay some fees (e.g., 10% or 14%) to the liquid staking protocol and a majority portion (or all) of this flows to node operators&#x20;

Hence, bonded validators enable node operators to lower their capital requirements and boost their rewards rate as compared to native solo staking.

## Examples of bonded validators

<table><thead><tr><th width="104">Option</th><th width="112">ETH bond</th><th width="139">ETH matched</th><th width="144">Non-ETH bond</th><th width="102">Fee %</th><th>Note</th></tr></thead><tbody><tr><td>Rocketpool</td><td>8</td><td>24</td><td>2.4 ETH worth of RPL</td><td>14% out of 14% total</td><td>8% APR on RPL staked</td></tr><tr><td>Stader</td><td>4</td><td>28</td><td>0.4 ETH worth of SD</td><td>6% out of 10% total</td><td>7.97% APR on SD staked</td></tr><tr><td>Lido</td><td>2</td><td>32</td><td>None</td><td>8% out of 10% total</td><td>Bond provided in stETH which also accrues rewards</td></tr><tr><td>Ether.fi (WIP)</td><td></td><td></td><td></td><td></td><td></td></tr><tr><td>Puffer (WIP)</td><td></td><td></td><td></td><td></td><td></td></tr></tbody></table>

## How do I run bonded validators as a solo staker?

Similar to DVT clients, running a bonded validator setup simply requires you to spin up a separate lightweight (mostly) client on top of your existing execution + consensus client setup.&#x20;

You then expose the endpoints of your execution, consensus, and, in some cases, validator clients and connect them to your bonded validator client. This way, your bonded validator client can now "talk" to your existing execution + consensus clients and perform its duties.

{% tabs %}
{% tab title="Bonded validators" %}
<figure><img src="../.gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Solo staking" %}
<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Simplified illustration of a solo staker setup</p></figcaption></figure>
{% endtab %}
{% endtabs %}

You can also run multiple bonded validator clients on the same hardware, provided that you have sufficient resources (e.g., CPU, RAM, Disk) on your hardware to meet the minimum requirements of each additional bonded validator client.

<figure><img src="../.gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
You can also run DVT clients alongside bonded validator clients in the same way.
{% endhint %}