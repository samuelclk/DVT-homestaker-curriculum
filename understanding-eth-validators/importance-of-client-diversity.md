# Importance of client diversity

Your Ethereum validator node is made up of various pieces of software clients working together to process and attest to transactions. Being a decentralised network, Ethereum is not dependent on any single physical server/node.&#x20;

However, if every node runs only on one single client set, the existence of crazy bugs or targeted attacks on the software layer can still disrupt this process.&#x20;

When this happens, we, as validator node operators, will suffer penalties. **These penalties increase exponentially when our validator nodes go offline together with a large portion of the network.**&#x20;

Looking at the chart below, correlation penalties involving 33% or more of the network will result in all of your staked ETH being slashed!

<figure><img src="../.gitbook/assets/Screenshot 2023-12-04 at 11.05.35 AM.png" alt=""><figcaption><p>Kiln's research on correlation penalties</p></figcaption></figure>

This is why there are at least 4 - 5 clients to choose from for execution layer and consensus layer clients today. With Geth owning more than 50% of the execution layer client market share today, the existence of a crazy bug causing validator nodes running Geth to double-sign will be catastrophic.&#x20;

This is why we will be recommending minority clients for this curriculum.

## Key considerations

Even though we should run minority clients, it does not automatically mean that any client can run on the same hardware. A key reason for Geth's popularity, other than its reliability, is the ability for it to run on minimal hardware.

**Execution layer clients (updated 4th Feb 2024)**

<table><thead><tr><th width="143">Client</th><th width="120">CPU speed</th><th width="81">RAM</th><th width="96">Storage</th><th width="161">SSD speeds</th><th width="74">Sync speed</th><th>Market share</th></tr></thead><tbody><tr><td><strong>Geth</strong></td><td>lowest</td><td>16 GB</td><td>2 TB</td><td>Good</td><td>2nd</td><td>78%</td></tr><tr><td><strong>Besu</strong></td><td>lower</td><td>32 GB</td><td>2 TB</td><td>Good</td><td>1st</td><td>5%</td></tr><tr><td><strong>Nethermind</strong></td><td>lower</td><td>32 GB</td><td>2 TB</td><td>Good</td><td>3rd</td><td>14%</td></tr><tr><td><strong>Erigon</strong></td><td>higher</td><td>32 GB</td><td>4 TB*</td><td>Good</td><td>4th</td><td>2%</td></tr></tbody></table>

_\*Erigon is optimised for running archival nodes (full nodes are sufficient for validators) and will consume 2 TB of disk space within several weeks after syncing._&#x20;

_Source on market share -_ [_https://clientdiversity.org/_](https://clientdiversity.org/)

**Consensus layer clients (updated 4th Feb 2024)**

Because the dependencies on hardware requirements is greater for the execution layer (EL) clients, the considerations for consensus layer (CL) clients will primarily be on market share.

<table><thead><tr><th width="158">Client</th><th>Market share</th></tr></thead><tbody><tr><td><strong>Prysm</strong></td><td>38.93%</td></tr><tr><td><strong>Lighthouse</strong></td><td>33.01%</td></tr><tr><td><strong>Teku</strong></td><td>18.69%</td></tr><tr><td><strong>Nimbus</strong></td><td>8.21%</td></tr><tr><td><strong>Lodestar</strong></td><td>1.16%</td></tr></tbody></table>

_Source on market share -_ [_https://clientdiversity.org/_](https://clientdiversity.org/)

Further, because all CL clients now have the checkpoint sync feature - enabling CL syncing in just a few minutes - we can rapidly switch between CL clients with minimal downtime when there are bugs in one.

## Curriculum Scope

Because of the Key Considerations above, we will be covering the following clients as the scope for this DVT Home Staker Programme:

1. Execution layer: Nethermind and Besu
2. Consensus layer: Teku, Nimbus, Lodestar
