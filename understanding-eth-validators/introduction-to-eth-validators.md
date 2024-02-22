# Introduction to ETH Validators

## How Ethereum achieves consensus in a nutshell

In order for blockchain networks to function, there needs to be a way for all computers (i.e. nodes) in such networks to agree on who owns what at any one point in time (i.e. achieve consensus).

With a robust consensus mechanism, transactions of users can be processed in a trust-less and permission-less manner.

On Ethereum, a network of nodes and validators run by individuals and institutions alike facilitates these processes. As long as >2/3 of the validator network agrees on the latest state of the network, that state is considered finalised. This means that it will require >1/3 of staked ETH to be burnt - along with another highly improbable level of network collusion - in order to revert any transaction to this finalised state.&#x20;

_**But what if more than 1/3 | 1/2 | 2/3 of the network decides to collude anyway?**_

They could then launch varying levels of attacks on the Ethereum network with effects ranging from halting the finality of the network and censorship to even rewriting the chain history (more on this [here](https://ethereum.org/developers/docs/consensus-mechanisms/pos/attack-and-defense)).

For this reason, having a large number of individuals running validators is healthier than a small group of institutions running them on behalf of everyone.

## What are Ethereum nodes?

Nodes are the computers running a suite of software called Ethereum clients.&#x20;

These computers communicate with one another in a permissionless and trustless manner to perform broadly two activities:

1. Broadcast and relay the transactions of users across the network of nodes. This is how your transactions get discovered and eventually processed by validators.&#x20;
2. Verify transaction (and block) data independently.

Anyone can run an Ethereum node without any minimum stake, but at the same time, there are no direct rewards for doing so as well. In order to earn rewards, you will need to turn your node into a validator.

Having said that, there are still benefits of running a non-validating node on Ethereum.

#### Benefits to yourself

1. **Don't trust, verify:** No need to trust any other nodes in the network because you can verify all transaction data yourself.&#x20;
2. **Privacy and security:** You can use Dapps more securely and privately because you won't have to leak your addresses and balances to intermediaries.
3. **Lower latency:** You can connect your Ethereum wallet directly to the RPC endpoint of your own Ethereum node. This gives you sole access and priority to get your transactions included instead of having to compete with everyone else via public RPC endpoints.

#### Benefits to the Ethereum network

1. **Increase security:**&#x20;
   * Full nodes enforce the consensus rules, so they can’t be tricked into accepting blocks that don't follow them.
   * In case of an attack which overcomes the crypto-economic defences of [proof-of-stake](https://ethereum.org/en/developers/docs/consensus-mechanisms/pos/#what-is-pos), a social recovery can be performed by full nodes choosing to follow the honest chain.
2. **Increase reliability & censorship resistance:** More nodes in the network result in a more diverse and robust network, making it harder for the entire network to fail or censor transactions.
3. **Increase the flexibility of use cases:** Full nodes provide access to blockchain data for lightweight clients that depend on it (i.e. Light Clients). This helps users access and interact with Ethereum in a secure and decentralized manner without having to sync the full blockchain, unlocking a wider range of use cases.

## What are Ethereum validators?

By generating a specialised signing key and staking 32 ETH into a smart contract linking to this key, you turn your Ethereum node into a validator.

Validators are "activated" nodes that actually process the transactions of users - vs simply broadcasting and relaying - and set them in stone (i.e., finalisation). The validity of each transaction is voted on by all validators (sign attestations) in the Ethereum network and vouched for by their ETH staked. This means that once your transaction is finalised, it will cost 1/3 of all staked ETH to undo it - providing strong economic securities users!

Because validators are processing and vouching for transactions of users, they are rewarded for performing their jobs well and penalised when they fall short. If validators are found to be dishonest, malicious, or grossly negligent, they will have their stake slashed for up to the entire amount and expelled from the network.

This incentivises validators to be and remain honest.&#x20;

## Pooled Staking vs Solo Staking

In general, pooled staking is more convenient and cost-efficient but comes at the cost of exposure to a broader set of risk factors.

This is because the asset owners are usually segregated from the owners of the validator key in pooled staking models. And as the saying goes — "not your keys, not your crypto".&#x20;

That being said, if your withdrawal address has already been set, the possible damage from exposing your validator key to 3rd parties is much lesser than exposing the key to your withdrawal wallet, as uncorrelated slashing penalties typically amount to around 1.1+ ETH.

You can however, still lose ALL your staked ETH because of correlation penalties. And that is one of the risks that comes with pooled staking, especially for large players with centralised practices.

<table><thead><tr><th>Method</th><th width="179">ETH owner</th><th>Operator</th><th>Validator key owner</th></tr></thead><tbody><tr><td>Pooled staking</td><td>Stakers</td><td>External node operators (NOs)</td><td>External node operators (NOs)</td></tr><tr><td>Solo staking</td><td>Stakers</td><td>Stakers</td><td>Stakers</td></tr></tbody></table>

To illustrate the risks, here are some scenarios that could happen:

#### Risk factor #1: External node operators run infrastructure on behalf of ETH stakers

1. **Location concentration:** Amazon Web Services is used by staking institutions to host 12.5% of all validators today. An extended mass outage event can wipe out >31% of staked ETH for validators who are affected.
2. **Software concentration:** Staking institutions might have strong preferences for using majority clients due to the pressure of maximising performance and yield to their users. However, a crazy bug or glitch causing >33% of validators to violate consensus rules will wipe out all who are affected. To drive the example home, Geth has between 50% to 80% market share today.
3. **Hardware concentration:** Due to economies of scale, staking institutions run hundreds of validator keys on a single set of hardware. This increases the blast radius of affected validators in the event of hardware failures. It is worth noting, however, that this is the mildest form of concentration.

#### Risk factor #2: External node operators have a copy of validator keys generated for staking ETH

1. **Mass leakage of keys:** Security breaches of staking institutions can put a large amount of users' capital at risk of blackmail via correlated slashing. The larger the institution, the more lucrative it is for potential attackers. &#x20;
2. **Slashing incidents from operational mistakes:** Staking institutions managing a large number of validator keys are likely to use some level of automation to run their maintenance and troubleshooting process. Operational mistakes can easily cascade and affect a large number of validators under their management. Most slashing incidents to date have been triggered by institutions rather than individuals.

### Pooled staking is more convenient, with higher yields

With the high convenience offered by liquid staking providers and the diminishing validator rewards due to a large influx of new validators, you can't help but wonder why we bother dealing with the headspace and costs required for running your own home-based validator nodes.&#x20;

Besides, liquid staking providers also smooth out block + MEV rewards across th

eir entire validator set and auto-compounds your rewards, which leads to **higher median yields of roughly 0.5% - 0.8% (after fees) vs solo staking.**

This is because the occurrence and amounts of block rewards and MEV fees are entirely random, leading to a long right tail in the distribution of total rewards for solo validators where **median < mean.**

### But these come at a cost...&#x20;

However, as with all things in crypto, these conveniences come at a cost. There are two main risks to consider when using liquid staking platforms.

1. **Smart contract risks -** Smart contract exploits can happen anytime, and we have seen that not even blue chip projects (e.g. Curve) are immune to them
   * A huge contributing factor is because there is a fundamental imbalance between auditors and attackers - i.e. audits are performed once or at certain checkpoints while attackers are attempting to exploit the smart contract continuously&#x20;
2. **Governance/counter-party risks -** Centralised platforms are the first to come to mind when we think about this risk, but this exists even in DeFi because:
   1. Smart contracts are upgradable and governed by a multi-sig and;
   2. Governance votes can change the rules (although indirectly) of the interaction with the smart contracts

This means that to stake large amounts, you will need to monitor the developments of the liquid staking smart contracts you are using very closely. Or you only stake amounts that you are comfortable with losing completely.

Another thing to note is that there will be a rush to exit when things go south, and you will likely not be the first one out the door. So the key question to ask yourself is if the additional 0.5% - 0.8% APY is worth the tail risk of your total staked ETH.

### Other benefits of solo-staking&#x20;

#### Contribute to a more decentralised Ethereum network

Having more solo stakers increases the difficulty of collusion among validators. If there were only independent solo stakers running validators, it would take an enormous amount of coordination to attack the network - e.g. halting finality, censorship, and rewriting chain history.

#### **Instead of tail risks, solo-stakers are exposed to "tail gains."**&#x20;

The largest block + MEV reward received by a single validator to date is 691 ETH. Some solo stakers think of this as giving up 0.5% - 0.8% of average APY for a chance for windfall gains. However, it is important to note that the likelihood of getting rich from this is extremely low so running a validator just to hit the lottery is not the right mindset to have.&#x20;

Having said that, there are current and upcoming open-source initiatives that will allow solo-stakers to enjoy rewards-smoothing benefits that were previously only available with liquid staking providers.

1. [Dappnode MEV smoothing pool](https://discourse.dappnode.io/t/mev-smoothing-pool-for-dappnode/1804) - Subscribers pool their block+MEV rewards with all other subscribers
2. [Protocol-enshrined MEV smoothing](https://notes.ethereum.org/cA3EzpNvRBStk1JFLzW8qg) - (Still under discussion) If all MEV is smoothed out across all validators, the average rewards for solo-stakers will be on par with liquid staking aside from the auto-compounding effect&#x20;
3. [Protocol-enshrined MEV burn ](https://ethresear.ch/t/burning-mev-through-block-proposer-auctions/14029) - (Still under discussion) Burning all MEV is equivalent to sharing MEV rewards with all ETH holders
