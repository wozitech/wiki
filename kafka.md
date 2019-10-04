![Kafka Logo](/uploads/logos/kafka-icon.png "kafka Logo"){.pagelogo}
<!-- TITLE: kafka -->
<!-- SUBTITLE: Confluent's platform for streaming data - the new middleware -->
# References
The Book:
[kafka: The Definitive Guide](/uploads/kafka/confluent-kafka-definitive-guide-complete.pdf "kafka: The Definitive Guide")

Articles:
* https://www.confluent.io/blog/building-a-microservices-ecosystem-with-kafka-streams-and-ksql/ - yet to read

# Background
kafka is made up of:
* Consumers
* Producers
* Connect API
* Streams
* KSQL

Messages are typically JSON/XML self describing content, but can be any binary or text format. Ideally, the format of the message should exist alongside the message itself making it self describing. This is why JSON and XML are good with JSON Schemas and XML Schemas. But more and more, folks are using Apache Avro, not just because its an efficient binary format and self describing, but because the data definition schema can be versioned too.

In kafka, all messages are sent to a topic (stream); FIFO. Partitions (shards) can be created on a topic based on a predefined key; each message within the partition is still FIFO. kafta distributively stores all messages, thus providing resilience, and each topic/partition having an `offset` allowing to stops and restarts for consumers with no loss of data.

# zookeeper
This an Apache technology for maintaining configration, control and monitoring of distributed services. Zookeeper can be deployed with kafka to monitoring the kafka ecosystem (clusters, topics, partitiions, ...).

# prometheus
> TBC

# kubernetes
kubernetes is now the industry's de facto app hosting platform. No longer are we virtualising servers to host applications, we simpy package (dockerise) applications and deploy on kubernetes. kubernetes has good scaling and monitoring options (least not Prometheus), so we do it yourself and moreso, why no consolidate on a common platform so the deployment, scaling and monitoring of all applications is consistent.

kafka, even though it requires good (fast) storage and networking, Confluent fully support deployment of kafka on kubernetes, through its `Operator`offering: https://www.confluent.io/confluent-operator.

# To Watch
* Apacke storm - realtime computation built on top of kafka; a network of predefined bots fed by spouts (data source) - not too dissimilar from a neural network
* Apache samza - distributed stream processing events/ETL built on top of kafka
* Apache airflow - authoring, scheduling and monitoring of workflows using Directed Acyclic Graphs (DAGs) - could be used to workflow data streaming through kafka

# Alternatives
https://blog.scottlogic.com/2018/07/06/comparing-streaming-frameworks-pt1.html

* Apache Flink - massively distributed (master/slaves architecture) computation cluster, operating on events and streams. Can use kafka as underlying storage infrastructure, but does not have to.