# Hardware & system requirements

Despite being catered towards home-staking, running an ETH validator node reliably still requires some thought on the choice of hardware. To run this locally in your own home, your hardware will need to meet the following requirements.&#x20;

## Minimum requirements

1. CPU: 4 cores, 8 threads
2. RAM: 64 GB&#x20;
3. Storage:&#x20;
   * 4TB NVME SSD, TLC, DRAM
   * 7000 mbps sequential read/write
   * 1,000,000 random read/write
4. Network requirements (Check with ISP):
   * **Volume**: Uncapped or at least 2 TB per month&#x20;
   * **Speeds:** At least 250mb/s shared - \*Your validator node needs dedicated internet speeds of 50 mbps download and 25 mbps upload&#x20;
   * **IP address:** Static if possible
5. Power: Uninterruptible power supply (UPS)

## "Fuss-free" requirements

1. Storage: 4TB NVME SSD, >5000 read IOPS, >1700 write IOPS, non-QLC
2. Everything else on par with the minimum requirements above&#x20;

## How each component affects performance

<table><thead><tr><th width="135">Component</th><th width="505">Impact on performance</th></tr></thead><tbody><tr><td>CPU</td><td><ul><li>Affects block execution speed (4s limit)</li><li>Will miss attestations and block proposals if this is too slow</li><li>This is one of the more expensive component</li></ul></td></tr><tr><td>RAM</td><td><ul><li>Services will shutdown/restart abruptly if the device runs out of memory, causing you to lose data. This can lead to database corruption, and in the worst case, require you to resync your validator node from scratch - missing attestations for 2 to 3 days in the meantime.</li><li>As the Ethereum network grows with more activity (e.g., more addresses, smart contracts, &#x26; transactions), there will be an increasing demand on your node's memory to maintain the chain state and propagate transactions to other nodes. </li><li>Go crazy on this if needed as it is the cheapest component. </li></ul></td></tr><tr><td>Storage</td><td><ul><li>Read and write speeds (IOPS) are the main bottleneck for block execution speeds</li><li>This is one of the more expensive component</li></ul></td></tr><tr><td>Network</td><td><ul><li>Affects latency on receiving/sending blocks, which affects overall block execution speed (4s limit)</li><li>Although not compulsory, having a static IP address improves discoverability by other nodes in the network and prevents issues of low peer count</li><li>Some ISPs also prevent port forwarding (for remote access to your node) if you don't have a static IP address</li></ul></td></tr><tr><td>Power</td><td><ul><li>Sudden power disruptions - e.g. lightning, power trips - will cause your node to shutdown uncleanly, causing you to lose data. This can lead to database corruption, and in the worst case, require you to resync your validator node from scratch - missing attestations for 2 to 3 days in the meantime.</li></ul></td></tr></tbody></table>

##
