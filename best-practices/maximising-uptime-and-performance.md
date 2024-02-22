# Maximising uptime and performance

## Hardware

In general, don't overly scrimp on hardware otherwise you will miss more attestations than normal or even be unable to sync your node - especially if you want to run minority clients. Some recommendations for hardware and system requirements below given the recent increase in load on ETH validator nodes.

1. **CPU:** Get something that is fast enough
   1. Compare the list of CPU speeds [here](https://sech.me/boinc/Amicable/cpu\_list.php). Try to choose something with >5.0 GFLOPS/core
2. **RAM:** At least 32GB&#x20;
3. **Storage:** 2TB NVME SSD equivalent
   1. Choose from the "good" list [here](https://gist.github.com/yorickdowne/f3a3e79a573bf35767cd002cc977b038)

## SOPs

1. Update your clients in a timely manner as these usually come with performance improvement fixes
   * Create a new server on Discord to receive alerts on new releases and patches of your execution layer and consensus layer clients. Keep this channel clean.
   * Set alerts from the announcement channels to your own server
2. Make sure your NTP service is active after running updates
3. Set up the monitoring suite detailed in this guide so that you are alerted on any downtime promptly
4. Maintain your own SOPs table for dealing with the various scenarios of downtime, including edge cases. Improve on this over time based on your own setup environment.
5. During normal operations, configure your clients to restart themselves automatically if they go down. This is in accordance to this guide
6. Prune your execution layer clients before you reach <300GB of available space
7. Check for scheduled sync committee duties [here ](https://www.coincashew.com/coins/overview-eth/guide-or-how-to-setup-a-validator-on-eth2-mainnet/part-ii-maintenance/checking-my-eth-validators-sync-committee-duties)and block proposal duties [here](https://wenmerge.com/block-proposer-schedule/) before starting the pruning process



