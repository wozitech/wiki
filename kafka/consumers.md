---
title: kafka consumers
description: 
published: true
date: 2020-04-11T10:11:48.554Z
tags: kafka, partitions, consumers, groups
---

# Kafka Consumers
Consumers read data from a topic.

Consumers connect to all brokers (the cluster partions) and know which broker to read data from. If a broker fails, the consumer knows which re-elected leader to read from.


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

## Delivery Semantics
