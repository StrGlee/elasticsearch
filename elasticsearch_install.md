# elasticsearch install
ubuntu14.04   
java8   
elasticsearch2.3.1   
ik-analyzer
## Step 1.Installing Java
```
sudo apt-get install python-software-properties

sudo add-apt-repository ppa:webupd8team/java

sudo apt-get update

sudo apt-get install oracle-java8-installer
```
## Step 2.Downloading and Installing Elasticsearch
```
wget https://download.elastic.co/elasticsearch/elasticsearch/elasticsearch-2.3.3.deb

sudo dpkg -i elasticsearch-1.7.2.deb
```
## Step 3.Configuring Elastic
```
sudo nano /etc/elasticsearch/elasticsearch.yml

...
node.name: "My First Node"
cluster.name: mycluster1
...
network.bind_host: localhost
...
node.master: false
node.data: false
....
index.number_of_shards: 1
index.number_of_replicas: 0
....
path.data: /media/different_media
....
action.destructive_requires_name: false
...

sudo service elasticsearch start
```
