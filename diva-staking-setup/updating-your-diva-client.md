# Updating your Diva client

## Update automatically

**Note:** This is only available for those with the latest versions of the docker compose for diva-alpha-net.

Run the installation script by executing:

```
cd ~/diva-alpha-net
./run.sh
```

Inside the `diva-alpha-net` folder, and select option `2. Update Diva`:

<figure><img src="../.gitbook/assets/image (26).png" alt="" width="318"><figcaption></figcaption></figure>

## Update manually

### 1. Stop the current Diva client

Navigate to the Diva directory containing the docker-compose.yml file and bring the service down.

```sh
cd diva-alpha-net
docker compose down
```

### 2. Backup the current Diva directory

Move all contents in the original Diva directory into a new directory `diva-alpha-net-bak`.

```sh
mv ~/diva-alpha-net ~/diva-alpha-net-bak
```

### 3. Download the new version&#x20;

Clone the latest version of the diva-alpha-net git repository into the original parent folder on your system.

```sh
git clone https://github.com/shamirlabs/diva-alpha-net ~/diva-alpha-net
```

### 4. Create a new `.env` file

```sh
cp ~/diva-alpha-net/.env.example ~/diva-alpha-net/.env 
```

### 5. Edit the new `.env` file

Open up the old `.env` file,

```sh
sudo nano ~/diva-alpha-net-bak/.env
```

and retrieve the following environment variables...

1. EXECUTION\_CLIENT\_URL
2. CONSENSUS\_CLIENT\_URL
3. DIVA\_API\_KEY
4. DIVA\_VAULT\_PASSWORD
5. TESTNET\_USERNAME

and enter them into the corresponding variables in your new `.env` file.

```sh
sudo nano ~/diva-alpha-net/.env
```

### 6. Migrate the data

Copy the `.diva` folder from the `~/diva-alpha-net-bak` folder to the new folder `~/diva-alpha-net`:

```sh
cp -r ~/diva-alpha-net-bak/.diva ~/diva-alpha-net/.diva
```

### 7. Preparing the docker compose file

Open up the `docker-compose.yml` file.

```sh
cd diva-alpha-net
sudo nano docker-compose.yml
```

Amend the `ports:` section of the `grafana` service to `"3001:3000".` This is so that the Grafana service running on docker does not clash with your native Grafana service.

```
# Metrics
  grafana:
    image: grafana/grafana:10.2.5
    user: root
    container_name: grafana
    profiles:
      - metrics
    hostname: grafana
    restart: unless-stopped
    ports:
      - "3001:3000"
    volumes:
      - ${DIVA_DATA_FOLDER:-.}/grafana/config:/etc/grafana/provisioning
      - ${DIVA_DATA_FOLDER:-.}/grafana/data:/var/lib/grafana
      - ${DIVA_DATA_FOLDER:-.}/grafana/config/grafana.ini:/etc/grafana/grafana.ini
```

You will then be able to run both Grafana services without conflicts. Access each of the dashboards via:&#x20;

1. Native Grafana: \<IP\_address:3000>
2. Docker Grafana: \<IP\_address:3001>&#x20;

### 8. Prepare the Lodestar docker compose file

{% hint style="info" %}
Skip this Step 8 for the Default (All-in-one) method.
{% endhint %}

Open up the `docker-compose-lodestar-vc.yml` file.

```sh
sudo nano ~/diva-alpha-net/docker-compose-lodestar-vc.yml
```

Append the following contents in this file.

```
# Monitoring configuration
  prometheus:
    extends:
      file: docker-compose.yml
      service: prometheus

  node-exporter:
    extends:
      file: docker-compose.yml
      service: node-exporter

  grafana:
    extends:
      file: docker-compose.yml
      service: grafana
```

### 9. Remove previous containers

**Note:** You must stop and delete all Diva containers before starting the services again.

So first, make sure all Diva containers are stopped and removed identify their container IDs. &#x20;

```sh
cd ~/diva-alpha-net
docker compose down
docker ps -a
```

**Expected output:** You should see an empty list.&#x20;

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

If you still see a list of Diva related containers like below,

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

then you can remove all Diva containers listed using one of the following methods:

* One by one using the`CONTAINER ID` if you have other non-Diva docker containers running

```sh
docker stop <CONTAINER ID>
docker rm <CONTAINER ID>
```

* All at once if you only have Diva containers running

```sh
docker rm -f $(docker ps -a -q)
```

### 9. Run the new Diva containers

<pre class="language-sh"><code class="lang-sh">cd ~/diva-alpha-net
<strong># Choose one of the following to run according to the setup method you are on 
</strong>docker compose -f up -d #For Default method
docker compose -f docker-compose-lodestar-vc.yml up -d #For Experimental method
</code></pre>

Monitor the logs to make sure there are no errors.

```sh
docker logs diva -f
```

**Expected output:** There are 3 things to look out for - "connected to execution client", "consensus client available", and "running diva client".

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>
