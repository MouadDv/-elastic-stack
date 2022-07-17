---
layout: page
title: Overview
permalink: /Overview/
nav_order: 1
---

So, what is the ELK Stack? "ELK" is the acronym for three open source projects: Elasticsearch, Logstash, and Kibana.

---

![Elasticsearch](https://user.oc-static.com/upload/2017/10/10/15076639807937_Elasticsearch-Logo-Color-V.jpg.png)

# Elasticsearch

Elasticsearch is a search and analytics engine. It is based on Lucene search engine, and it is built with RESTful APIS. It offers simple deployment, maximum reliability, and easy management. It also offers advanced queries to perform detail analysis and stores all the data centrally. It is helpful for executing a quick search of the documents.

## Features of Elasticsearch

- Near real-time searching:

Elasticsearch is a distributed document store. Instead of storing information as rows of columnar data, Elasticsearch stores complex data structures that have been serialized as JSON documents. When you have multiple Elasticsearch nodes in a cluster, stored documents are distributed across the cluster and can be accessed immediately from any node.

When a document is stored, it is indexed and fully searchable in near real-time--within 1 second. Elasticsearch uses a data structure called an inverted index that supports very fast full-text searches. An inverted index lists every unique word that appears in any document and identifies all of the documents each word occurs in.

- High level of customization:

### Managing indexing lifecycle

[Resource#1](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-lifecycle-management.html)

Depending on your need you can customize the indexing lifecycle, per example you can configure elasticsearch to automaticlly create a new indices when an index reaches a certain size.

### Node roles

[Resource#1](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-node.html#node-roles)
[Resource#2](https://www.elastic.co/guide/en/elasticsearch/reference/current/data-tiers.html#data-tiers)

You can assign a role to your nodes, to enssure efficancy out of your cluster. By creating a collection of nodes with the same data role you are creating data tiers that are the defined as follows

- Content tier nodes handle the indexing and query load for content such as a product catalog.
- Hot tier nodes handle the indexing load for time series data such as logs or metrics and hold your most recent, most-frequently-accessed data.
- Warm tier nodes hold time series data that is accessed less-frequently and rarely needs to be updated.
- Cold tier nodes hold time series data that is accessed infrequently and not normally updated. To save space, you can keep fully mounted indices of searchable snapshots on the cold tier. These fully mounted indices eliminate the need for replicas, reducing required disk space by approximately 50% compared to the regular indices.
- Frozen tier nodes hold time series data that is accessed rarely and never updated. The frozen tier stores partially mounted indices of searchable snapshots exclusively. This extends the storage capacity even further — by up to 20 times compared to the warm tier.

### RESTful APIS

[Resource#1](https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html)

Elasticsearch provide us with an extensive RESTful APIs which allow us to integrate, manage and query the indexed data without using an interface.

### Scalability, Resilience and Redundancy

[Resource#1](https://www.elastic.co/guide/en/elasticsearch/reference/current/scalability.html)

Elasticsearch is built to be always available and to scale with your needs. It does this by being distributed by nature. You can add nodes to a cluster to increase capacity and Elasticsearch automatically distributes your data and query load across all of the available nodes. No need to overhaul your application, Elasticsearch knows how to balance multi-node clusters to provide scale and high availability.

By distributing the documents in an index across multiple shards, and distributing those shards across multiple nodes, Elasticsearch can ensure redundancy, which both protects against hardware failures and increases query capacity as nodes are added to a cluster.

---

# Logstash

Logstash is a plugin-based data collection and processing engine. It comes with a wide range of plugins that makes it possible to easily configre it to collect, process and forward data in many different architectures.

Processing is organized into one or more pipelines. In each pipeline, one or more input plugins receive or collect data that is then placed on an internal queue. This is by default small and held in memory, but can be configured to be larger and persisted on disk in order to improve reliability and resiliency.

![logstash](https://images.contentstack.io/v3/assets/bltefdd0b53724fa2ce/blt3959396f94d16f1b/5c304d53e71ce40c6e4ad977/logstash-instance-input-filter-output-plugins.png)

---

# Kibana

Kibana is a data visualization and exploration tool used for log and time-series analytics, application monitoring, and operational intelligence use cases. It offers powerful and easy-to-use features such as histograms, line graphs, pie charts, heat maps, and built-in geospatial support.


![](https://i.ibb.co/pndF6cw/Screen-Shot-2022-07-14-at-4-03-17-PM.png)
---