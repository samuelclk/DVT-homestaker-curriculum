# Lodestar

## Updating Lodestar

As we are running the Lodestar services via docker containers, we will automatically be using the latest version simply by restarting the docker containers if we are using `image: chainsafe/lodestar:latest` in our `docker-compose.yml` file (which we are in this guide).

```sh
docker restart lodestar_beacon lodestar_validator
```

To check your docker compose files,&#x20;

{% tabs %}
{% tab title="beacon" %}
```
cd ~/lodestar_beacon
sudo nano docker-compose.yml
```
{% endtab %}

{% tab title="validator" %}
```
cd ~/lodestar_validator
sudo nano docker-compose.yml
```
{% endtab %}
{% endtabs %}

## Pruning Lodestar

Consensus clients take up a small amount of disk space when compared to execution clients. However, you can still free up \~200GB by pruning it if your validator node has been running for a while.

To prune consensus clients, simply delete the existing database and restart the beacon service with `checkpoint sync` enabled.&#x20;

```sh
cd ~/lodestar_beacon
docker compose down
sudo rm -r /var/lib/lodestar_beacon/*
docker compose up -d
```

Monitor logs for errors.

```sh
docker logs lodestar_beacon -f
```
