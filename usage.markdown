---
layout: page
title: Usage
permalink: /usage
nav_order: 3
---


# Elasticsearch

[List of Elasticsearch REST APIs](https://www.elastic.co/guide/en/elasticsearch/reference/current/rest-apis.html)

As said before elasticsearch provide an extensive RESTful APIs we gonna see some examples:

To perform an API request we can use different ways:

- Kibana console section
- cURL requests
- Any API platform(Post man...)

## Index APIs

As its name implies, these API calls can be used to query indexed data for specific information.

```
# Creates a new index.

GET /PUT /my-index-000001
```


![index](https://i.ibb.co/1XC6HVf/Screen-Shot-2022-07-16-at-7-29-15-PM.png)

```
# Returns information about one or more indices. For data streams, the API returns information about the stream’s backing indices.

GET /my-index-000001
```


![index](https://i.ibb.co/PzhM5H4/Screen-Shot-2022-07-16-at-7-23-49-PM.png)


## Search Api

Search APIs are used to search and aggregate data stored in Elasticsearch indices and data streams.

```
# Returns search hits that match the query defined in the request.

GET /my-index-000001/_search

```

![search](https://i.ibb.co/wY7TVh8/Screen-Shot-2022-07-16-at-7-35-24-PM.png)


---

# Kibana

## Visualize data

With the broad variety of visualization styles, Kibana allows you to create a visualization of your data in the Elasticsearch indices.


![visualize](https://i.ibb.co/7nBXxSf/Screen-Shot-2022-07-16-at-7-43-57-PM.png)

## Dashboards

Dashboards in Kibana let you rapidly create views that pull together charts, maps, and filters to display the full picture of your Elasticsearch data.

![dashboads](https://i.ibb.co/NT9FS6L/Screen-Shot-2022-07-16-at-7-47-06-PM.png)



![edit_dashboard](https://i.ibb.co/Br3mM6W/Screen-Shot-2022-07-16-at-7-47-23-PM.png)

## Monitoring

We can use metricbeat to collect logs and metrics of our stack.

The monitoring page of Kibana is usefull in multiple ways:

- You can visualize the data across Elastic Stack as it includes options to monitor the performance data for Elasticsearch, Kibana, Logstash as well as Beats in real-time
- You can also analyze the past performance of these products

![Stack monitoring section](https://i.ibb.co/x7brLHx/Screen-Shot-2022-07-16-at-7-50-42-PM.png)

## Security features SIEM

Elastic Security combines SIEM threat detection features with endpoint prevention and response capabilities in one solution. These analytical and protection capabilities, leveraged by the speed and extensibility of Elasticsearch, enable analysts to defend their organization from threats before damage and loss occur.

Elastic Security provides the following security benefits and capabilities:

- A detection engine to identify attacks and system misconfigurations
- A workspace for event triage and investigations
- Interactive visualizations to investigate process relationships
- Inbuilt case management with automated actions
- Detection of signatureless attacks with prebuilt machine learning anomaly jobs and detection rules

![Security](https://i.ibb.co/wQ03hf6/Screen-Shot-2022-07-16-at-7-57-18-PM.png)

---

# Logstash

## Parsing log using logstash

Sometimes there is a perfect filter that can be used to parse your data, e.g. the json filter in case your logs are in JSON format. A lot of the time we do however need to parse logs in different types of text formats. The example we will use is a line of sysmon logs that looks as follows:

```
<34>1 2003-10-11T22:14:15.003Z mymachine.example.com su - ID47 - BOM'su root' failed for lonvick on /dev/pts/8
```

