---
title: JPA
date: 2018-12-04 15:51:06
tags: [Java,JPA]
---

##  JPA 是干嘛的？

JPA（Java Persistence API，Java 持久化 API），是一组 ORM（Object Relational Mapping，对象关系映射）规范。所谓持久化，包含三层意思：

1. API 本身，定义在 `javax.persistence ` 包下；
2. JPQL（Java Persistence Query Language，Java 持久化查询语言）；
3. 对象与关联表之间的元数据。

## 实现

| 项目                                                         | 开发公司           | 数据库支持  | 备注                  |
| ------------------------------------------------------------ | ------------------ | ----------- | --------------------- |
| [Hibernate](http://hibernate.org/)                           | RedHat             | SQL         | JPA 制定的参考。      |
| [Spring Data JPA](https://docs.spring.io/spring-data/jpa/docs/2.1.3.RELEASE/reference/html/) | Pivotal            | SQL 及NoSQL | 支持 RESTful API 查询 |
| [EclipseLink](https://www.eclipse.org/eclipselink/)          | Eclipse Foundation | SQL 及NoSQL | 基于 TopLink          |
| [OpenJPA](https://openjpa.apache.org/)                       | Apache             | SQL         | 支持缓存。            |

\* 2001年，澳大利亚墨尔本一位名为Gavin King的27岁的程序员，上街买了一本SQL编程的书，他厌倦了实体bean，认为自己可以开发出一个匹配对象关系映射理论，并且真正好用的Java持久化层框架，因此他需要先学习一下SQL。这一年的11月，Hibernate的第一个版本发布了。

\* Pivotal 和 VMware 都是 EMC 的子公司，2015 年 Dell 以 670 亿美元收购 EMC。 

\* MyBatis 是一套持久化框架，但不是 ORM 的，而且 Java 方法与 SQL 语句的关联。

\* OpenJPA 至今已有 4933 次 commit，更新也很频繁，但其 Github 上的 star 却只有 56，坚持不懈的精神令人肃然起敬。

## 参考

1. [Java 持久化 API - 维基百科](https://zh.wikipedia.org/zh-cn/Java%E6%8C%81%E4%B9%85%E5%8C%96API)