---
title: kafka
description: 
published: true
date: 2020-06-04T13:16:07.397Z
tags: 
---

![Kafka Logo](/uploads/logos/kafka-icon.png "kafka Logo"){.pagelogo}
<!-- TITLE: kafka -->
<!-- SUBTITLE: Confluent's platform for streaming data - the new middleware -->
# References
The Book:
[kafka: The Definitive Guide](/uploads/kafka/confluent-kafka-definitive-guide-complete.pdf "kafka: The Definitive Guide")

Articles:
* https://www.confluent.io/blog/building-a-microservices-ecosystem-with-kafka-streams-and-ksql/ - yet to read
* https://hmh.engineering/experimenting-with-apache-kafka-and-nodejs-5c0604211196 - read, but need to try example
* https://www.confluent.io/blog/iot-with-kafka-connect-mqtt-and-rest-proxy - MQTT and kafta
* https://medium.com/@stephane.maarek/the-kafka-api-battle-producer-vs-consumer-vs-kafka-connect-vs-kafka-streams-vs-ksql-ef584274c1e
* https://www.youtube.com/watch?v=BuE6JvQE_CY - is kafka a database
* https://www.youtube.com/watch?v=IFNs6sKvb8k - Growing an engineering culture with kafka
* https://medium.com/bakdata/queryable-kafka-topics-with-kafka-streams-8d2cca9de33f - Kafta streams key-value store - RESTful endpoint to query

 
# Download
https://kafka.apache.org/downloads

# Overview
kafka is made up of:
* [Brokers](/kafka/brokers) and [Topics](/kafka/topics)
* [CLI](/kafka/cli)
* [Consumers API](/kafka/consumers)
* [Producers API](/kafka/producers)
* [Connect API](/kafka/connect)
* [Streams API](/kafka/streams)
  * KSQL
* [Schema Registry](/kafka/registry)

Messages are typically JSON/XML self describing content, but can be any binary or text format. Ideally, the format of the message should exist alongside the message itself making it self describing. This is why JSON and XML are good with JSON Schemas and XML Schemas. But more and more, folks are using Apache Avro, not just because its an efficient binary format and self describing, but because the data definition schema can be versioned too.

In kafka, all messages are sent to a topic (stream); FIFO. Partitions (shards) can be created on a topic based on a predefined key; each message within the partition is still FIFO. kafta distributively stores all messages, thus providing resilience, and each topic/partition having an `offset` allowing to stops and restarts for consumers with no loss of data.

![kafka-overview.png](/uploads/kafka/kafka-overview.png)

## CLI
Typically kafka is accessed via custom applications using the kafka API. For administrative purposes, kafka has a [cli](/kafka/cli).

### Confluent
Download from: https://docs.confluent.io/current/cli/index.html. Federates control of `kafka`, `zookeeper`, `schema-registry`, `kafka-rest`, `connect` and `ksql-server`.

```
./confluent local [start|stop|destroy]
```

A special case is to start only `kafka` and `zookeeper`:
```
./confluent local start kafka
```

# Docker
https://github.com/simplesteph/kafka-stack-docker-compose

# zookeeper
This an Apache technology for maintaining configration, control and monitoring of distributed services. Zookeeper must be deployed with kafka to control/amange the brokers and to monitor the kafka ecosystem (clusters, topics, partitiions, ...).

# Advertised Host
A broker has a single "Advertised Host" - this can be set to a public DNS/IP address or private:

## Private
Set of private identify, if all clients are within the private network:
![kafka-advertised-host.png](/uploads/kafka/kafka-advertised-host.png)

## Public
Set of private identify, if all clients are within the public network.

# Private and Public
Create a DMZ in which to host your private clients and use the "public" identity.

# CQRS - Command Query Responsibility Separation
An event platform like kafka is perfect for building a CQRS application:
* https://www.confluent.io/blog/event-sourcing-cqrs-stream-processing-apache-kafka-whats-connection
* https://medium.com/@qasimsoomro/building-microservices-using-node-js-with-ddd-cqrs-and-event-sourcing-part-1-of-2-52e0dc3d81df

# Patterns
[kafka patterns](/kafka/patterns)

# Monitoring & Ops
As a Java platform, kafka is exposed via JMX. These can be exported to an ELK service, Datadog, New Relic, Confluence Control Centre, Prometheus/Grafana, ....

https://kafka.apache.org/documentation/#monitoring

## Important Metrics
* Under replicated Partitions - number of partitions having trouble with ISR (in-sync replicas). May indicate high load.
* Request Handlers - utilisation of threads (overall utilisation of a kafka broker)
* Request Timing - how long it is taking to process requests; lower is better (duh).

# Typical Operations
* rolling restarts of brokers
* updating configs
* rebalancing partitions
* increasing replication factor
* adding/replcaing/removing a broker
* upgrading kafka with zero downtime
* maintaining ACLs (and possibly _client_ users)

# Security
kafka supports authentication, authorisations and transport encryption.

## Encryption
TLS - between brokers and clients.  Port 9092 is non-secure. Port 9093 is secure.

There is no TLS between the brokers; ensure good edge security around your cluster, especially with respect to the CLI.

## Authentication
Clients authenticate to the bootstrap broker.

* SSL client certiicates
* SASL:
  * plain - username/password - easy but weak
  * Kerberos - hard to setup; strong and federated
  * SCRAM - effectively username/password - strong and easy to setup

## Authorisation
kafka supports acccess control lists against authenticated users, such subscribe/publish to a given topic.


# Multi-Region Clusters and Replication
kafka clusters can share data; replication between clusters is little more than a producer and consumer, right!

Different tools can be  deployed:
* Mirror Maker (MM) - ships with kafka
* netflix using flink (not open source)
* uber uses [uReplicator](https://eng.uber.com/ureplicator-apache-kafka-replicator/) - improves upon Mirror Maker
* [Comcast](https://github.com/Comcast/MirrorTool-for-Kafka-Connect) open source kafka Connect source
* Confluence has an enterprise (paid) Connect source

When you replicate, it does not preserve offsets; the replicated cluster's offsets are created as new.

https://www.confluent.io/kafka-summit-sf17/multitenant-multicluster-and-hieracrchical-kafka-messaging-service/

# kubernetes
kubernetes is now the industry's de facto app hosting platform. No longer are we virtualising servers to host applications, we simpy package (dockerise) applications and deploy on kubernetes. kubernetes has good scaling and monitoring options (least not Prometheus). So we DIY it and benefit from consolidating on a common platform so the deployment, scaling and monitoring of all applications is consistent.

kafka, even though it requires good (fast) storage and networking, Confluent fully support deployment of kafka on kubernetes, through its `Operator`offering: https://www.confluent.io/confluent-operator.

https://www.confluent.io/blog/apache-kafka-kubernetes-could-you-should-you

_NOTE: k8s cluster to allow for persistent storage and stateful pods._

# To Watch
* Apacke storm - realtime computation built on top of kafka; a network of predefined bots fed by spouts (data source) - not too dissimilar from a neural network
* Apache samza - distributed stream processing events/ETL built on top of kafka
* Apache airflow - authoring, scheduling and monitoring of workflows using Directed Acyclic Graphs (DAGs) - could be used to workflow data streaming through kafka

# Alternatives
https://blog.scottlogic.com/2018/07/06/comparing-streaming-frameworks-pt1.html

* Apache Flink - massively distributed (master/slaves architecture) computation cluster, operating on events and streams. Can use kafka as underlying storage infrastructure, but does not have to.