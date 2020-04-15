---
title: Kafka Topics
description: 
published: true
date: 2020-04-15T05:10:56.733Z
tags: kafka, partitioning, keys, TTL
---

# Kafa Topics
Topic is the most critical entity in kafka. Think of it like a database table without the constraints. Messages are posted to a topic (producers) and distributed via that topic (consumers).

It (the topic) has a name. No limit to the number of topics, or on the name.

## Partitions
A topic must be created with a given number of partitions (_the number of partitions can be changed later_). Partitions are labelled from 0. Each partition is orderted. Each message within a the same parition is given an incremental id, called an offset.

A message is referenced by "topic:partition:offset".

Order of messages is only guaranteed within a partition; not across partition. If you have data which is temporal, then choose a key that ensures the data is written to the same partition for the given time eseries. If all events are time based, choose a temporal database (like [promtetheus](https://en.wikipedia.org/wiki/Time_series_database)) as a consumer and stream from the topic.

### Get it Right First Time
Although you can increase the partition count and replication factor after a topic has been created, it is best to _get it right_ the first time:
* Increasing partition count breaks key order guarantee - for in-flight messages
* INcreasing replication factor puts additional load on your cluster and decreases performance whilst the partitions are replicated; the bigger & higher number of partitions the greater the impact

#### Partitions
Each partition can handle a throughput of a few MB/s - but measure it for your environment.

More partitions mean:
* Better concurrency - throughput
* To run more producers and consumers in a group
  * Size for maximum (peak) throughput
* Leverage more brokers in your cluster
* But:
  * More re-elections to perform for Zookeeper
  * More open files in kafka

Rule of thumb:
* Small cluster (< 6 brokers): 2 x  #brokers
* Big cluster (> 12 brokers); 1 x #brokers

#### Replication Factor
Should be at least 2, usually 3, maximum 4.

Higher replication factor means:
* Better resilience
* But:
  * More replication means higher latency (acks=all)
  * More disk space on brokers - 50% more if replication factor is 3 instead 2)
  
Rule of thumb:
* Set it to 3 (you must have at least three brokers)
* If latency too high, get a better broker (do not compromise on replication)
* Never set to 1 in production

#### Brokers
It is generally accepted that:
* a broker should not hold more than 20000 to 40000 partitions across all topics
* a cluster should have a maximum 200000 partitions across all brokers

This is owing to the volume of partition re-elections required by zookeeper if a broker goes down.

### Keys
Note - keys are not defined against a topic is created (unlike a database table index). A key is against a message; see Brokers.


## TTL
Data within a topic expires after a given duration; the default is one week.


## Immutable
Once data is written to a partition, it can not be updated.

## Naming Convention
Although topics can have any name, it is best to pick a naming convention up front. A dot notation based on business and function (like java packages) is a good choice.