---
layout: page
title: Installation
permalink: /installation/
nav_order: 2
---

***All the configurations in this documantation are for version 8.3.1 of the stack.***

***All the configuration files are included in the main branch of this repo.***

# Elasticsearch installation and configuration on Linux

To download Elasticsearch archive we can use wget or curl:

```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.3.1-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.3.1-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-8.3.1-linux-x86_64.tar.gz.sha512 
tar -xzf elasticsearch-8.3.1-linux-x86_64.tar.gz
cd elasticsearch-8.3.1/
```

Your main config file is located at elasticsearch-8.3.1/config/elasticsearch.yml

*If you installed elasticsearch using apt or dpkg your config file witll be located at /etc/elasticsearch/elasticsearch.yml*

The archive contain a great configuration sample, it is minimal and contain all the configuration to start.

Let's try to understand the most important ones.

```

# ----------------------------------- Cluster -----------------------------------


#The cluster name is straight forward. it is the name of your cluster.

cluster.name: "myfirst-cluster"


# ------------------------------------ Node ------------------------------------

# Use a descriptive name for the node:

node.name: "master-node"

# ----------------------------------- Paths ------------------------------------

# Path to directory where to store the data, Or you can leave it and elasticsearch will store the data at the default location with is elasticsearch-8.3.1/data/

#path.data: /path/to/data

# Path to log files. Same thing we can leave it and the default log location will be elasticsearch-8.3.1/logs/

#path.logs: /path/to/log

#Note: If you are running multple nodes from the same directory you may specify each node log and data location to avoid conflictions.

# ---------------------------------- Network -----------------------------------

# By default Elasticsearch is only accessible on localhost. Set a different
# address here to expose this node on the network:

network.host: 0.0.0.0

# By default Elasticsearch listens for HTTP traffic on the first free port it
# finds starting at 9200. Set a specific HTTP port here:

http.port: 9200

# --------------------------------- Discovery ----------------------------------
#
# Discovery seed hosts are used when your nodes are hosted in diffrent hosts. If you want to run multiple nodes in the same host leave it disabled and the default seed_hosts will be set to 127.0.0.1.

#discovery.seed_hosts: ["host1", "host2"]

# If you are trying to run a single-node set the discovery type to single-node

discovery.type: single-node

# Bootstrap the cluster using an initial set of master-eligible nodes:

#cluster.initial_master_nodes: ["node-1", "node-2"]

```

After configuring elasticsearch we can start it:

```
./bin/elasticsearch
```



![elasticsearch](https://i.ibb.co/dKsWMJW/Screen-Shot-2022-07-15-at-4-54-21-PM.png)

As you can see we started elasticsearch succesfuly and it provided us with the password for elastic user and two enrollment tokens we can use them for enrolling kibana or other nodes to out cluster. Elasticsearch also provides multiple binaries to reset passwords or create enrollment tokens.

---

# Running multple nodes on the same host

To run multiple nodes in the same host we need to reconfigure our master-node.

*Note: To not exceed the memory limit we can set the ES_JAVA_OPTS envirment variable and specify the min and max memory usage*

```
export ES_JAVA_OPTS="-Xms2g -Xmx2g"
```

For the master node we gonna use the same config as before, and change these parameters:

```

# Set the discovery to multiple nodes

discovery.type: multi-node

# specify the data path

path.data: data-master-node

# specify the logs path

path.logs: logs-master-node

```

Before running the master node we gonna generate out ssl certs to enable secure connection between nodes


```
# lets generate our own CA

./bin/elasticsearch-certutil ca --pem

# after that lets generate out cert and sign it using the CA

./bin/elasticsearch-certutil cert --pem --ca-cert config/certs/ca/ca.crt --ca-key config/certs/ca/ca.key

```

After generating the certificate we can configure ssl and tls in out elasticsearch.yml

```
xpack.security.enabled: true
xpack.security.http.ssl.enabled: true
xpack.security.http.ssl.key: certs/instance/instance.key
xpack.security.http.ssl.certificate: certs/instance/instance.crt
xpack.security.http.ssl.certificate_authorities: certs/ca/ca.crt
xpack.security.http.ssl.verification_mode: certificate
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.key: certs/instance/instance.key
xpack.security.transport.ssl.certificate: certs/instance/instance.crt
xpack.security.transport.ssl.certificate_authorities: certs/ca/ca.crt
xpack.security.transport.ssl.verification_mode: certificate
```

After that we can run the master node as before:

```
./bin/elasticsearch
```

For the second node we gonna change these configs:

```

# change node name

node.name: "second-node"

# change data path

path.data: data-second-node

# change logs path

path.logs: logs-second-node

# set ssl ca to enable secure connection between nodes

xpack.security.enabled: true
xpack.security.http.ssl.enabled: true
xpack.security.http.ssl.key: certs/instance/instance.key
xpack.security.http.ssl.certificate: certs/instance/instance.crt
xpack.security.http.ssl.certificate_authorities: certs/ca/ca.crt
xpack.security.http.ssl.verification_mode: certificate
xpack.security.transport.ssl.enabled: true
xpack.security.transport.ssl.key: certs/instance/instance.key
xpack.security.transport.ssl.certificate: certs/instance/instance.crt
xpack.security.transport.ssl.certificate_authorities: certs/ca/ca.crt
xpack.security.transport.ssl.verification_mode: certificate


```

Before running the second node we need to generate the enrollment token

```
./bin/elasticsearch-create-enrollment-token -s node
```

-s flag stand for scope you can either generate an enrollment token for kibana or for another node

now lets run the second node

```

./bin/elasticsearch --enrollment-token eyJ2ZXIiOiI4LjMuMSIsImFkciI6WyIxMC4xODIuMC4xMTo5MjAwIl0sImZnciI6IjYzMmE0NjIxZTYyYTE0ODAwNDkwYzk0MGMyOGZlNGUwZDdlOWY0YTk4NzQ1NDY1YjNkNGM5ODhkYzdhZGY4NjQiLCJrZXkiOiIzM0stQW9JQmRsWWMxeGxOaUNpYjpjUFlMd01wY1E4T010LVAyLWt0MmlBIn0=

```

![master-node](https://i.ibb.co/RCZL9PJ/Screen-Shot-2022-07-15-at-6-17-57-PM.png)


![second-node](https://i.ibb.co/Bym1CRm/Screen-Shot-2022-07-15-at-6-17-48-PM.png)

We can see that both nodes are running and the second node succesfuly enrolled into out cluster.

To run elasticsearch as daemon you can either use -d flag or install elasticsearch using apt.

```
./bin/elasticsearch -d
```

---

# Installing Logstash

Download logstash archive:

```
wget https://artifacts.elastic.co/downloads/logstash/logstash-8.3.1-linux-x86_64.tar.gz
tar -xzf logstash-8.3.1-linux-x86_64.tar.gz
cd logstash-8.3.1-linux-x86_64
```

The configuration file for logstash is located at logstash-8.3.1-linux-x86_64/config/logstash.yml

Let's configure logstash

```
# Changing host to be able to access logstash externaly

http.host: "0.0.0.0"
http.port: 9600

# The pipeline.workers setting determines how many threads to run for filter and output processing.

pipeline:
  workers: 2
node:
  name: "master-node"

```

We can configure logstash input and output pipelines using logstash.conf file.

We gonna use a CSV file for testing

```

input {
    file { 
            path => "/root/logstash-8.3.1/config/Games.csv"
            start_position => ["beginning"]
            sincedb_path => "NULL"
    }
}
filter
{
    csv
        {
            separator => ","
            columns => [ "Name", "Sales", "Series", "Release", "Genre" , "Developer", "Publisher" ]
        }
}
output
{

    elasticsearch
        {
            hosts => ["https://localhost:9200"]
            
            # Specify the ca cert to trust elasticsearch cert

            cacert => "/root/config/certs/ca/ca.crt"

            # Specify index name

            index => "games"
            document_type => "sold_games"

            # Specify the credential of the user used to authenticat to elasticsearch

            user => "elastic"
            password => "elasticpassword"

    }

# stdout is a great way to debug the ouput that are gonna be sent to elasticsearch

stdout {}
}

```

After that lets start logstash

```
./bin/logstash -f config/logstash.conf
```

