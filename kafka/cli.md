---
title: kafka cli
description: 
published: true
date: 2020-04-11T14:12:50.312Z
tags: kafka, cli
---

# kafka cli



## Local Start
To start `kafka`, you must also start `zookeeper`.

_Assumed downloaded and installed (unpacked) kafka to `<kafka>`._

### zookeeper
_Assumed directory `<kafka>/data/zookeeper`._

Edit the file `<kafka>/config/zookeeper.properties` and change `dataDir` to:
```
dataDir=/home/aylingw/bin/kafka_2.13-2.4.1/data/zookeeper
```

Then run `<kafka/bin/zookeeper-server-start.sh`.

All is well when `zookeeper` is listening on port 2181 (can be changed in `zookeeper.properties`).

### kafka
_Assumed directory `<kafka>/data/kafka`._


Edit the file `<kafka>/config/server.properties` and change `log.dirs` to:
```
log.dirs=<kafka>/data/kafka
```

All is well when `kafka` is listening on port 9092 (can be changed in `server.properties`).

By default, the broker id is `0` (can be changed in `server.properties`).


## Topics
Use the `kafka-topic.sh` script found in `<kafka>/bin`. _Note - the kafka cli works by connecting to zookeeper, that then issues commands to the broker/cluster._

### Create Topic
```
kafka-topics --zookeeper localhost:2181 --topic <NAME OF TOPIC> --create --partitions <#> --replication-factor <#>
```

### List Topics
```
kafka-topics --zookeeper localhost:2181 --list
```

### Describe Topic
```
kafka-topics --zookeeper localhost:2181 --topic <NAME OF TOPIC> --describe
```


### Delete Topic
```
kafka-topics --zookeeper localhost:2181 --topic <NAME OF TOPIC> --delete
```

Notes:
* topics are only marked for deletion; they are not deleted straight away.
* topics need their property `delete.topic.enable` set to true.


## Producer
kafka cli supports creating of producers (publishers to topics) using `<kafka>/kafka-console-producer.sh`.

### launch
To launch a producer, you need a list of ALL brokers in the cluster. If started locally, you have a single broker.

```
`<kafka>/kafka-console-producer.sh` --broker-list localhost:9092 --topic <NAME OF TOPIC>
```




_