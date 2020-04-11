---
title: Kafka Brokers
description: 
published: true
date: 2020-04-11T10:31:44.024Z
tags: kafka, replication, partitions, distributed, leaders, zookeeper
---

# Kafka Brokers
A kafka cluster is composed of brokers (servers is you will). Each broker is identified by a number (there is no predefined numbering). Each broker stores a set of _topic_ partitions.

## bootstrap
All brokers run as a cluster. Producers and Consumer connect to any broker; but just one broker. The initial broker used on connection to is called the "bootstrap broker".

Each broker knows about all other brokers in the cluster (regardless of topic. Each Broker knows about all topics and all partitions across the cluster; as metadata.

> Security? Restricting knowledg of topics/partitions to consumers and producers. Restricting the access across the cluster of brokers - e.g. IP subnet.


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

A replication factor of two, allows for continued operation when one broker fails. A replication factor of three, allowed for continued operation when two brokers fail. N-1.

### leader
At any one time, only one broker can be the leader for a partition.

Only the leader can receive (from producers) and serve (to consumers) data for a topic for a given partition. The other copies (replicated) of  the partition are known as "in-sync replicas" (ISRs).

When a broker goes offline, all the partitions which were leaders are re-elected to other brokers. When that broker comes back online, it re-elects itself as leader on the original set of partitions.


## zookeeper
Manages all brokers within the cluster.

zookeeper managers the process of leader election on partitions.

zookeeper sends events whenever changes are made on a broker, including (but limited to), create/delete topic, broker up/down.

zookeeper can only operate with an odd number of brokers; "dat is de rule".

zookeeper has the concept of a leader/followers. Just one of the zookeeper servers (broker) is the leader (does all writes); the rest are followers. If the leader is lost (fails), a new leader is re-elected from the remaining set of followers.