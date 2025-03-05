# Monitoring

## On-chain Monitoring

Basic on-chain monitoring can be done using the following tools found on the CSM Widget directly. Under the `Dashboard` navbar tab scroll all the way down to the `External Dashboards` expandable list.

<figure><img src="../../.gitbook/assets/image (209).png" alt=""><figcaption></figcaption></figure>

### Beaconcha.in V2

Shows a wide range of performance data for all of the validator keys under your CSM Operator.

<figure><img src="../../.gitbook/assets/image (210).png" alt=""><figcaption></figcaption></figure>

To receive notifications on when your validators go offline, open up the Validators pop-up window, click into each key, and add them to your  watchlist. You will need to create a free account on Beaconcha.in in order to use this.

<figure><img src="../../.gitbook/assets/image (211).png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (212).png" alt="" width="563"><figcaption></figcaption></figure>

### Rated Explorer

Provides more details on performance of all validator keys under your CSM Operator in aggregate. No built-in notifications available.

<figure><img src="../../.gitbook/assets/image (214).png" alt="" width="375"><figcaption></figcaption></figure>

### Lido Operators

Provides an overall view of validator keys uploaded onto the CSM Widget. Mouse over each category for a description of each metric.

<figure><img src="../../.gitbook/assets/image (217).png" alt=""><figcaption></figcaption></figure>

### Lido MEV Monitoring

Provides details on blocks proposed by the validators under your CSM Operator over a period. If the Fee recipient is not equal to the designated fee recipient addresses for the respective networks, "[MEV Stealing](https://docs.lido.fi/staking-modules/csm/guides/mev-stealing/)" will be detected.

<figure><img src="../../.gitbook/assets/image (218).png" alt="" width="563"><figcaption></figcaption></figure>

## Off-Chain Monitoring: Grafana

For more detailed monitoring, refer to the section below.

{% content-ref url="broken-reference" %}
[Broken link](broken-reference)
{% endcontent-ref %}

## CSM Performance Threshold

Staying above the Lido CSM `Performance Threshold` enables CSM Operators to qualify for the socialized node operator rewards.

<figure><img src="../../.gitbook/assets/image (219).png" alt="" width="563"><figcaption></figcaption></figure>

Lido CSM uses the following parameters to measure the Performance Threshold, where `Performance Threshold = Average Validator Network Performance - Performance Leeway`.

* Performance of each validator = `Successful Attestations / Total Attestations Assigned`
* Average Validator Network Performance = A good proxy to use will be the `Average Participation Rate` found on [Rated Network](https://explorer.rated.network/network?network=mainnet\&timeWindow=1d\&rewardsMetric=average\&geoDistType=all\&hostDistType=all\&soloProDist=stake).
* Performance Leeway = `500 Basis Points (5%)`
* Frame = `28 days`

e.g., For a 28 day period, if the Average Validator Network Performance is 99.6%, then your validators will need to achieve a 94.6% Performance level in order to qualify for the socialized node operator rewards.

### Tips to improve performance

{% content-ref url="../../best-practices/maximising-uptime-and-performance.md" %}
[maximising-uptime-and-performance.md](../../best-practices/maximising-uptime-and-performance.md)
{% endcontent-ref %}
