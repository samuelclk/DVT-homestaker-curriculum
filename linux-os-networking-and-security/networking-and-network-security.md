# Networking & network security

## Network architecture

There are 2 ways to prepare your home network for your validator node.

{% hint style="info" %}
ISPs in some countries combine both modem and routers into a single device.
{% endhint %}

{% tabs %}
{% tab title="Shared router" %}
It is entirely possible and also sufficiently safe to connect your validator node directly to your existing home router if you do not plan to have un-trusted guests over at your home often.

<figure><img src="../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

* Secure your node router properly by setting strong passwords on the WIFI and device level. Do not expose these passwords or let anyone else connect to the WIFI network or log in to the device level of your node router.
{% endtab %}

{% tab title="Dedicated router" %}
Adding a dedicated router in between your validator node and existing home router offers a greater level of segregation and adds another security layer. Useful if you plan to host guests at home frequently.

<figure><img src="../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

* Your existing home router will first be connected to a dedicated "node router" on the downstream via cable. This node router will be set to "router" mode and not "Access Point (AP)" mode - this will create a subnet within your main network for segregation of your regular home devices from your validator node setup.&#x20;
* The validator node will be connected to the node router further downstream via cable
* Secure your node router properly by setting strong passwords on the WIFI and device level. Do not expose these passwords or let anyone else connect to the WIFI network or log in to the device level of your node router.
* You will need to be connected to the WIFI network of your Node Router in order to access your Validator Node using a separate device (e.g. working laptop)
{% endtab %}
{% endtabs %}

{% hint style="info" %}
If you need to be away from home for long periods, port forwarding will need to be configured on both your Home Modem and your Home Router (i.e. Modem->Home Router->Node Router) to allow incoming connections from outside of your home network.&#x20;

* This is so that you can access your validator node for troubleshooting and maintenance even if you are not at home.
* You can also setup a VPN for remote access
* Turn off port forwarding on your Home Modem when you are no longer away from home
{% endhint %}

Check out the Port Forwarding page below after you are done setting up your validator node.

{% content-ref url="../tips/advanced-networking.md" %}
[advanced-networking.md](../tips/advanced-networking.md)
{% endcontent-ref %}

## Network security model

1. Your validator node will be secured with an SSH key so that only users who have this SSH key can access it
2. Any home devices that become compromised will not be able to access your validator node which sits in a separate subnet
3. If you think your SSH keys could be leaked, turn off any port forwarding settings and change the SSH key pair in your Validator Node

{% tabs %}
{% tab title="Shared Router" %}
<figure><img src="../.gitbook/assets/image (151).png" alt=""><figcaption></figcaption></figure>
{% endtab %}

{% tab title="Dedicated Node Router" %}
<figure><img src="../.gitbook/assets/image (150).png" alt=""><figcaption></figcaption></figure>
{% endtab %}
{% endtabs %}
