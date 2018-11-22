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



