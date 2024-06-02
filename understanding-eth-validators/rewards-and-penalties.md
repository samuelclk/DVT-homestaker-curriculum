# Rewards and penalties

## Rewards

In exchange for processing transactions on the Ethereum network and providing economic security, validators receive rewards in the form of issuance, transaction fees (tips), and MEV bribes. Issuance is received from performing attestations and tips + MEV bribes are received from proposing blocks.&#x20;

The breakdown of these fees on average are as follows:

1. Consensus Layer (issuance): **2.85%**
   * Block attestation
   * Sync committee duties
2. Execution Layer (Block rewards): **0.58%**
   * MEV and transaction tips

**Average total: \~3.42% APR**

<figure><img src="../.gitbook/assets/image (159).png" alt=""><figcaption><p>Last 30 days data from Rated.Network. 2nd June 2024.</p></figcaption></figure>

#### Block attestation rewards

This component of validator rewards are relatively stable and changes slowly according to the number of active validators on the network - e.g. more validators = lower attestation rewards, less validators = higher attestation rewards.

#### Sync committee rewards

Every 24 hours, 512 validators are chosen randomly out of the validator network to perform sync committees duties for the next 24 hours. During this period, the computational load on the 512 validators are increased along with the rewards they receive. However, penalties incurred also increase at the same rate if the chosen validators are offline during this period.&#x20;

The purpose of the sync committee is to allow **Light Clients** to keep track of the chain of beacon block headers.&#x20;

#### Block rewards

Every epoch (6.4 minutes), 32 validators will be chosen as the block proposers for each block in that epoch. Block proposers will receive all transaction tips (paid by users) and MEV fees (paid by block builders) for their respective blocks.

{% hint style="info" %}
**Important Note:** Because the APR from block rewards are random and can be very large when they occur (i.e. a heavy right skew), the median total APR, which is what most validators will be getting is closer to 3.5%.&#x20;
{% endhint %}

These rewards are received every \~6.4 minutes (every epoch) and the accumulated balance is automatically withdrawn to your designated wallet every 6 days.

## Penalties

### Uncorrelated downtime

On the other hand, you will be penalised if your validator goes offline or is otherwise inactive. Under normal circumstances, your validator should not go offline together with other validators - i.e. "uncorrelated downtime" - and if so, your inactivity penalties per epoch will be roughly the same as your rewards rate per epoch. Unless of course, you miss your turn to propose a block while you were inactive.

This means that you typically don't have to panic when you start seeing missed attestations and should rather take your time to make sure you don't make a mistake when you troubleshoot your validator node.

### Correlated downtime

However, if your validator node goes down together with a large portion of the network, you will be penalised more heavily.&#x20;

In an extreme scenario, where more than 1/3 of the network goes offline, causing the chain to be in danger of splitting, the ["inactivity leak"](https://eth2book.info/capella/part2/incentives/inactivity/) will be triggered.

The inactivity leak is a state of emergency to compel the inactive 1/3 to resume their duties or otherwise removing them (and their ETH) from the network until the network has >2/3 of active validators once again.&#x20;

There are two main mechanisms that will occur during an inactivity leak:

1. **Normal attestation rewards will stop for all validators.** Block proposers will still receive their normal rewards from all 3 sources
2. **Quadratic leak** - where inactive validators will suffer increasing penalties that grows quadratically over time until the network has >2/3 of active validators once again

## Double signing

Double signing is a slash-able offense and occurs when your validator:

1. Signs two different beacon blocks for the same slot while serving as proposer - i.e. endorsing two different versions of the chain's history
2. Signs an attestation that “surrounds” another one while serving as an attester - i.e. trying to change the history of the chain&#x20;
3. Signs two different attestations with the same target while serving as an attester - i.e. signing the same block twice using the same key

Although these slashing mechanisms were set in place to discourage dishonest or malicious behaviour by the validator network. They can be cause by negligence and misconfiguration as well. In fact, all of the slashing incidents to-date have been due to common technical mistakes and you can learn how to prevent them below.

{% content-ref url="../best-practices/slashing-prevention.md" %}
[slashing-prevention.md](../best-practices/slashing-prevention.md)
{% endcontent-ref %}

When your validator is slashed, the following sequence of events will take place.

1. Your validator will be forcibly scheduled to exit the network within the next 36 days
2. Receive a minimal penalty of 1/32 of your effective balance initially when a whistleblowing validator reports your validator
3. Incur additional penalties for missing your validator duties over the next 36 days until your validator exits the network&#x20;
4. Additionally, a special penalty will be imposed on your validator for correlated slashing. This penalty increases with the number of other validators that were slashed over the same period as yours and can even come up to your entire effective balance.
