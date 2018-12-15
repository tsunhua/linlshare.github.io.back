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

## 排错

### KeeperException：NoNode for /config/topics/xxx

#### （1）背景

在本机先后启动 Zookeeper 和 Kafka，然后发送 PING 主题。但消费失败，错误日志如下：

```
[2018-12-06 16:48:06,120] INFO Got user-level KeeperException when processing sessionid:0x1000a6787410000 type:setData cxid:0xcb zxid:0xc4 txntype:-1 reqpath:n/a Error Path:/config/topics/PONG Error:KeeperErrorCode = NoNode for /config/topics/PING (org.apache.zookeeper.server.PrepRequestProcessor)
```

#### （2）查看 Zookeeper 终端日志

发现 host.name 不是 localhost 而是内网地址。

```
[2018-12-06 16:21:18,687] INFO Server environment:host.name=192.168.1.2 (org.apache.zookeeper.server.ZooKeeperServer)
```

#### （3）修改 `config/server.properties`  文件

参看 [Kafka系列2-producer和consumer报错](https://blog.csdn.net/kuluzs/article/details/51577678) ，修改如下：

```shell
// 之前
#listeners=PLAINTEXT://:9092
// 现在
listeners=PLAINTEXT://localhost:9092
```

#### （4）重启 Zookeeper 和 Kafka

## 参考

1. [Quickstart - kafka.apache.org](https://kafka.apache.org/quickstart)