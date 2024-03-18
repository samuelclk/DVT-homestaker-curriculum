# Registering your Diva node

You will need to access the web UI of the Diva client to register your Diva node.&#x20;

## Open required ports

But first, we need to temporarily open the 2 additional ports for this.

* The port `80` is used by the Operator UI and you **SHOULD** open it if you want to serve the Operator UI.

```sh
sudo ufw allow 80
```

* The port `30000` is used to access the swagger API of your node and you **SHOULD** keep it open if you want to use the Operator UI.

```sh
sudo ufw allow 30000
```

## Accessing the Web UI

2 ways to do this.

#### Local network access:

This can be used if you are in the same network as your node device - e.g. at home.

1. Connect your laptop to the same WiFi network as the one your node device is connected to
2. Enter the internal IP address of your node device into your browser using the `http://` prefix. Make sure not to use `https` here
3. Follow the steps in this short video below

{% embed url="https://www.youtube.com/watch?ab_channel=ShamirLabs&v=efkyU2oEygo" %}

#### Remote access:

This is used when you are not in the same network as your node device - e.g. away from home.

Open a new terminal window on your client machine (e.g. laptop) and perform an SSH tunnel. This maps port 8080 and 30000 on your client machine into ports 80 and 30000 of your node device.

```sh
ssh -L 8080:<internal_ip_address>:80 -L 30000:<internal_ip_address>:30000 <user>@<external_IP_address> -p <port_number> -i <ssh_key> -v
```

Once you are logged in, you can now open a new browser on your laptop and key in `http://127.0.0.1:8080` to access the Web UI.

## Register your node

Once you are able to access the Web UI, follow the steps in this short video below.

{% embed url="https://www.youtube.com/watch?ab_channel=ShamirLabs&v=efkyU2oEygo" %}

Key in the Diva API URL according to your method of access below.

* **Local network access:** Use `http://<internal IP address>:30000`&#x20;
* **Remote access:** Use `http://127.0.0.1:30000`

Key in the Diva Auth Token using the `DIVA_API_KEY` variable you set in your `.env` file during the Diva client setup step.

Complete the rest of the steps to register your Diva node on-chain.
