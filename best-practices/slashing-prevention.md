# Slashing prevention

1. Never import the same validator signing keystore onto more than 1 validator node and to be more prudent,&#x20;
   * Never store your validator signing keystores on more than 1 running device
   * Do not expose your validator signing keystores or validator seed phrase to any other entity or on any online device. This is so that you can be sure you retain full control over them
   * Always delete the keystores and any cached versions of it on your old device or VMs when doing migration
2. It is recommended to import the slashing protection database of your existing validator keys into a new validator client when performing client or hardware migrations
   * Always stop the validator client service before exporting the slashing protection database of your existing validator client
3. Consider using the doppelganger protection feature of your consensus layer client
4. Always wait for at least 15 minutes and verify that your validator has missed 2 epochs on [beaconcha.in](https://beaconcha.in/) before your start your new validator client when doing migrations
5. If you need to migrate your validator keys onto a new VM / hardware or client, always rehearse your migration process on the testnet first before you attempt to do so on the mainnet. Create your own playbook similar to [this](https://hackmd.io/0fAqTy8iSIKViJO5HOf3Nw) so that you can refer to it while you are doing the actual migration
