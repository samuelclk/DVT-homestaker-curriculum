# FAQ

## A note about this section
This section contains community contributed answers to common questions and problems. Please always exercise your own judgement when running commands or following these instructions.

## What's the expected time for a node to finish initial sync on Ethereum mainnet?
As of Jan 2025, the expected sync time (assuming sufficient hardware and network conditions) is roughly:
- Consensus client (assuming checkpoint-sync is enabled): a few minutes
- Execution client: ~1-2 days

If your initial sync takes much longer than this, consider debugging your setup for:
- hardware problems and machine resource bottlenecks
- software errors (check logs)

## Why is my consensus client initial sync extremely slow?
Assuming checkpoint-sync is enabled, consensus client initial sync should be relatively fast (on the order of minutes).

One possibility of the cause of a slow initial sync could be a failure in checkpoint-sync (e.g. wrongly configured URL, temporary glitch etc). With such failures, depending on the implementation, the client might attempt to sync the entire history from peers which will be extremely slow.

To confirm the problem, check the following:
- Logs from consensus client indicating failures or errors related to checkpoint-sync
- Run `telnet <checkpoint-sync-url-provided> 443` to check your connection to checkpoint-sync
- Check if peer count for consensus client is low (this is a symptom of the problem above)

If you suspect checkpoint-sync is the cause of this problem, consider re-triggering the consensus sync entirely. The exact command differs by your choice of client, take `EthDocker` for example, the command would be `ethd resync-consensus`.
