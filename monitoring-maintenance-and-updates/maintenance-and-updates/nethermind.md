# Nethermind

## Updating Nethermind

### Download Nethermind and configure the service

[Download](https://downloads.nethermind.io/) the latest version of Nethermind and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://github.com/NethermindEth/nethermind/releases/download/1.25.4/nethermind-1.25.4-20b10b35-linux-x64.zip
echo "05848eaab4b1b621054ff507e8592d17 nethermind-1.25.4-20b10b35-linux-x64.zip" | md5sum --check
```

{% hint style="info" %}
Each downloadable file comes with it's own checksum (see below). Replace the actual checksum and URL of the download link in the code block above.

{% hint style="info" %}
Make sure to choose the amd64 version. Right click on the linked text and select "copy link address" to get the URL of the download link to `curl`.
{% endhint %}
{% endhint %}

<figure><img src="../../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>

_**Expected output:** Verify output of the checksum verification_

```
nethermind-1.25.4-20b10b35-linux-x64.zip: OK
```

If checksum is verified, extract the files and move them into the `(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
unzip nethermind-1.25.4-20b10b35-linux-x64.zip -d nethermind
sudo cp -a nethermind /usr/local/bin/nethermind
rm -r nethermind-1.25.4-20b10b35-linux-x64.zip nethermind
```

### Restart the Nethermind service

Reload the systemd daemon to register the changes made, start Nethermind, and check its status to make sure its running.

```bash
sudo systemctl start nethermind.service
sudo systemctl status nethermind.service
```

**Expected output:** The output should say Nethermind is **“active (running)”.** Press `CTRL-C` to exit and Nethermind will continue to run.&#x20;

Use the following command to check the logs of Nethermind’s syncing process. Watch out for any warnings or errors.

```bash
sudo journalctl -fu nethermind -o cat | ccze -A
```

Press `CTRL-C` to exit.

## Pruning Nethermind

### Activating pruning mode

Your ETH validator node will use up the available disk space over time as the state grows. In order to avoid out-of-storage errors, it is advisable to prune your execution clients periodically.

Nethermind is able to run its pruning process in the background without interrupting it's operations but it is very heavy task so you will experience some performance degradation  during this time (\~20 - 30 hours).

To enable the pruning process for Nethermind, open up the `systemd` configuration file:

```bash
sudo nano /etc/systemd/system/nethermind.service
```

and append the following flags into the `[Service]` section of the file depending on your preference of pruning method.

{% tabs %}
{% tab title="Manual" %}
```
[Service]
<existing_flags> \
--Pruning.Mode=Hybrid \
--Pruning.FullPruningTrigger=Manual
```

This will start the pruning process once you reload the daemon and restart the service.
{% endtab %}

{% tab title="By remaining disk space" %}
<pre><code><strong>[Service]
</strong>&#x3C;existing_flags> \
--Pruning.Mode=Hybrid \
--Pruning.FullPruningTrigger=VolumeFreeSpace \
--Pruning.FullPruningThresholdMb=300000
</code></pre>

This will instruct Nethermind to activate its pruning mechanism once the amount of available free space on your disk falls below 300GB.

_**Note:** The recommended threshold is 250GB but lets be a little more prudent._
{% endtab %}

{% tab title="By disk space used" %}
```
[Service]
<existing_flags> \
--Pruning.Mode=Hybrid \
--Pruning.FullPruningTrigger=StateDbSize \
--Pruning.FullPruningThresholdMb=1200000
```

This will instruct Nethermind to activate its pruning process once the state size grows beyond 1.2TB.
{% endtab %}
{% endtabs %}

Save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.

Restart the daemon and the Nethermind service.&#x20;

```
sudo systemctl daemon-reload
sudo systemctl restart nethermind.service
sudo systemctl status nethermind.service
```

**Expected output:** The status should say Nethermind is **"active (running)".**

### Monitoring pruning progress

If you have configured the pruning mode correctly, you should see the following logs&#x20;

**At initiation:**

> Full Pruning Ready to start: pruning garbage before state BLOCK\_NUMBER with root ROOT\_HASH.\
> **WARN**: Full Pruning Started on root hash ROOT\_HASH: do not close the node until finished or progress will be lost.

_**\*As the warning states, do not restart your node from here on until the pruning process is completed.** Else you will have to restart the whole pruning process, or worse, end up with a corrupted database._&#x20;

After a few minutes, you will start to see some progress logs:

> Full Pruning In Progress: 00:00:57.0603307 1.00 mln nodes mirrored.\
> Full Pruning In Progress: 00:01:40.3677103 2.00 mln nodes mirrored.\
> Full Pruning In Progress: 00:02:25.6437030 3.00 mln nodes mirrored.

When the pruning process is completed, you will see the following output:

> Full Pruning Finished: 15:25:59.1620756 1,560.29 mln nodes mirrored.

### Tips

The pruning process can take **more than 30 hours** to complete (depending on CPU and IO speeds). During this time, you may experience degraded performance on your validator node - i.e. missing \~10% of attestations.&#x20;

Hence, it is important to time your pruning schedule to avoid coinciding with your scheduled sync committee or block proposer duties. You can check for these below.

* [Check scheduled sync committee duties](https://www.coincashew.com/coins/overview-eth/guide-or-how-to-setup-a-validator-on-eth2-mainnet/part-ii-maintenance/checking-my-eth-validators-sync-committee-duties)
* [Check scheduled block proposal duties](https://wenmerge.com/block-proposer-schedule/)

If you want to trigger the pruning process immediately, set the threshold of the following flag to whatever amount your available disk space is left with.

> `--Pruning.FullPruningThresholdMb=<bytes>`

Run `df -h` on your terminal to find out how much available disk space you have remaining.&#x20;
