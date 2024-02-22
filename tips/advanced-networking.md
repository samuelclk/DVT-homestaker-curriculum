# Advanced networking

## Upnp

Universal plug and play (**Upnp**) is a way for modems/routers to automatically open ports that devices connected to it are using and closes them when they are done.

To enable this, log in to your modem/router and turn it on. It's usually a one-click action located under the "Advanced", "NAT", or "Application" etc sections.

<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

**Upnp** needs to be enabled on all routers upstream and finally on your modem if you have multiple layers of routers in your network.

## Port forwarding

Port forwarding allows devices outside of your home network to access devices within. For our use case, this allows you to:

1. SSH into your validator node to perform maintenance and troubleshooting even when you are on vacation
2. Addresses the "low peer count" problem on your execution layer or consensus layer client

Due to routers/modems having different interfaces, you will need to play around with your own configuration panel or look up the official manual of your router's model.&#x20;

**Example:**

{% tabs %}
{% tab title="SSH" %}
<figure><img src="../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>


{% endtab %}

{% tab title="EL low peer count" %}
<figure><img src="../.gitbook/assets/image (117).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Cl low peer count" %}
<figure><img src="../.gitbook/assets/image (116).png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

If you have a router sitting below your modem, you will need to configure port forwarding from `modem->router->validator` using the same port numbers on both the modem and the router.&#x20;

#### Below is a repository of port forwarding instructions for a wide range of router models.

{% embed url="https://portforward.com/router.htm" %}

## VPN

Setting up a VPN is useful for the following:

1. A smoother user experience for managing your validator node remotely
2. Configuring failover beacon nodes across multiple physical locations

While there are many VPNs available, the most fuss-free of all is [Tailscale](https://tailscale.com/). Not to mention, it's free to use for the first 100 connected devices.

Quick start:

1\) Create a free account at [Tailscale](https://tailscale.com/).&#x20;

2\) Add devices under "Machines".

<figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

3\) Add your client device (e.g. working laptop) using the easy-to-follow instructions provided

{% tabs %}
{% tab title="Linux" %}
<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Windows" %}
<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Mac" %}
<figure><img src="../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}

4\) Make sure to complete the registration of the newly added device on your Tailscale account when prompted

5\) Add your validator node device by running the script provided in the terminal

6\) Add any other failover beacon node devices into this VPN

#### All of your devices added to this VPN will now be able to communicate/connect directly with one another via the IP addresses provided by Tailscale.
