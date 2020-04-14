---
title: kafka streams
description: 
published: true
date: 2020-04-14T05:47:39.455Z
tags: kafka, streams
---

# kafka Streams API
A kafka "source" consumer will pump data into a topic. A kafka "sink" producer will pump data from a topic. But it is all or nothing.

kafka streams allow you to filter and transform data between source and sink, in addition to being able to calculate new data, e.g. metric (number of events with flag `bob=true`.

kafka stremas ia a JAVA API (library). There is a good node.js wrapper library: https://www.npmjs.com/package/kafka-streams (unlike the node.js libraries for producers and consumers).

Uses:
![kafka-streams-uses.png](/uploads/kafka/kafka-streams-uses.png)

Architecture:
![kafka-streams.png](/uploads/kafka/kafka-streams.png)