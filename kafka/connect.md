---
title: kafka Connect API
description: 
published: true
date: 2020-04-14T05:20:37.939Z
tags: kafka, connect, API
---

# kafka Connect API
Simplifies getting data in and out of kafka, as both a consumer (sink) and a producer (source) minimising custom code. Many products provider supported connectors.

![kafka-connect.png](/uploads/kafka/kafka-connect.png)

* "source" connectors to stream data into kafka
* "sink" connectors to stream data out of kafka
* reliable & scalable
* Part of any ETL pipeline
* Reuses code developed in by product companys (when using the company's own connetor)

## Download
Connectors are downloaded as binary distributions or source:
https://www.confluent.io/hub/?utm_medium=sem&utm_source=google&utm_campaign=ch.sem_br.nonbrand_tp.prs_tgt.kafka_mt.xct_rgn.emea_lng.eng_dv.all&utm_term=kafka%20connect&creative=&device=c&placement=&gclid=Cj0KCQjwm9D0BRCMARIsAIfvfIa8N-Lrv9zCnVFQWMZVhf1hR6-XQgIqFiKLUD6EwS6MDH-XRcGaSI4aAnwTEALw_wcB

Read the instructions on how to build/install the connector, and significantly, what configuration properties they require. Create a `.properties` file in the top level directory of all 

Install all your connectors into the same parent folder, called `connectors`.

## Run
The connectors run on the kafka cluster.

To start a connector, use the `<kafka>/bin/connect-standalone.sh` or `<kafka>/bin/connect-distributed.sh` script, which need the location of the connector properties, typically, `<kafka>/config` directory.

There are many difference property files, just to name a few:
* `connect-standalone.properties`
* `connect-distributed.properties`
* `connect-console-sink.properties`

These a connector profiles, and are templates to use in given scenarios. The same _connect_ properties file is used for all connectors; copy it to the parent directory where you have installed your connector. The _properties_ file specifies the location of connectors, viz `connectors` .

To start the connector simply run from within the parent folder of the connectors:
```
<kafka>/bin/connect-standalone.sh ./connect-standalone.properties <./<connector.properties>`
```

_where <connector.properties> is the custom properties file you created when installing the connector._


