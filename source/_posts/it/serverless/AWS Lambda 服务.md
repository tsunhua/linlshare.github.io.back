---
title: AWS Lambda 服务
date: 2019-01-17 22:40:52
tags: [Lambda,AWS]
---

## 简介

Lambda 是 AWS 提供的一个无服务器架构的服务，用户只需要专注于写代码而不用关心部署，通常需要关心外部事件源，可以与 API Gateway, SNS, S3, DynamoDB 等等配合使用。

## 本质

简单粗暴地说，一个 Lambda 就是一个 Docker 服务或者 Firecracker 服务，Firecracker 是 AWS 开源的一个类似 Docker 的服务，AWS 计划将所有的 Lambda 实现改为 Firecracker。因此，当 Lambda 第一次被调用时通常会比较慢，因为服务需要先进行冷启动，但接下来的调用就会快很多了。

## 支持

1. 默认支持 6 种编程语言（.Net, Python, Node.js, Java, Go, Ruby）；
2. 支持自定义运行时（仓库 [mthenw/awesome-layers](mthenw/awesome-layers) 中罗列了一些开源的运行时）；
3. 支持Lambda 之间的共享库（类似 Docker 的 Layer 概念），方便各个 Lambda 之间共用公共代码；
4. 支持版本控制；
5. 支持红蓝发布，即将请求按比例发送到不同版本的 Lambda 函数中。

## 限制

1. 非预留账户并发 1000（注意：不是单个函数，而是单个 AWS 账户），提升需提 case；
2. 单个函数内存占用 128M ~ 3008M；
3. 单个函数最长运行时间是 15分钟；
4. 单个函数最多只能包含 5 个函数层（即共享库）；
5. 单个函数部署包压缩状态下不得超过 50 MB，解压状态不得超过 250 MB；
6. 单个区域（Region）Lambda 函数的总大小不得超过 75 GB。

## 费用

1. 计费方式：需按使用量计费，具体为函数的**请求数量**、**持续时间**和**内存量**。
2. 免费套餐：每个月 100万请求，及 400000GB-秒。
3. 免费之后：每 100 万个请求 0.20 USD，每 GB-秒 0.00001667 USD。

## 参考

1. [AWS Lambda - aws](https://docs.aws.amazon.com/zh_cn/lambda/latest/dg/welcome.html)（AWS Lambda 的官方开发人员指南）
2. [AWS Lambda 定价 - aws](https://aws.amazon.com/cn/lambda/pricing/)