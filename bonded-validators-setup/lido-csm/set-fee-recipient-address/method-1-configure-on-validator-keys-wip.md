# Method 1: Configure on validator keys (WIP)

## Validator Clients

{% tabs %}
{% tab title="Teku" %}
```sh
sudo useradd --no-create-home --shell /bin/false csm_teku_validator
```
{% endtab %}

{% tab title="Nimbus" %}
```sh
sudo useradd --no-create-home --shell /bin/false csm_nimbus_validator
```
{% endtab %}

{% tab title="Lodestar" %}
```sh
sudo useradd --no-create-home --shell /bin/false csm_lodestar_validator
```
{% endtab %}

{% tab title="Lighthouse" %}
Edit the `validator_definitions.yml` file with the designated `fee_recipeint` address.

```sh
sudo nano /var/lib/lighthouse/<network>/validators/validator_definitions.yml
# Replace <network> with mainnet or holesky
```

Find the pubkeys of each of your CSM validator keystores and add the following line under each keystore as a new line. **Note:** Take note of the exact indentation.&#x20;

* **Mainnet**

```
suggested_fee_recipient: "0x388C818CA8B9251b393131C08a736A67ccB19297"
```

* **Holesky**

```
suggested_fee_recipient: "0xE73a3602b99f1f913e72F8bdcBC235e206794Ac8"
```

#### Example:

```
---
- enabled: true
  voting_public_key: "0x87a580d31d7bc69069b55f5a01995a610dd391a26dc9e36e81057a17211983a79266800ab8531f21f1083d7d84085007"
  type: local_keystore
  voting_keystore_path: /home/paul/.lighthouse/validators/0x87a580d31d7bc69069b55f5a01995a610dd391a26dc9e36e81057a17211983a79266800ab8531f21f1083d7d84085007/voting-keystore.json
  voting_keystore_password_path: /home/paul/.lighthouse/secrets/0x87a580d31d7bc69069b55f5a01995a610dd391a26dc9e36e81057a17211983a79266800ab8531f21f1083d7d84085007
  suggested_fee_recipient: "0x388C818CA8B9251b393131C08a736A67ccB19297"
- enabled: true
  voting_public_key: "0xa5566f9ec3c6e1fdf362634ebec9ef7aceb0e460e5079714808388e5d48f4ae1e12897fed1bea951c17fa389d511e477"
  type: local_keystore voting_keystore_path: /home/paul/.lighthouse/validators/0xa5566f9ec3c6e1fdf362634ebec9ef7aceb0e460e5079714808388e5d48f4ae1e12897fed1bea951c17fa389d511e477/voting-keystore.json
  voting_keystore_password: myStrongpa55word123&$
  suggested_fee_recipient: "0x388C818CA8B9251b393131C08a736A67ccB19297"
```
{% endtab %}

{% tab title="Prysm" %}
```sh
sudo useradd --no-create-home --shell /bin/false csm_prysm_validator
```
{% endtab %}
{% endtabs %}

## Automation Tools

{% tabs %}
{% tab title="ETH Docker" %}
With ETH Docker running (i.e., `ethd up`), run&#x20;

```
./ethd keys list
```

then

```
./ethd keys set-recipient 0xPUBKEY 0xADDRESS 
```

with the public key of the key you wish to set a separate fee recipient for, and the Ethereum address fees should go to.
{% endtab %}

{% tab title="ETH Pillar" %}
Not straightforward. Designed to assign custom `fee_recipient` addresses by running a separate validator client. Refer to the subpage below, under **"Automation Tools"**

{% content-ref url="method-2-configure-on-separate-validator-client.md" %}
[method-2-configure-on-separate-validator-client.md](method-2-configure-on-separate-validator-client.md)
{% endcontent-ref %}
{% endtab %}
{% endtabs %}



## ETH Docker

