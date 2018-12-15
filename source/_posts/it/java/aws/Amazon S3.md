---
title: Amazon S3
date: 2018-12-14 12:02:45
tags: [Java,aws,S3]
---

## Amazon S3 是什么？

Amazon S3 是亚马逊推出的一款存储服务，名为 Amazon Simple Storage Service，即亚马逊简单存储服务。

有些 S3 的概念需要了解一下：

1. 存储桶（Buckets）：S3 中用于存储对象的容器，相当于文件系统中的目录（Directory）的概念。
2. 对象（Objects）：S3 中存储的基本实体，由对象数据和元数据组成，元数据是描述对象的一组键值对。在存储中的对象由键和版本 ID 唯一标识。
3. 键（Keys）：存储桶中对象的唯一标识符。
4. 区域（Regions）：地理区域。

> S3 中的对象映射：存储桶 + 键 + 版本 --> 对象

## API 1.0

## 排错

### 身份验证错误

BasicProfileConfigLoader - Your profile name includes a 'profile ' prefix. This is considered part of the profile name in the Java SDK, so you will need to include this prefix in your profile name when you reference this profile from your Java code.

