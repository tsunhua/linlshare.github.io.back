---
title: MyBatis
date: 2018-12-04 15:51:06
tags: [Java,MyBatis]
---

## MyBatis 是干嘛的？

MyBatis 是一个 SQL 映射框架，它通过 XML 描述符或者注解将对象与**关系型数据库**的存储过程或 SQL 语句关联起来。

## 特性

### 缓存

1. 支持声明式数据缓存；
2. 提供基于 HashMap 的默认缓存实现；
3. 提供 API 供其他缓存实现。

## 使用

### 单独使用

```groovy
compile 'org.mybatis:mybatis:3.4.6'
```

如使用其他依赖引入方式，参看 [mybatis - maven.org](https://search.maven.org/artifact/org.mybatis/mybatis/3.4.6/jar)。

### 集成使用

1. 与 Spring Framework 集成
2. 与 Google Guice 集成

## 参考

1. [MyBatis - 维基百科](https://zh.wikipedia.org/zh-cn/MyBatis)
2. [mybatis-3 - mybatis.org](http://www.mybatis.org/mybatis-3/zh/)