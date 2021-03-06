---
title: Kafka Topics
description: 
published: true
date: 2020-04-19T08:43:44.993Z
tags: kafka, partitioning, keys, TTL
---

# Kafa Topics
Topic is the most critical entity in kafka. Think of it like a database table without the constraints. Messages are posted to a topic (producers) and distributed via that topic (consumers).

It (the topic) has a name. No limit to the number of topics, or on the name.

## Partitions
A topic must be created with a given number of partitions (_the number of partitions can be changed later_). Partitions are labelled from 0. Each partition is orderted. Each message within a the same parition is given an incremental id, called an offset.

A message is referenced by "topic:partition:offset".

Order of messages is only guaranteed within a partition; not across partition. If you have data which is temporal, then choose a key that ensures the data is written to the same partition for the given time eseries. If all events are time based, choose a temporal database (like [promtetheus](https://en.wikipedia.org/wiki/Time_series_database)) as a consumer and stream from the topic.

### Segments
A partition on a single broker is provided through a series of segments; each segment contains consecutive range of offsets. Only one (the latest segment) is ever opened.

![kafka-segments.png](/uploads/kafka/kafka-segments.png)
Each segment file is complemented with two index files:
* An offset to position index; is used to locate individual messages
* A timestamp to offset index; for filtering based on time

Two properties are dedicated to segments:
* `log.seegment.bytes` - maximum size of a single segment in bytes (default 1GB)
* `log.segment.ms` - the max time kafka will wait before committing (closing the active segment) if not full (default 7 days)

The smaller the segment files, the more segment files you will have and log compacttion happens more (think of this like Java garbage collection). The more segment files you have, the larger the number of files kafka has to keep open (because it is able to return data from any segment). Fast streaming data may require you to increase the segment size.

The smaller the segment time, the more segment files and the more frequent log compaction will be.


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

The default is that data is deleted (see log cleanup below).

## Immutable
Once data is written to a partition, it can not be updated.

## Naming Convention
Although topics can have any name, it is best to pick a naming convention up front. A dot notation based on business and function (like java packages) is a good choice.

More depth article: https://riccomini.name/how-paint-bike-shed-kafka-topic-naming-conventions.

## Config
When creating topics, much of the topic's configuration is assumed by default parameters on the broker. Some topic properties have to be set explicitly if required, but all topic properties can be changed.

A complete list of topic configurations can be found at: https://kafka.apache.org/documentation/#topicconfigs.

A topic's config can be changed using the CLI `<kafka>/kafka-configs` (the same tool used to change broker properties too), which uses zookeeper to pass onto all brokers (given the a topic's partitions are spread across all brokers) - to add/delete:
```
<kafka>/kafka-configs.sh --zookeeper localhost:2181 --entity-type topics --entity-name <NAME OF TOPIC> --alter --add-config <config property> --delete-config <config property>
```

Note - to change a config property, use `--add-config`.


## Log Cleanup
All data in kafka has a TTL. 	Deleting old data is called "log cleanup".

Happens on each broker whenever the active segment is closed. Consumes CPU/RAM - and may impact on performance especiallty if using "compact" mode.

Two policies:
* `log.cleanup.policy=delete` - this is the default policy for all user topics:
  * Deletes data based on age
  * Delete data based on size of log (default is -1 => infinite log size)
* `log.cleanup.policy=compact` - this is the default policy for consumer offsets
  * Deletes based on keys of your messages
  * Only delete old duplicates keys **after** the active segment is committed

### Delete
This is set by the `log.retention.hours` property on the topic.

_Alternatively - or complementary - retention can be set at log size, using the `log.retention.bytes` topic property._

* To bound data by size only, set `log.retention.hours` high and set `log.retention.bytes`.
* To bound data by time only, set `log.retention.hours` and set `log.retention.bytes` to -1 (infinite).
* To bound data by time & size only, set `log.retention.hours` and set `log.retention.bytes`.

### Compact
Compacting logs is the equivalent of a log snapshot. Over time, data is published to a topic with a key (an index to the data). That data changes over time. When compacting, only the last copy of the data for that given key is stored.

When compacting, kafka creates new segments (and complemenatary indexes). The order of the offsets remains the same (the offset index remains the same).

Properties to use for log compacting:
* `segment.ms` - equivalent to `log.retention.hours` on delete, and is used to determine when to close active segment and trigger compacting (default 7 days)
* `segment.bytes` - equivalent to `log.retention.bytes` on delete, and is used to determine when to close active segment and trigger compacting (default is 1Gb)
* 'min.compaction.lag.ms` - time to wait before compacting a single message (default is 0 - disabled)
* `deletye.retention.ms` - time to wait before actually deleting the data (default is 24 hours)
`min.cleanable.dirty.ratio` - higher is less, more efficient cleaning (default is 0.5).

_In kafka 2.0, log compaction does stop, and need to restart kafka._