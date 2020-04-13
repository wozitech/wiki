---
title: kafka producers
description: 
published: true
date: 2020-04-13T09:12:15.182Z
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

During retry, there is a chance that messages can be sent out of sequence. This is owing to a producer having multiple threads/connections (`max.inflight.requests.per.connection`) - the sequence of which cannot be guaranteed when retrying. 