---
layout: page
title: Installation
permalink: /installation/
nav_order: 2
---

***All the configurations in this documantation are for version 8.3.1 of the stack.***

# Elasticsearch installation and configuration on Linux

To download Elasticsearch archive we can use wget or curl:

```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.3.1-linux-x86_64.tar.gz
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.3.1-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-8.3.1-linux-x86_64.tar.gz.sha512 
tar -xzf elasticsearch-8.3.1-linux-x86_64.tar.gz
cd elasticsearch-8.3.1/
```

