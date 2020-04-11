---
title: kafka producers
description: 
published: true
date: 2020-04-11T09:56:27.022Z
tags: kafka, keys, producers, acks
---

# Kafka Producers
Producers write messages to paritions via the brokers of the cluster.

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