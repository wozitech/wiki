---
title: kafka cli
description: 
published: true
date: 2020-04-12T07:58:15.402Z
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

The producer CLI usings _string serializer_ (both key and value).

### launch
To launch a producer, you need a list of ALL brokers in the cluster. If started locally, you have a single broker.

```
`<kafka>/kafka-console-producer.sh --broker-list localhost:9092 --topic <NAME OF TOPIC> --producer-property acks=all
...message 1
...message 2
...
```

Notes:
* The topic does NOT have to exist. It won't error when launching, and it might warn when posting to the topic as the leader is elected.
  * The topic is created with defaults (defined in `server.properties` - such as number of partitions and number of replications. It is best to create topic first.
* `<CTRL><D>` to stop sending messages.

### keys
`kafka-console-producer.sh`  consumer can also post messages with a key. Specify the key separator on the command line using `--property` parameter, e.g:
```
<kafka>/kafka-console-consumer.sh --broker-list localhost:9092 --topic <NAME OF TOPIC> parse.key=true --property key.separator=,
```

> key1,message1
> key2,message1
> key1,message2

## Consumer
kafka cli supports creating of consumers (subscribers to topics) using `<kafka>/kafka-console-consumer.sh`.

The producer CLI usings _string deserializer_ (both key and value).

### launch
To launch a consumer, you only need one broker in the cluster; to bootsrap. If started locally, you have a single broker.
```
<kafka>/kafka-console-consumer.sh --broker-list localhost:9092 --topic <NAME OF TOPIC>
```

Notes:
* By default, the consumer only starts to receive messages from the point after which it started.
  * But you can specify the `--from-beginning` option or indeed the `--offset <id>` option.`
  * The order of the messages is per partition and sequentially in the each partition; so not the order in which the messages were sent to the topic.
  
  
### keys
`kafka-console-consumer.sh` when messages are posted to topics with keys, the keys are used to map to a partition, but the consumer by default does not display the key (it is metadata on the message). To display the key along with the message, suffix `--property print.key=true --property key.separator=,` to the consumer.


### groups
`kafka-console-consumer.sh` participants in a _consumer group_, by passing `--group` parameter. There is no need to provie a partition id (optional). As more consumers are added to the group, the partitions are shared between the consumers. When a consumer is stopped, the partition(s) it had are redistributed among the remaining consumers.

Note - when launching a consumer without a group, the a group is created automaticaly with a random name.

#### from beginning
As a _consumer group_, when the group is first started, it defaults to listening for messages from the time it is started. It can also be started to listen from the begginging, by simnply passing the `--from-beginning` flag.

But as a _consumer group_, the _consumer offset_ is written. So when starting the same group with the `--from-beginning` flag, it has no effect; the consumer offset takes precedence.

And as a group, when all the consumers are down, can keep posting messages to the topic. When one of more consumers in the group start up, messages since the _consumer offset_ are replayed.


### manage
`kafka-consumer-groups.sh` manages _consumer groups_.

* list

```
<kafka>/kafka-consumer-groups.sh  --bootstrap-server localhost:9092 --list
```

* describe

```
<kafka>/kafka-consumer-groups.sh  --bootstrap-server localhost:9092 --group <GROUP NAME> --describe
```

#### offsets
`kafka-consumer-groups.sh` supports resetting the _consumer offsets_, with the `--reset-offsets` parameter which takes one of the following options:
* --to-datetime
* --to-earlist
* --to-latest
* --shift-by - forward with a positive integer and back with a negative integer
* --from-file
* --to-current

Each of the above options are combined with one of `--dryrun` or `--execute`.

When resettinga _consumer group's_ offsets, it can be done by an individual topic or all topics associated with that consumer.


e.g:
```
<kafka>/kafka-consumer-groups.sh  --bootstrap-server localhost:9092 --group <GROUP NAME> --reset-offsets --execute --to-earlist --topic <NAME OF TOPIC>
```
