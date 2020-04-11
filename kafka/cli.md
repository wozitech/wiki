---
title: kafka cli
description: 
published: true
date: 2020-04-11T13:56:48.737Z
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