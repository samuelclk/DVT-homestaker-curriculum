# Monitoring your Diva Node

There will be 3 levels of metrics you as the Diva operator will be monitoring on.

1. **On-chain metrics:** How effective you are as an operator in maximising uptime and performance for the validator keys assigned to you - e.g. attestation effectiveness, active/inactive status, rewards & penalties
2. **Device-level metrics:** How does the health of your hardware look like? - e.g. internal temperature, CPU/RAM/storage usage, read/write speeds, network latency
3. **Client-level metrics:** Are there any warnings or errors due to misconfigurations or anomalies? - e.g. database corruption, out-of-memory errors, no auto-restart, undiscoverable endpoints, potential slashing violations

## Monitoring On-chain metrics

### Operator UI

You will be able to see all the validator key shares you have been assigned by accessing your Operator UI via a browser.

<figure><img src="../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>

Recap on how to access the Operator UI if needed below.

{% content-ref url="registering-your-diva-node.md" %}
[registering-your-diva-node.md](registering-your-diva-node.md)
{% endcontent-ref %}

### Divascan

Divascan ([https://divascan.io/](https://divascan.io/)) is an on-chain explorer similar to Etherscan that provides metrics on all operators, nodes, activated validator keys, and stakers of Diva Staking. This is a community-contributed project and is not an official product of Diva Staking.

The tabs you will be interested in as a Diva node operator are **"Operators"** and **"Nodes".** Each operator can operate multiple nodes and each node can in turn be assigned multiple validator key shares.

<figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

Under these tabs, you will be able to look for your operator ID - which is the wallet address you used when registering your node during the [Registering your Diva node](registering-your-diva-node.md) step.

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

Clicking into your operator ID will bring you to a page showing all of the nodes that have been registered to your operator ID, along with how many validator key shares have been assigned to each node.&#x20;

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Clicking into a Node ID here will show you a summary of all validator key shares assigned to that node, along with their corresponding public keys on the beacon chain.

<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

It is worth noting that this method merely provides a very high level overview.

Negative performance does not immediately mean that there is something wrong with your node because each validator key has a fault tolerance rate of 5/16 - e.g. if more than 11 nodes are offline, the validator key assigned will be offline even if your node is online.

The reverse is true as well. Just because your assigned validator keys are chugging along, it does not mean that your nodes are performing up to standards.

The Diva Staking team will monitor the performance of each operator in a trust-less manner via the use of Zero-Knowledge Proof oracles and you will be penalised or ejected from the network for low performance.

For this reason, we will need other methods of monitoring your performance.

### Beaconcha.in & Rated.Network

This method is used to monitor the performance of your assigned validator keys individually.&#x20;

Although it may not be practical to do this for large amounts of key shares, it is still an extremely tool for monitoring alerts and troubleshooting.

#### Beaconcha.in

1\) Go to [https://holesky.beaconcha.in/](https://beaconcha.in/) and search for the validator pubkey you want to inspect. The validator pubkey can be found using Divascan (see above section)

<figure><img src="../.gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

2\) You will then be able to see the status, the summary performance, and individual attestations of the validator you want to inspect. However, the industry defers to another explorer for the "Effectiveness" metric.

<figure><img src="../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>

#### Rated.Network

Rated.Network provides a more wholistic measure of "Effectiveness" (more details [here](https://docs.rated.network/methodologies/ethereum-beacon-chain/rated-effectiveness-rating)) and you use it in the same way as Beaconcha.in - by searching for the validator pubkey or ID that you want to inspect.

<figure><img src="../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

## Monitoring device performance with Grafana

The Diva service comes with a pre-configured docker container running Grafana, a highly extensible system/device-level monitoring dashboard.

<figure><img src="../.gitbook/assets/image (122).png" alt=""><figcaption><p>Example of a pre-configured Diva dashboard on Grafana</p></figcaption></figure>

**Here's how you can access this dashboard:**

1\) First, make sure the Grafana docker container is running and exposing port 3000.

```sh
docker ps -a
```

&#x20;**Expected output:**

<figure><img src="../.gitbook/assets/image (123).png" alt=""><figcaption></figcaption></figure>

2\) Open port 3000 on your device-level firewall

```sh
sudo ufw allow 3000
```

3\) Identify the internal IP address of your node device.

```sh
ip a
```

**Expected output:** Look for an IP address that is similar to the format - 192.168.xx.xx. That will be the internal IP address.

<figure><img src="../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

4\) Choose one of the following methods to access the Grafana dashboard depending on your network access.

**Local (e.g. at home):**

Connect your working laptop to the same router as your node device and enter `http://<internal_IP_address>:3000` of the node device (e.g. 192.168.1.45) in your browser.

**Remote (e.g. out of home):**

Create an SSH tunnel into your node device. **Note:** Your SSH port needs to be open/forwarded from your modem/router into your node device. Check out the port forwarding section below if needed.

{% content-ref url="../tips/advanced-networking.md" %}
[advanced-networking.md](../tips/advanced-networking.md)
{% endcontent-ref %}

```
ssh -L 3000:<internal_IP_address:3000 <user>@<external_ip_address> -p <SSH_port> -i <SSH_key> -v 
```

Once you are logged in to your node device, you can now enter `http://127.0.0.1:3000` into the browser on your working laptop.

{% hint style="info" %}
Make sure you enter`http` and not `https here`
{% endhint %}

## Monitoring client performance with system logs

1\) Check that all docker containers are active and running

```sh
docker ps -a
```

**Expected output:**

<figure><img src="../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

2\) Check and follow logs of individual containers

```sh
docker logs <CONTAINER_ID> -f --tail 5
```

This prints out the last 5 lines of the monitoring logs and new lines as they appear. You can change the number of lines as needed. Check the output for warnings or errors and troubleshoot them against the list of common errors in the next section.

If none of the scenarios matches your issue, you can raise them in the [Diva Staking discord channel](https://discord.gg/A4tfy6t3) for targeted support.

### Expected outputs

**Main containers:**

{% tabs %}
{% tab title="Diva" %}
<figure><img src="../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Lodestar" %}
<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

**Note:** suggestedFeeRecipeint is set to the "zero" address and burnt during this testnet phase but will be set to a designated pooling address on the mainnet.
{% endtab %}

{% tab title="Operator UI" %}
<figure><img src="../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Diva Reloader" %}
\*Before being assigned any validator key shares from Diva Staking

<figure><img src="../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

**Monitoring/Telemetry containers:**

{% tabs %}
{% tab title="Node Exporter" %}
<figure><img src="../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Prometheus" %}
<figure><img src="../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Grafana" %}
<figure><img src="../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Jaegar" %}
<figure><img src="../.gitbook/assets/Screenshot 2024-03-18 at 11.20.07 AM.png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Vector" %}
<figure><img src="../.gitbook/assets/Screenshot 2024-03-18 at 11.23.34 AM.png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

## Common errors

### Diva Client

1\) Warning that says "cannot start p2p network without identity". This means you have not [registered your Diva node via the Operator UI](registering-your-diva-node.md)

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

### Lodestar Client

1\) Unable to connect to Diva container endpoint.

```
Dec-16 15:34:07.791[]                 info: Lodestar network=goerli, version=v1.12.1/85e44ef, commit=85e44efe73d1ad5228486c73ea9b1fcf3f036193
Dec-16 15:34:07.793[]                 info: Connecting to LevelDB database path=/var/lib/lodestar/validators/validator-db
 ✖ FetchError: Request to http://diva:9000/api/v1/eth2/publicKeys failed, reason: connect ECONNREFUSED 172.27.0.4:9000
    at wrappedFetch (file:///usr/app/packages/api/src/utils/client/fetch.ts:10:11)
    at processTicksAndRejections (node:internal/process/task_queues:95:5)
    at externalSignerGetKeys (file:///usr/app/packages/validator/src/util/externalSignerClient.ts:111:15)
    at getRemoteSigners (file:///usr/app/packages/cli/src/cmds/validator/signers/index.ts:170:54)
    at Object.validatorHandler [as handler] (file:///usr/app/packages/cli/src/cmds/validator/handler.ts:91:19)
```

#### **Fixes:**&#x20;

* Make sure your Diva client is active by running `docker ps -a`
* Ensure that you have [registered your Diva node via the Operator UI](registering-your-diva-node.md)
