---
title: JDBC
date: 2018-12-04 15:51:06
tags: [Java,JDBC]
---

## JDBC 是干什么的？

是 Java 语言中用来规范客户端程序如何来访问数据库的应用程序接口，提供了诸如**查询和更新**数据库中数据的方法。JDBC 是面向**关系型数据库**的。

### JPA 与 JDBC 的异同？

**（1）相同点**

- 都是面向关系型数据库的；
- 都具备查询保存数据的能力。

**（2）不同点**

- JPA 是 Java 持久化 API 的规范，关注将数据库中的表与实体类做映射；
- JDBC 是 Java 数据库访问的接口，关注数据的 CRUD。

参看 [JPA or JDBC, how are they different? - stackoverfow](https://stackoverflow.com/questions/11881548/jpa-or-jdbc-how-are-they-different)

## 参考

1. [Java 数据库连接 - 维基百科](https://zh.wikipedia.org/zh-cn/Java%E6%95%B0%E6%8D%AE%E5%BA%93%E8%BF%9E%E6%8E%A5)

