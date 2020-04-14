---
title: kafka Schema Registry
description: 
published: true
date: 2020-04-14T06:01:42.322Z
tags: kafka, schema, registry
---

# kafka schema registry
Kafka brokers do not care for what data is in a message; even the key is provided as metadata.

Pushing _crap_ into a topic only guarantees that _crap_ remains within the topic and _crap_ can come out of the topic.

The Confluent Schema Registry is not kafka per se; it is a separate service that can be used by producers and consumers to verify message content.

It supports:
* schemas
  * with evolution
* lightweight