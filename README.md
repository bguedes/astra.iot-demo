**Pre-requisites**

***Java***  

https://www.java.com/en/download/help/download_options.html

For linux:

https://linuxhint.com/install_java_linux_mint/

***Docker***

You need to install Docker and Docker-compose on your computer:

https://www.docker.com/products/docker-desktop

For Linux :

```
#docker setup
echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable" | sudo tee /etc/apt/sources.list.d/docker.list
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io pigz

#execution permission
sudo usermod -aG docker $USER

#docker compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.26.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

***Datastax ASTRA***

You need to have an Astra database setup:

database = demo

username = datastax

password = datastax

***Jq for Json viewing***

Please install Jq in your computer :

https://stedolan.github.io/jq/download/

For Linux Mint 20:

```
sudo rm /etc/apt/preferences.d/nosnap.pref
sudo apt update

sudo apt install snapd
sudo snap install jq
```

**Setup the demo with Docker**

To setup the demo environment use the following command:

```
docker-compose up -d
```

To check that everything is launched, check with this command:

```
docker ps
```

You will get the docker containers created and running:

```
CONTAINER ID   IMAGE                                              COMMAND                  CREATED        STATUS        PORTS                                                                                                                                                                             NAMES
2581bd02f86b   confluentinc/cp-enterprise-control-center:latest   "/etc/confluent/dock…"   13 hours ago   Up 13 hours   0.0.0.0:9021->9021/tcp                                                                                                                                                            control-center
f7774d7c9ca7   confluentinc/cp-kafka-rest:latest                  "/etc/confluent/dock…"   13 hours ago   Up 13 hours   0.0.0.0:8082->8082/tcp                                                                                                                                                            rest-proxy
264b5b248df5   datastax-connect:latest                            "/etc/confluent/dock…"   13 hours ago   Up 8 hours    0.0.0.0:8083->8083/tcp, 9092/tcp                                                                                                                                                  datastax-connect
ea9547b20636   confluentinc/cp-schema-registry:latest             "/etc/confluent/dock…"   13 hours ago   Up 13 hours   0.0.0.0:8081->8081/tcp                                                                                                                                                            schema-registry
c04068b49e03   confluentinc/cp-kafka:latest                       "/etc/confluent/dock…"   13 hours ago   Up 13 hours   0.0.0.0:9092->9092/tcp                                                                                                                                                            kafka-broker
6a46b96182d5   confluentinc/cp-zookeeper:latest                   "/etc/confluent/dock…"   13 hours ago   Up 13 hours   2181/tcp, 2888/tcp, 3888/tcp                                                                                                                                                      zookeeper
```

**Create the topic**

Go to the Confluent Control Center:

http://localhost:9021/

On the Menu choose Topics -> +Add a Topic

Topic name = iot-data-event

Create

**Setup the Datstax Kafka Connector configuration**

Copy the provided configuration on the Kafka producer container:

```
docker cp secure-connect-demo.zip datastax-connect:/home/
```

Check if the connector is ready:

```
curl -X POST -H "Content-Type: application/json" -d @connector-config-astra.json "http://localhost:8083/connectors"
```

You must see everything in RUNNING mode:

```
curl -X GET "http://127.0.0.1:8083/connectors/demo-iot-astra/status" | jq
```

```
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                               Dload  Upload   Total   Spent    Left  Speed
100   747  100   747    0     0   145k      0 --:--:-- --:--:-- --:--:--  145k
{
"name": "demo-iot-astra",
"connector": {
  "state": "RUNNING",
  "worker_id": "datastax-connect:8083"
},
"tasks": [
  {
    "id": 0,
    "state": "RUNNING",
    "worker_id": "datastax-connect:8083"
  },
  {
    "id": 1,
    "state": "RUNNING",
    "worker_id": "datastax-connect:8083"
  },
  {
    "id": 2,
    "state": "RUNNING",
    "worker_id": "datastax-connect:8083"
  },
  {
    "id": 3,
    "state": "RUNNING",
    "worker_id": "datastax-connect:8083"
  },
  {
    "id": 4,
    "state": "RUNNING",
    "worker_id": "datastax-connect:8083"
  },
  {
    "id": 5,
    "state": "RUNNING",
    "worker_id": "datastax-connect:8083"
  },
  {
    "id": 6,
    "state": "RUNNING",
    "worker_id": "datastax-connect:8083"
  },
  {
    "id": 7,
    "state": "RUNNING",
    "worker_id": "datastax-connect:8083"
  },
  {
    "id": 8,
    "state": "RUNNING",
    "worker_id": "datastax-connect:8083"
  },
  {
    "id": 9,
    "state": "RUNNING",
    "worker_id": "datastax-connect:8083"
  }
],
"type": "sink"
}
```

**Import Astra Notebook**

Import iot-demo_studio_notebook.tar in the Astra Studio

**Launch the data producer**

```
java -jar iot-kafka-producer-1.0.0.jar
```
