---
title: Kafka Brokers
description: 
published: true
date: 2020-04-11T09:13:43.186Z
tags: kafka, brokers
---

# Kafka Brokers
A kafka cluster is composed of brokers (servers is you will). Each broker is identified by a number (there is no predefined numbering). Each broker stores a set of _topic_ partitions.

## bootstrap
All brokers run as a cluster. When you connect to any broker, you are connected to the cluster. The broker you connect to is called the "bootstrap broker".

## minimum and maximum
Start with a cluster of three brokers. This allows for one Broker to be taken offline, and still maintain the cluster availability.

There is no limit to the number of brokers.

## storage
As brokers store _topic partitions_ 

## distribution
A topic's partitions are distributed across the brokers at the time the topic ia created. kafka will try to ensure no two partitions of the same topic are stored on the same broker.

But if your topic has more partitions than brokers, then multiple partitions will end up on the same broker.

Partitions are spread across all brokers in a predetermined but dsitribution manner; _random_ like - but it is deterministic based on broker load.