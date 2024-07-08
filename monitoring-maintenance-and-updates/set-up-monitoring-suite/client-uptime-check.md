# Client Uptime Check

Sometimes, simply monitoring on-chain performance of your validators may not be sufficient.&#x20;

However, this method no longer works well for solo stakers running DVTs because your nodes could be offline without causing missed attestations - Recall that a cluster of X nodes is responsible for hosting Y validator keys.

This means that we either need to manually check on our nodes periodically (tedious) or act when the cluster fails to achieve consensus, leading to missed attestations, which can lead to free-rider problems.

## Custom Checker Chatbot (Free)

{% embed url="https://github.com/samuelclk/ETH-node-alerts-TG-bot" %}

This is a simple and lightweight uptime checker chatbot for Solo DVT operators to monitor as many nodes as you want (Like Google Uptime Check but free).

## Google Uptime Check (Not Free)

### Pre-requisites

You must configure port forwarding to ports 30303 and 9000 of your validator node in order for Google Uptime Check to work.

Refer to the [Configure the firewall rules](../../linux-os-networking-and-security/device-level-security-setup#configure-the-firewall-rules) sub-section of this guide to understand how to.

### Setup

Log in to your google cloud console and type _**"monitoring"**_ into the search bar. Then select the _**"Monitoring - Infrastructure and application quality checks"**_ result.&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.10.49 PM.png" alt=""><figcaption></figcaption></figure>

Select _**"Uptime checks"**_ on the left hand panel.

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.14.38 PM.png" alt=""><figcaption></figcaption></figure>

Click on +CREATE UPTIME CHECK located at the top panel.

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.18.10 PM.png" alt=""><figcaption></figcaption></figure>

You will be prompted to enter the following:

1. Protocol: `TCP`
2. Resource type: `URL`
3. Hostname: `<the external IP address of your beacon node>`
4. Port: `30303`&#x20;

_**\*Port 30303 checks for the uptime of the execution layer client. Repeat this step for Port 9000 as well to check for the uptime of the consensus layer client.**_

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.20.34 PM.png" alt=""><figcaption></figcaption></figure>

Click through the default settings until _**Step 3 - Alert & Notification**_. Then click on the _**"Notification channels"**_ drop down and then _**"MANAGE NOTIFICATION CHANNELS"**_

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.26.59 PM (1).png" alt=""><figcaption></figcaption></figure>

Set up your favourite notification channels. I like to keep it simple by using email as my alerts channel.&#x20;

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.33.06 PM.png" alt=""><figcaption></figcaption></figure>

Next, key in the name of the alert you created and test the service. If the connection is successful, you will see a "success" message.

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.38.13 PM.png" alt=""><figcaption></figcaption></figure>

Go ahead and click _**"CREATE"**_  to complete the setup.

_\*Repeat the same steps for ports 9000 and 3000._

_**Congrats! You have set up an alerts tool to check if each of your clients are running. This is useful to identify out-of-memory, database corruption, power/internet, or hardware issues.**_

{% hint style="info" %}
This is not a free tool so monitor your usage after a month and adjust your uptime check duration accordingly.
{% endhint %}
