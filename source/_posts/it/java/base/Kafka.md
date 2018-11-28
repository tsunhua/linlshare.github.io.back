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

[下载](https://www.apache.org/dyn/closer.cgi?path=/kafka/2.1.0/kafka_2.11-2.1.0.tgz)  `kafka_2.11-2.1.0.tgz`，注意 kafka 中已经包含了 zookeeper。执行以下命令解压并切换工作目录：

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

