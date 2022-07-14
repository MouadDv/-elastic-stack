---
layout: page
title: Resources
permalink: /Resources/
nav_order: 100
---

## What is a SIEM

[https://www.ibm.com/topics/siem](https://www.ibm.com/topics/siem)


SIEM tools are an important part of the data security ecosystem: they aggregate data from multiple systems and analyze that data to catch abnormal behavior or potential cyberattacks. SIEM tools provide a central place to collect events and alerts – but can be expensive, resource intensive, and customers report that it is often difficult to resolve problems with SIEM data.

![what is SIEM](https://info.varonis.com/hs-fs/hubfs/Imported_Blog_Media/siem-process-2.png?width=2882&height=3015&name=siem-process-2.png)

---

## Elk stack as a SIEM

[https://www.elastic.co/security/siem](https://www.elastic.co/security/siem)

---

## Elk stack architecture

[https://www.softwaretestinghelp.com/elk-stack-tutorial/#:~:text=A%20simple%20ELK%20stack%20architecture,stored%2C%20searched%2C%20and%20indexed.](https://www.softwaretestinghelp.com/elk-stack-tutorial/#:~:text=A%20simple%20ELK%20stack%20architecture,stored%2C%20searched%2C%20and%20indexed.)


[https://medium.com/dataseries/elk-stack-architecture-deep-dive-41168732f0e3](https://medium.com/dataseries/elk-stack-architecture-deep-dive-41168732f0e3)


![elk](https://miro.medium.com/max/1400/0*8DZtMKXzbsf5QBFB)

![elk](https://miro.medium.com/max/1400/0*e-rMqNRNgTxkGdAE)

- Built on top of Apache Lucene (it itself is a powerful search engine, all the power of Lucene easily expose to simple configuration and plugins, it handles human language synonyms, typo mistake)
- NoSQL Datastore (like MongoDB)
- Schema-free (no need to define a schema before adding data into Elasticsearch)
- JSON Document (data in Elasticsearch is stored in form of JSON document)
- RESTful APIs (Elasticsearch has powerful RESTful APIs that you can interact with cluster)
- Node
- Cluster

---

## scaling

### Vertical Scaling

![https://webimages.mongodb.com/_com_assets/cms/kkr1adoqfib7hc8jb-vertical-scaling.jpg?auto=format%2Ccompress](https://webimages.mongodb.com/_com_assets/cms/kkr1adoqfib7hc8jb-vertical-scaling.jpg?auto=format%2Ccompress)

***Vertical scaling*** refers to increasing the processing power of a single server or cluster. Both relational and non-relational databases can scale up, but eventually, there will be a limit in terms of maximum processing power and throughput. Additionally, there are increased costs with scaling up to high-performing hardware, as costs do not scale linearly.

### Horizontal Scaling

![https://webimages.mongodb.com/_com_assets/cms/kkr1awf03wxyu4455-horizontal-scaling.jpg?auto=format%2Ccompress](https://webimages.mongodb.com/_com_assets/cms/kkr1awf03wxyu4455-horizontal-scaling.jpg?auto=format%2Ccompress)

***Horizontal scaling***, also known as scale-out, refers to bringing on additional nodes to share the load. This is difficult with relational databases due to the difficulty in spreading out related data across nodes. With non-relational databases, this is made simpler since collections are self-contained and not coupled relationally. This allows them to be distributed across nodes more simply, as queries do not have to “join” them together across nodes.

---

## Elasticsearch scalability and resilience

Scalability and resilience: clusters, nodes, and shardsedit
Elasticsearch is built to be always available and to scale with your needs. It does this by being distributed by nature. You can add servers (nodes) to a cluster to increase capacity and Elasticsearch automatically distributes your data and query load across all of the available nodes. No need to overhaul your application, Elasticsearch knows how to balance multi-node clusters to provide scale and high availability. The more nodes, the merrier.

How does this work? Under the covers, an Elasticsearch index is really just a logical grouping of one or more physical shards, where each shard is actually a self-contained index. By distributing the documents in an index across multiple shards, and distributing those shards across multiple nodes, Elasticsearch can ensure redundancy, which both protects against hardware failures and increases query capacity as nodes are added to a cluster. As the cluster grows (or shrinks), Elasticsearch automatically migrates shards to rebalance the cluster.

There are two types of shards: primaries and replicas. Each document in an index belongs to one primary shard. A replica shard is a copy of a primary shard. Replicas provide redundant copies of your data to protect against hardware failure and increase capacity to serve read requests like searching or retrieving a document.

The number of primary shards in an index is fixed at the time that an index is created, but the number of replica shards can be changed at any time, without interrupting indexing or query operations.

---

## elastic search search language

## jvm heap sizing

## API and RESTful APIs in ELasticsearch

## ETL

## Multitentants

## secutity features SIEM

## data tiers in elasticsearch

## life cycle managment

## xdr elasticsearch






[]()