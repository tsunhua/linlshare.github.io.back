---
title: Kafka
date: 2018-11-22 17:44:02
tags: [Java,JavaEE,Kafka]
comments: true
---

## 概念

Kafka 是一个快速、可扩展和高可用的基于发布-订阅模式（pub-sub model）的消息系统，用作消息中间件，在系统之间传递消息。其核心概念有：

- Topic（话题）
- Producer（生产者）
- Consumer（消费者）
- Broker（经纪人）

在 Kafka 中，所有的消息都由 Topic 来组织，Producer 把消息发送到特定的 Topic，Consumer 从特定的 Topic 中读取消息。作为一个分布式系统，Kafka 运行在集群中，集群中的每个节点称之为 Broker。

## 快速开始

### 安装

[下载](https://www.apache.org/dyn/closer.cgi?path=/kafka/2.1.0/kafka_2.11-2.1.0.tgz)  `kafka_2.11-2.1.0.tgz`，也可以使用命令 `wget "http://mirrors.hust.edu.cn/apache/kafka/2.1.0/kafka_2.11-2.1.0.tgz"` 下载，注意 kafka 中已经包含了 zookeeper。执行以下命令解压并切换工作目录：

```shell
tar -xzf kafka_2.11-2.1.0.tgz
cd kafka_2.11-2.1.0
```

### 启动

```shell
# 启动 zookeeper，默认监听端口 2181
bin/zookeeper-server-start.sh config/zookeeper.properties
# 启动 kafka，默认监听端口 9092
bin/kafka-server-start.sh config/server.properties
```

### 停止

```shell
# 停止 zookeeper
bin/zookeeper-server-stop.sh config/zookeeper.properties
# 停止 kafka
ps aux | grep kafka | grep -v grep | awk '{print $2}'
kill [pid]
```

### 发送消息

```shell
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
```

### 接收消息

```shell
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
```

### 查看 Topic 列表

```shell
bin/kafka-topics.sh --list --zookeeper localhost:2181
```

### 查看指定 Topic 的信息

```shell
bin/kafka-topics.sh --zookeeper localhost:2181 --describe --topic a_topic
```

## 进一步学习

## 排错

### Connection to node -1 could not be established. Broker may not be available.

1. 检查连接的 kafka 集群地址是否正确；
2. 检查 kafka 集群收发消息是否正常；
3. 检查 kafka 配置文件（server.properties）是否正确。

默认配置如下：

```java
listeners=PLAINTEXT://:9092
```

如果被修改为错误地址那么将无法连接。

### ConcurrentModificationException: KafkaConsumer is not safe for multi-threaded access

一个线程只能有一个 Consumer，如果多个线程共用一个 Consumer，那么就会出现这个错误。

解决方案就是去解决线程问题，确保只有一个线程调用一个 Consumer。

### Kafka 消息不按顺序消费

背景：查看消息日志发现原本按顺序发送的消息 `1,2,3` 被消费的时候变成 `1,3,2` 了。

原因：Kafka 不保证全局的消息有序性，但保证同一个 Partition 消息的有序性。

解决方案：发消息时设置同一个 Key 或者直接指定同一个 Partition 即可。

### Kafka 日志过多问题

调整日志级别为 info，在 logback.xml 文件中写入

```xml
<logger name="org.apache.kafka" level="INFO" />
```

## 参考

1. [Quickstart - kafka.apache.org](https://kafka.apache.org/quickstart)