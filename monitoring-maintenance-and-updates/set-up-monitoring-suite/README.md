# Set up monitoring suite

We will be using the following monitoring suite:

1. **Prometheus** - Expose runtime data from the Execution and Consensus clients
2. **Node Exporter** - Expose OS metrics for the Ubuntu server
3. **Grafana** - Creates dashboards from Prometheus and Node Exporter data
4. **Google Uptime Check** - Periodically pings your node to make sure it is running and alerts you when it doesn't get a response
5. **Beaconcha.in App API** - Alerts you when your node misses attestations, proposes a new block, or (touch wood) gets slashed. Also able to monitor device level diagnostics and other useful metrics such as CPU usage, RAM usage, disk space usage, networking throughput, peer count, and status of your execution & consensus layer clients
6. **Backup beacon node** - You can use the Google Cloud GUI to monitor this node
