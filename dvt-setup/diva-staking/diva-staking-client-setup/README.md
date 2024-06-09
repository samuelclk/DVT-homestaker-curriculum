# Diva Staking client setup

## Important update:

The recommended setup method for the Holesky testnet version is the **"Default - All-in-one"** method for the smoothest experience. This method requires you to stop your external execution and consensus clients and run Geth + Prysm consensus + Prysm validator clients as part of Diva's docker stack.&#x20;

{% content-ref url="default-all-in-one-setup.md" %}
[default-all-in-one-setup.md](default-all-in-one-setup.md)
{% endcontent-ref %}

However, the more technically advanced may proceed with the Experimental method and share your feedback with the Diva Staking team. This will help the team identify potential issues for the less technically adept that will come after you!

This setup method includes a standalone Lodestar validator client as part of Diva's docker stack so you do not need to stop any of your existing clients.  &#x20;

{% content-ref url="advanced-with-standalone-lodestar-vc.md" %}
[advanced-with-standalone-lodestar-vc.md](advanced-with-standalone-lodestar-vc.md)
{% endcontent-ref %}
