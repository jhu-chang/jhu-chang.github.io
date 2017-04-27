---
layout: post
title: "Kafka Install"
category: "BigData"
tags: [kafka,install]
---

## Kafka ##

### How to install the kafka ###

- Download the Kafka package
  
  ```
  wget http://mirrors.hust.edu.cn/apache/kafka/0.10.0.0/kafka_2.11-0.10.0.0.tgz
  ```

- Unzip the Kafka

  ```
  tar -zxf kafka_2.11-0.10.0.0.tgz
  ```

- Start the zookeeper server

  ```
  bin/zookeeper-server-start.sh config/zookeeper.properties
  ```

- Start the Kafka server

  ```
  bin/kafka-server-start.sh config/server.properties
  ```

- Create a topic `test`

  ```
  bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
  ```

- List a topic

  ```
  bin/kafka-topics.sh --list --zookeeper localhost:2181
  ```

- Send message to `test`

  ```
  bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
  ```

  Send a file (`file.csv`) to topic `test`

  ```
  bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test < file.csv
  ```

- Receiver message from `test`

  ```
  bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning
  ```

### Kafka connect from outside ###

> `X.X.X.X` is your public address.

- server.properties

  ```
  listeners=PLAINTEXT://0.0.0.0:9092
  advertised.host.name=X.X.X.X
  advertised.listeners=PLAINTEXT://X.X.X.X:9092
  ```
-  producer.properties

  ```
  bootstrap.servers=X.X.X.X:9092
  metadata.broker.list=X.X.X.X
  host.name=0.0.0.0
  ```

- consumer.properties

  ```
  zookeeper.connect=X.X.X.X:2181
  ```
