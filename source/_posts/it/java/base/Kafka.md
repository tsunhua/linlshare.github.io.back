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

### Kafka Client 消息接收的三种模式

| 模式                      | 特点                   | 设置                                                         |
| ------------------------- | ---------------------- | ------------------------------------------------------------ |
| At-most-once（最多一次）  | 消息最多被消费一次     | 让 Kafka 在特定时间间隔内自动提交，如网络中断恢复或程序重启可能会使消息未被处理完就被提交了。设置 `enable.auto.commit` 为 true，设置 `auto.commit.interval.ms` 为一个较小的时间间隔，不用手动调用 `commitSync()`。 |
| At-least-once（最少一次） | 消息至少被消费一次     | 手动提交，如提交失败，则下次重复推送。设置 `enable.auto.commit` 为 false，然后调用 `commitSync()`。 |
| Exactly-once（正好一次）  | 消息有且只有被消费一次 | 保证消息处理和提交反馈在同一个事务中（ACID，原子性、一致性、隔离性和持久性）。设置 `enable.auto.commit` 为 false，保存 `ConsumeRecord` 中的 offset 到数据库，实现 `ConsumerRebalanceListener` ，监听 Consumer Rebalance 事件，然后使用`seek` 方法将数据库的 offset 更新到 Kafka。 |

### Consumer Group 与 Partition

1. 按照如上的算法，所以如果kafka的消费组需要增加组员，最多增加到和partition数量一致，超过的组员只会占用资源，而不起作用。
2. kafka的partition的个数一定要大于消费组组员的个数，并且partition的个数对于消费组组员取模一定要为0，不然有些消费者会占用资源却不起作用。
  3.如果需要增加消费组的组员个数，那么也需要根据上面的算法，调整partition的个数。

> 作者：ens
> 链接：https://juejin.im/post/5baca032e51d450e735e74af
> 来源：掘金
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

```
 $ /bin/kafka-topics.sh --alter --topic A_TASK --zookeeper localhost:2181 --partitions 2
WARNING: If partitions are increased for a topic that has a key, the partition logic or ordering of the messages will be affected
Adding partitions succeeded!

$ bin/kafka-topics.sh --list --zookeeper localhost:2181
```

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