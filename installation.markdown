---
layout: page
title: Installation
permalink: /installation/
nav_order: 2
---

***All the configurations in this documantation are for version 8.3.1 of the stack.***

***All the configuration files are included in the main branch of this repo.***

*Note: all the elk stack software we installed are the same version, Be carefull with the version you choose to install because elk stack does not do well with back compatibility.*

[Support Matrix for all Elastic softwares](https://www.elastic.co/support/matrix)

# Elasticsearch installation and configuration on Linux

To download Elasticsearch archive we can use wget or curl:

```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.3.1-linux-x86_64.tar.gz
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

[Resource#1](https://www.elastic.co/guide/en/cloud-enterprise/2.0/ece-heap.html#ece_elasticsearch_clusters_and_jvm_heap_size)

For Elasticsearch clusters, ECE gives 50% of the available memory to the JVM heap used by Elasticsearch, while leaving the other 50% for the operating system. This memory won’t go unused, as Lucene is designed to leverage the underlying OS for caching in-memory data structures, meaning that Lucene will happily gobble up whatever is left over. The ideal heap size is somewhere below 32 GB, as heap sizes above 32 GB become less efficient.

What these recommendations mean is that on a 64 GB cluster, we dedicate 32 GB to the Elasticsearch heap and 32 GB to the operating system in the container that hosts your cluster. If you provision a 128 GB cluster, we create two 64 GB nodes, each node with 32 GB reserved for the Elasticsearch heap and 32 GB reserved for the operating system.

To not exceed the memory limit we can set the ES_JAVA_OPTS envirment variable and specify the min and max memory usage

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

All the data is stored on elasticsearch and we can comfirm that after installing kibana.

---

# Kibana installation

As elasticsearch and logstash we can download the archive containing the binaries directly or we can use any package manager (Depending on your distro of choice) to install kibana.

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-8.3.1-linux-x86_64.tar.gz
tar -xzf kibana-8.3.1-linux-x86_64.tar.gz
cd kibana-8.3.1/
```

This is the important configs we need to start kibana:

```
# Changing host to be able to access kibana externaly

server.host: "0.0.0.0"

# Provide the host and port for your elasticsearch cluster

elasticsearch.hosts: ["https://localhost:9200"]

# Provide the crendentials to your kibana_system user, you can reset the password for this user using elasticsearch-reset-password binary.

elasticsearch.username: "kibana_system"
elasticsearch.password: kibana_password

# Provide kibana with the your CA certificate so the connection can be verified between kibana and elasticsearch

elasticsearch.ssl.verificationMode: certificate

elasticsearch.ssl.certificateAuthorities: [ "/etc/kibana/config/certs/ca/ca.crt" ]
```

To run kibana:

```
./bin/kibana
```

After running kibana we can access it at http://localhost:5601

![Kibana](https://i.ibb.co/VSWhMjZ/Screen-Shot-2022-07-16-at-5-32-15-PM.png)

Lets confirm that logstash data is stored by checking Analytics->Discovery

First we need to cearte a data view for the index we used in our data

![create_data_view#1](https://i.ibb.co/8cyMLFN/Screen-Shot-2022-07-16-at-5-34-59-PM.png)

![data](https://i.ibb.co/vYftmbP/Screen-Shot-2022-07-16-at-5-35-14-PM.png)

---

# Monitor a windows machine using sysmon logs

- Download Sysmon

[https://download.sysinternals.com/files/SysinternalsSuite.zip](https://download.sysinternals.com/files/SysinternalsSuite.zip)

- We are going to use existing configuration by olafhartong

[https://github.com/olafhartong/sysmon-modular](https://github.com/olafhartong/sysmon-modular)

[https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml](https://raw.githubusercontent.com/olafhartong/sysmon-modular/master/sysmonconfig.xml)

- Install by opening up a command prompt as administrator

```
# 32 bit

sysmon.exe –accepteula –i PATH/TO/sysmonconfig.xml

# 64 bit

sysmon64.exe –accepteula –i PATH/TO/sysmonconfig.xml
```

- Download Winlogbeat

[https://artifacts.elastic.co/downloads/beats/winlogbeat/winlogbeat-8.3.2-windows-x86_64.zip](https://artifacts.elastic.co/downloads/beats/winlogbeat/winlogbeat-8.3.2-windows-x86_64.zip)

- Extract the contents into C:\Program Files.

- Rename the winlogbeat-<version> directory to Winlogbeat

- Using PowerShell prompt as an Administrator

```
# Access winlogbeat directory

cd 'C:\Program Files\Winlogbeat'

# Install winlogbeat service

.\install-service-winlogbeat.ps1

```

- Configure winlogbeat to connect ro the elastic stack

winlogbeat.yml

```
output.elasticsearch:
  hosts: ["https://YOUR-HOST:9200"]
  username: "winlogbeat_internal"
  password: "YOUR_PASSWORD"
```

```
# provide kibana host credential to setup dashboards

setup.kibana:
    host: "mykibanahost:5601" 
    username: "my_kibana_user"  
    password: "{pwd}"

```

- Setup assets

```
.\winlogbeat.exe setup -e
```

- Start Winlogbeat

```
Start-Service winlogbeat
```

![winlogbeat](https://i.ibb.co/Cb3WHM0/Screen-Shot-2022-07-16-at-9-16-20-PM.png)


You can check the Discover section to confirm data is shipping succesfully.

![discover](https://i.ibb.co/w7sBnDX/Screen-Shot-2022-07-16-at-9-18-02-PM.png)

---

# Fleet server

[Resource#1](https://www.elastic.co/guide/en/fleet/current/fleet-server.html)

[Resource#2](https://www.elastic.co/guide/en/fleet/current/add-a-fleet-server.html)

Fleet Server is a component of the Elastic Stack used to centrally manage Elastic Agents. It’s launched as part of an Elastic Agent on a host intended to act as a server. One Fleet Server process can support many Elastic Agent connections, and serves as a control plane for updating agent policies, collecting status information, and coordinating actions across Elastic Agents.

To deploy a self-managed Fleet Server, you install an Elastic Agent and enroll it in an agent policy containing the Fleet Server integration.

First we need to creat Fleet server policy, Head to **Management > Fleet > Settings** and specify your host IP adress. After that you be provided with the commands needed to start your fleet server. The commands contain commands to download elastic agent. Be careful with the version to insure compatibility. If you enabled security on your cluster you need to specify the path for your CA cert so the ssl can be verified, You can use the flag ***--fleet-server-es-ca=*** to provide an absolute path to your CA cert.

![Fleet server](https://www.elastic.co/guide/en/fleet/current/images/add-fleet-server.png)

---

# Monitor your stack with Metricbeat


*Metricbeat is a lightweight agent that can be installed on target servers to periodically collect metric data from your target servers, this could be operating system metrics such as CPU or memory or data related to services running on the server. It can also be used to monitor other beats and ELK stack itself.*

![metricbeat](https://miro.medium.com/max/1056/0*6axE2zHZt_fsLFFx.png)

First we need to enable monitoring on elastisearch cluster:

Add this to your elasticsearch.yml

```
xpack.monitoring.collection.enabled: true
xpack.monitoring.elasticsearch.collection.enabled: true

```

After saving the config you have to restart elasticsearch.

Now download metricbeat:

```
wget https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-8.3.1-linux-x86_64.tar.gz
tar -xzf metricbeat-8.3.1-linux-x86_64.tar.gz
cd metricbeat-8.3.1/
```

Your main config file will be config/metricbeat.yml. You can enable modules and configure each module directly from ***modules.d/nameofmodule.yml***, Or you can configure modules directly on your metricbeat.yml.

metricbeat.yml

```

# Provide elasticsearch host and credentials so metricbeat can save data in your cluster. p.s don't forget CA.crt so metricbeat can verify the ssl connection.

output.elasticsearch:
  hosts: ["https://localhost:9200"]
  username: "elastic"
  password: 'elastic_password'
  ssl:
    enabled: true
    certificate_authorities: "/root/config/certs/ca/ca.crt"

# We gonna monitor our whole stack so we are going to enable modules for elasticsearch, kibana, logstash

metricbeat.modules:
- module: elasticsearch
  xpack.enabled: true
  period: 10s
  scope: cluster
  hosts: ["https://localhost:9200"]
  username: "elastic"
  password: 'elastic_password'
  ssl:
    enabled: true
    certificate_authorities: "/root/config/certs/ca/ca.crt"

- module: kibana
  metricsets: ["status"]
  period: 10s
  xpack.enabled: true
  hosts: ["http://localhost:5601"]
  username: "elastic"
  password: 'elastic_password'

- module: logstash
  metricsets: ["node", "node_stats"]
  period: 10s
  hosts: ["localhost:9600"]
  username: "elastic"
  password: 'elastic_password'
  xpack.enabled: true

# Provide your kibana credentials so the dashboards can be setted up.

setup.kibana:
    host: "kibana:5601"
    protocol: "http"
    username: "elastic"
    password: '${ELASTIC_PASSWORD}'


```

Now you can start Metricbeat setup:

```
./metricbeat setup -e
```

and start metricbeat to start collecting metrics:

```
./metricbeat -e
```

If everything worked right, you need to find metricbeat dashboard in kibana, and monitor section start showing metrics.

![metricbeat dashboards](https://i.ibb.co/R6517sP/Screen-Shot-2022-07-17-at-11-06-42-PM.png)

![stack monitoring](https://i.ibb.co/x7brLHx/Screen-Shot-2022-07-16-at-7-50-42-PM.png)