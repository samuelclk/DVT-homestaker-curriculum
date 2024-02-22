# Beaconcha.in App API

## Beaconcha.in website settings

1\) Go to https://holesky.beaconcha.in on your browser and sign up for an account.

2\) Download the beaconcha.in app on your mobile phone.

3\) Once you are logged in, click on your User icon on the top right corner and select _**"Settings".**_

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.52.42 PM.png" alt=""><figcaption></figcaption></figure>

Click on the _**"Mobile App"**_ tab and select _**"Desktop"**_ as the Architecture option.

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.54.21 PM.png" alt=""><figcaption></figcaption></figure>

Select your consensus layer client from the list and copy the resulting flag with your own unique API key. As you can see, I have redacted my API key below and you should also make sure not to reveal yours.

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.57.08 PM.png" alt=""><figcaption></figcaption></figure>

## Validator node settings

Next, you will SSH into your validator node and add this flag into your Teku (or other CL) client.

Once you are logged in to your validator node, run the following command to open the configuration file of your Teku Beacon Node:

```bash
sudo nano /etc/systemd/system/tekubeacon.service
```

Add in the flag you copied earlier into the configuration file.

```bash
[Unit]
Description=Teku Beacon Node (Mainnet)
Wants=network-online.target
After=network-online.target
[Service]
User=teku
Group=teku
Type=simple
Restart=always
RestartSec=5
Environment="JAVA_OPTS=-Xmx6g"
Environment="TEKU_OPTS=-XX:-HeapDumpOnOutOfMemoryError"
ExecStart=/usr/local/bin/teku/bin/teku \
  --network=mainnet \
  --data-path=/var/lib/teku \
  --ee-endpoint=http://127.0.0.1:8551 \
  --ee-jwt-secret-file=/var/lib/jwtsecret/jwt.hex \
  --initial-state=https://beaconstate.ethstaker.cc \
  --metrics-enabled=true \
  --rest-api-enabled=true \
  --builder-endpoint=http://127.0.0.1:18550 \
  --validators-builder-registration-default-enabled=true \
  --metrics-publish-endpoint 'https://beaconcha.in/api/v1/client/metrics?apikey=<your_API_key>
  
[Install]
WantedBy=multi-user.target
```

Press `CRTL + O`, `ENTER`, then `CTRL + X` to save and exit.

Next, do the same for the Teku validator client.

```bash
sudo nano /etc/systemd/system/tekuvalidator.service
```

Add in the same flag you copied earlier into the configuration file.

```
[Unit]
Description=Teku Validator Client (Mainnet)
Wants=network-online.target
After=network-online.target
[Service]
User=teku
Group=teku
Type=simple
Restart=always
RestartSec=5
Environment="JAVA_OPTS=-Xmx6g"
Environment="TEKU_OPTS=-XX:-HeapDumpOnOutOfMemoryError"
ExecStart=/usr/local/bin/teku/bin/teku vc \
  --network=mainnet \
  --data-path=/var/lib/teku \
  --validators-external-signer-public-keys=<validator pubkeys> \
  --validators-external-signer-url=http://<external_signer_IP_address> \
  --beacon-node-api-endpoint=http://localhost:5051,http://<backup_beacon_node>:<http/rest_port_number> \
  --validators-proposer-default-fee-recipient=<designated wallet address> \
  --validators-proposer-blinded-blocks-enabled=true\
  --validators-graffiti="<yourgraffiti>" \
  --metrics-enabled=true \
  --doppelganger-detection-enabled=true \
  --metrics-publish-endpoint 'https://beaconcha.in/api/v1/client/metrics?apikey=<your_API_key>
  
[Install]
WantedBy=multi-user.target
```

Press `CRTL + O`, `ENTER`, then `CTRL + X` to save and exit.

Reload the systemd daemon, then restart the Teku beacon node and Teku validator client service. Check that both services are **“active (running)”.**

```bash
sudo systemctl daemon-reload
sudo systemctl restart tekubeacon.service tekuvalidator.service
sudo systemctl status tekubeacon.service tekuvalidator.service
```

Monitor the journal logs of each service for any error messages.

* &#x20;**For Teku Beacon Node:**

```bash
sudo journalctl -fu tekubeacon -o cat | ccze -A
```

* **For Teku Validator Client:**

```bash
sudo journalctl -fu tekuvalidator -o cat | ccze -A
```

## Beaconcha.in App settings

Open up the Beaconcha.in mobile up and play around with it:&#x20;

* 1st tab - Summary of validators in your watchlist
* 2nd tab - Search for your Validator ID or public key and check the flag on the right to add it to your watchlist
* 3rd tab -  View more device level diagnostics like CPU, RAM, disk space, networking throughput, peer count etc
* 4th tab - Configure your notification preferences for your validator on the settings

![](<../../.gitbook/assets/460x0w (2).webp>)![](<../../.gitbook/assets/460x0w (1) (1).webp>)![](<../../.gitbook/assets/Screenshot 2023-08-16 at 4.07.01 PM.png>)<img src="../../.gitbook/assets/image (74).png" alt="" data-size="original">

