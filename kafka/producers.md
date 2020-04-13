---
title: kafka producers
description: 
published: true
date: 2020-04-13T13:55:14.351Z
tags: kafka, keys, producers, acks
---

# Kafka Producers
Producers write messages to all paritions via all brokers of the cluster (via bootstraping initial broker).

A producer writes to all partitions of a topic, via the partitions' leader brokers. If a broker becomes unavailable, the producer know how to write to the re-elected leader.

## Acks
When a producer writes to a partition, it (the producer) can choose what level of acknowledgements (ack) to wait upon:
* acks=0 - producer does noy wait (possible data loss)
* acks=1 - producer only waits for the leader (limited data loss); this is default
* acks=all - producer waits for leader and ISRs (no data loss).


## Key
When submitting the message topic, you can define a key that will be used to identify the partition.

The key is optional. If no key (null), then producers will write to all partitions using _round robin_.

The key ensures all messages are written to the same partition (key hashing) - so messages are guaranteed to be in the same order.

Choose a key wisely though; a bad key will result in non-uniform data across partitions.

### hashing
kafka uses the "murmur2" algorithm to create hashes.

Can set the `partioner.class` property to provide own hash algorithm (but be careful).


## Versions
Two major versions of kafka:
* V1
* V2

A V2 producer can post to both a V1 and V2 topic.

A V1 producer can post only to a V1 topic.

## Retries
A producer can error when posting to a topic. For example `NotEnoughReplicasException` (when using `acks` of "all" and there are insufficient number of In-Sync Replicas (ISRs).

<= V2.0, retries defaulted to 0. >V2.0 and retries defaults to 2^16.

Along with `retries`, there is a `retry.backoff.ms` property, which defaults to 100ms.

In addition, there is the `delivery.timeout.ms` property, which defaults to 2 minutes, which is the ultimate upper bound.

During retry, there is a chance that messages can be sent out of sequence. This is owing to a producer having multiple threads/connections (`max.inflight.requests.per.connection` - default is 5) - the sequence of which cannot be guaranteed when retrying.

## Idempotent Producer
Owing to network errors, it is possible for a producer to duplicate messages:
![kafka-idempotent-producer-why.png](/uploads/kafka/kafka-idempotent-producer-why.png)

Here it can be seen that the _ack_ to the second message is not received, but it was committed; the producer retries with a duplicate message.

Since kafka V0.11, when the producer posts to topic, it posts with a unique id. When the producer retries, it reuses that unique id which kafka uses to trap a duplicate, returning what would have been the original ack; no duplicate message:
![kafka-idempotent-producer-effect.png](/uploads/kafka/kafka-idempotent-producer-effect.png)

With idempotent producers and kafka V1.0+, kafka can continue to assure message sequence with `max.inflight.requests.per.connection` set to the default of 5.

To benefit, you must set `enable.idempotence` to true when creating your producer.

### Safe Producer
The properties to set for a safe producer (messages assured to be sent and sent just once) for kafka V1.0+:

* `enable.idempotence` - true
* `acks` - "all" (or -1)
* `retries` - leave as default
* `max.inflight.requests.per.connection` - leave as default of 5


## Compression
Compression (`compression.type`) can be set when a producer posts a message. Can be one of:
* none - **default**
* gzip (highest compression but slowest)
* lz4
* snappy


Compression works better on batches of messages or messages with lots of repeating data (e.g. JSON arrays).

## Batches
A producer can post to a topic individual messages, or can batch up messages and post in bulk.

kafka tries to send messags as soon as possible; by default it can keep 5 messages in flight.

If the volume of messages inbound is greater, kafka will automatically start to batch messages to increase throughput (given parition resolution).

kafka has two properties that control batches:
* `linger.ms` - defaults to 0; being the time in ms that kafka is willing to wait before sending message
  * _real world events have shown that introducing some lag on producers can result in more throughout as messages as dsitributed in batches._
* `batch.size` - volume of batch, e.g. 32KB; if the message backlog hits this before `linger.ms` is reached, the set (_batched_) messages will be sent.
  * _A message greater than the `batch.size` will be sent directly._


### Fast Producer
The properties to set for a fast producer:

* `compression.type` - snappy (_assumed text based, e.g. log or JSON_)
* `linger.ms` - 20
* `batch.size` - 32KB
* `max.inflight.requests.per.connection` - leave as default of 5


## Buffers
When posting data to a topic, kafka returns immediately, because the message is added to a buffer. The size of that buffer is control by `buffer.memory` (defaults to 32MB),

If posting faster than kafka is able to send or is unable to send (all brokers are down), this buffer will overflow, and kafka will start to block on all posts. The duration of the block is given by the property `max.block.ms` (defaults to 60 seconds).

An exceptions is thrown when:
1. The buffer is full
2. The blocking timeout has expired

