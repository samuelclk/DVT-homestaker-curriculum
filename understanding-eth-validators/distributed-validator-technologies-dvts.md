# Distributed Validator Technologies (DVTs)

## What are DVTs

DVTs split the signing key and duties of a single validator into a cluster of validators, requiring them to act collectively for signing responsibilities.&#x20;

This removes the single point of failure on the hardware and signing key level that can result in offline penalties and slashing. Attackers will have a much harder time getting access to the entire validator signing key as it is now split up into many parts. It also allows for some nodes to go offline, as a subset of the machines in each cluster can do the necessary signing.

A highly simplified way to think about this is a multi-sig for the operations of validator nodes.&#x20;

All validator signing operations will require consensus among their respective clusters at a defined threshold (e.g. 62.5%). For example, in a DVT cluster of 16 validators, at least 10 validators need to attest to the same block to complete the operation - providing 2 benefits:

1. Offline fault tolerance of 6/16&#x20;
2. Slashing fault tolerance of 10/16

This reduces single points of failure and makes the validator set more robust!&#x20;

## How do I use DVTs as a solo staker?

Using DVTs involves a relatively trivial step  for solo stakers. It works by running an additional lightweight DVT client on top of your existing execution + consensus client setup.

You then expose the endpoints of your execution, consensus, and, in some cases, validator clients and connect them to your DVT client. This way, your DVT client can now "talk" to your existing execution + consensus client and perform its duties.

{% tabs %}
{% tab title="With DVT" %}
<figure><img src="../.gitbook/assets/image (160).png" alt=""><figcaption><p>Simplified illustration of how DVT clients fit into the existing solo staker setup</p></figcaption></figure>


{% endtab %}

{% tab title="Without DVT" %}
<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Simplified illustration of a solo staker setup</p></figcaption></figure>
{% endtab %}
{% endtabs %}

## How do DVTs affect solo stakers?

For solo stakers, DVTs enable us to achieve levels of performance and security rivalling large staking institutions, lower the financial barriers to entry, and even allow us to scale our income.

### Improving performance

In a study conducted by Diva Staking, a set of validator nodes with 5% downtime each can reduce this number to less than 0.01% by incorporating their DVT solution. This is a huge improvement compared to the typical single-node operator, showcasing the resilience and reliability of the DVT system.

**In other words, Diva Staking's DVT turns a set of 16 nodes with 95% uptime into a Distributed Validator with 99.99% uptime!**

### Improving security

As solo stakers, our weakest link is the physical location of our validator nodes and, as a consequence, the location of our validator signing keys.&#x20;

This is because we leave information about our home addresses everywhere - from daily conversations to online forms. Attackers will be able to blackmail us by inducing slashing incidents if we lose sole access to our validator signing keys. As a result, most solo stakers prefer to stay anonymous.

DVTs can essentially render this attack vector useless, as attackers will need to collect the minimum shards required to restore the original key. This is because when using DVTs, there are no validator seed phrases or private keys to manage. Instead, each validator is operated by distributed key shares which together act as the validator key.

### Lowering financial requirements

A nice consequence of splitting our validator signing key is the splitting of the 32 ETH minimum requirement into equivalent parts.

For example, forming a cluster of 10 validators on Obol will require each participant to contribute 3.2 ETH to activate a single validator key.

_**Other solutions like Diva Staking enable solo stakers to get started with just 1 ETH!**_

### Scaling income

DVT solutions allow solo stakers to use their existing staking hardware to participate in DVT clusters, earning additional fees when they do so.&#x20;

Some examples below:&#x20;

1. **SSV:** Enables validator signing key owners to choose the SSV operator's node as one of the operators in their preferred cluster. SSV operators collect a fee in return for providing this service.
2. **Diva Staking:** Assigns node operators into clusters and assigns each cluster new key shares of active validators randomly. Solo stakers do not need to convince ETH holders to choose their node themselves in this case.
3. **Obol:** Enables solo stakers to form clusters with up to 9 other node operators manually. In most cases, the required ETH to activate validator keys is deposited by the cluster participants themselves.

<figure><img src="../.gitbook/assets/image (168).png" alt=""><figcaption><p>Illustration of how a single solo staking setup can be used to participate in multiple DVT clusters across various DVT providers</p></figcaption></figure>

## Different types of DVT solutions

### Obol

Obol works by requiring node operators to form and fund their own clusters. Fees among participants in each cluster can be determined among themselves - e.g. node operators who are able to run minority clients proficiently might be able to demand a higher fee within the cluster.&#x20;

The Obol client (Charon) sits between the validator client and the beacon node and does not handle the validator signing operations directly. Instead, it coordinates consensus within the cluster before any interaction with the beacon chain occurs.

### SSV

SSV works by turning nodes that run the SSV client into a "open market" DVT operator that validator key owners can select to shard their keys to. SSV operators are free to set their own fees - denominated in the SSV token - that validator key owners will pay when choosing such SSV operators. &#x20;

The SSV client replaces the validator client in a typical validator node setup. In this case, both the validator signing operations and coordination of consensus within the cluster is handled by the SSV client. &#x20;

### Diva Staking

The main difference is that Diva provides integrated Liquid Staking + DVT in one single trustless and permissionless system.

Diva node operators put up minimal collateral, and are automatically and randomly assigned to a cluster of 16 and assigned validator key shares.&#x20;

This way, Diva node operators do not need to form and fund their own clusters manually as with the case of Obol. Nor do they need to attract validator key owners to choose their node as with the case of SSV.

Stakers also get to enjoy the benefits of putting their ETH to work with DVT-enabled node operators - i.e. huge reduction in slashing risks and offline penalties - without having to pay additional fees denominated in a secondary token.  &#x20;

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="563"><figcaption><p>Source: <a href="https://docs.divastaking.com/install">https://docs.divastaking.com/install</a></p></figcaption></figure>

{% hint style="info" %}
Diva Staking supports Besu as the execution client as well despite being missing from the diagram above.
{% endhint %}
