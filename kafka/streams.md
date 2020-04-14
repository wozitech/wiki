---
title: kafka streams
description: 
published: true
date: 2020-04-14T05:37:05.305Z
tags: kafka, streams
---

# kafka Streams API
A kafka "source" consumer will pump data into a topic. A kafka "sink" producer will pump data from a topic. But it is all or nothing.

kafka streams allow you to filter and transform data between source and sink, in addition to being able to calculate new data, e.g. metric (number of events with flag `bob=true`.

kafka stremas ia a JAVA API (library).

Uses:
![kafka-streams-uses.png](/uploads/kafka/kafka-streams-uses.png)

Architecture:
![kafka-streams.png](/uploads/kafka/kafka-streams.png)