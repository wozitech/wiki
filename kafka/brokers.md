---
title: Kafka Brokers
description: 
published: true
date: 2020-04-11T09:43:24.081Z
tags: kafka, replication, partitions, distributed, leaders, zookeeper
---

# Kafka Brokers
A kafka cluster is composed of brokers (servers is you will). Each broker is identified by a number (there is no predefined numbering). Each broker stores a set of _topic_ partitions.

## bootstrap
All brokers run as a cluster. When you connect to any broker, you are connected to the cluster. The broker you connect to is called the "bootstrap broker".

## minimum and maximum
Start with a cluster of three brokers. This allows for one Broker to be taken offline, and still maintain the cluster availability.

There is no limit to the number of brokers.

## storage
As brokers store _topic partitions_, storage and fast storage is key. The fastest storage is of course RAM. Scale out horizontally using RAM. But don't simply scale your topics to have 100 partitions and 100 brokers, if your topic has a poor _parition key_. 

## distribution
A topic's partitions are distributed across the brokers at the time the topic ia created. kafka will try to ensure no two partitions of the same topic are stored on the same broker.

But if your topic has more partitions than brokers, then multiple partitions will end up on the same broker.

Partitions are spread across all brokers in a predetermined but dsitribution manner; _random_ like - but it is deterministic based on broker load.

## replication
When creating a topic, you decided on the replication factor; greater than one, but typically 2 or 3.

This dictates the number of copies each broker makes of each of its partitions. If that broker were to become unavailable (either intetionally or unintentionally), one of the other brokers can continue to serve the topic.

A replication partition for a topic can exist on brokers where there is no primary partition for that topic. Every broker in the cluster is equal.

A replication factor of two, allows for continued operation when one broker fails. A replication factor of three, allowed for continued operation when two brokers fail.

### leader
At any one time, only one broker can be the leader for a partition.

Only the leader can receive (from producers) and serve (to consumers) data for a topic for a given partition. The other copies (replicated) of  the partition are known as "in-sync replicas" (ISRs). This is managed by zookeeper.

When a broker goes offline, all the partitions which were leaders are re-elected to other brokers. When that broker comes back online, it re-elects itself as leader on the original set of partitions.


## Acks
When a producer writes to a partition, it (the producer) can choose what level of acknowledgements (ack) to wait upon:
* acks=0 - producer does noy wait (possible data loss)
* acks=1 - producer only waits for the leader (limited data loss); this is default
* acks=all - producer waits for leader and ISRs (no data loss).


## Key
When submitting the message topic, you can define a key that will be used to identify the partition.

The key is optional. If no key (null), then producers will write to all partitions using _round robin_.

The key ensures all messages are written to the same partition - so messages are guaranteed to be in the same order.

Choose a key wisely though; a bad key will result in non-uniform data across partitions.