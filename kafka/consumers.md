---
title: kafka consumers
description: 
published: true
date: 2020-04-13T15:11:19.986Z
tags: kafka, partitions, consumers, offsets, consumer groups, delivery semantics, bootstrap
---

# Kafka Consumers
Consumers read data from a topic.

Consumers connect to all brokers (the cluster partions; via a bootstrap) and know which broker to read data from. If a broker fails, the consumer knows which re-elected leader to read from.


## Partitions
Consumers read from one or more (including ALL) partitions in parallel.  Data from a single partition arrives in sequence.


## Groups
A single consumer, does not have to read from all partitions. This allow multiple consumers to spread the load of consuming data. These are called "consumer groups". 

Across the consumer group, all partitions must be read.

Only one consumer within the group, can read from a given partition.

Multiple "consumer groups" (multiple subscribers) can read across all partitions.

## Offsets
kafka stores the partition offsets (for each partition) for each "consumer group".

These consumed offets are called within a system topic called, `__consumer_offsets`.

When the "consumer group" has processed (not read) data from a topic, it _should_ commit it's offsets. This allows the consumer group to recover from consumer failures (be them intentional or unintentional).

_Note - prior to V0.10 of kafka, consumer offsets were written to zookeeper._

### Retentions
kafka expects a consumer to continuously stream the data from a topic. If kafka topic has a retention say 7 days, but the consumer does not commit their offsets within 7 days, those offsets will become void.

If the consumer starts up with _earliest_ or _latest_ offsets, the offsets will be rebuilt. Any other setting, and kafka will throw an exception.

kafka also have a broker defined period where (`offset.retention.minutes`) it demands all consumers to have committed offsets within. If the consumer is online for longer than that period, its offsets will be deleted:
* <V2.0 - 1 day
* >V2.0 - 7 days


### Delivery Semantics
Consumers choose when to write their offsets, choosing one of three schemes:
* At most once - committed as soon as the message is read. If something goes wrong after reading, the message is lost.
* At least onne - _preferred_ committed  only when processed. The consumers must be idempotent (reprocessing the same message should not cause a problem). Remember, messages in the same partition are read in sequence.
* Exactly Once - this is only available kafka => kafka workflows using the Kafka Streams API.

Two strategies for consumer commits:
* [easy] `enable.auto.commit` = true with syncrhonous processing of batches
  * `auto-.commit.interval.ms` defaults to 5000 (every five seconds) and when closing the consumer
  * but if the batch fails, the commits have already been made and you're operating in "at most once"
* [harder] `enable.auto.commit` = false with manual commit of offsets



## Versions
Two major versions of kafka:
* V1
* V2

A V2 consumer can subscribe to both a V1 and V2 topic.

A V1 consumer can subscribe only to a V1 topic.


## Long Poll
The kafka consumer effectively polls for data from brokers:
![kafka-long-poll.png](/uploads/kafka/kafka-long-poll.png)

This polling behaviour can be tuned. BUT - only tune if getting exceptions. The defaults have proven to work well in most scenarios.

* `fetch.min.bytes`- default is 1; how many bytes to wait until releasing messages for processing - equvalent to a producer's `batch.size` property
* `max.poll.records` - default is 500; how many records to wait until releasing messages for processing - equvalent to a producer's `linger.ms` property

Introducing latency on very fast streaming messages typically improves performance.

`max.partitions.fetch.bytes` - default 1MB; top level of data that can be returned by each broker for each partition of a topic; lots of memory will be required if you have a topic with lots of partitions.

`fetch.max.bytes` - default is 50MB; top level of data that can be returned in one request (across all brokers and partitions).


## Re Balancing
Consumers within a group have a heartbeat, which if it fails, is used to re-balance a topic's partitions across remaining consumers or when new consumers are added to the group.

* `session.timeout.ms` - default is 10 second; will wait for this period to assume the consumer is no more
* `heartbeat.interval.ms` - default is 3 seconds; this is the poll period. Rule of thumb, set this to one third of the session timeout.
