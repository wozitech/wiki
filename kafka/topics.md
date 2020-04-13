---
title: Kafka Topics
description: 
published: true
date: 2020-04-13T08:53:32.234Z
tags: kafka, partitioning, keys, TTL
---

# Kafa Topics
Topic is the most critical entity in kafka. Think of it like a database table without the constraints. Messages are posted to a topic (producers) and distributed via that topic (consumers).

It (the topic) has a name. No limit to the number of topics, or on the name.

## Partitions
A topic must be created with a given number of partitions (_the number of partitions can be changed later_). Partitions are labelled from 0. Each partition is orderted. Each message within a the same parition is given an incremental id, called an offset.

A message is referenced by "topic:partition:offset".

Order of messages is only guaranteed within a partition; not across partition. If you have data which is temporal, then choose a key that ensures the data is written to the same partition for the given time eseries. If all events are time based, choose a temporal database (like [promtetheus](https://en.wikipedia.org/wiki/Time_series_database)) as a consumer and stream from the topic.

### Keys
Note - keys are not defined against a topic is created (unlike a database table index). A key is against a message; see Brokers.


## TTL
Data within a topic expires after a given duration; the default is one week.


## Immutable
Once data is written to a partition, it can not be updated.

