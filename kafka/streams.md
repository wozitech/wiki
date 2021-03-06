---
title: kafka streams
description: 
published: true
date: 2020-04-14T05:57:55.567Z
tags: kafka, streams
---

# kafka Streams API
A kafka "source" consumer will pump data into a topic. A kafka "sink" producer will pump data from a topic. But it is all or nothing.

kafka streams allow you to filter and transform data between source and sink, in addition to being able to calculate new data, e.g. metric (number of events with flag `bob=true`.

Uses:
![kafka-streams-uses.png](/uploads/kafka/kafka-streams-uses.png)

Architecture:
![kafka-streams.png](/uploads/kafka/kafka-streams.png)

## Library
kafka stremas ia a JAVA API (library). There is a good node.js wrapper library: https://www.npmjs.com/package/kafka-streams (unlike the node.js libraries for producers and consumers).

Provides set of operators that be used to filter and transform data; filter, map, reduce, ...

You build one or more `kstreams`, and start each of those kstreams within the cluster.