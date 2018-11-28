---
title: Logback
date: 2018-11-28 14:35:54
tags: [IT,Java,Logback]
---

## Logback 是什么？

Logback 是一个 Java 平台上的日志框架，是 log4j 的加强版本，目前分为以下模块：

1. logback-core，放置为下面两个模块服务的基础代码；
2. logback-classic，log4j 的加强版本，实现了 SLF4J API，以便于切换其他日志框架；
3. logback-access，与 Servlet 容器集成，提供 HTTP 访问日志功能。

> SLF4J：The Simple Logging Facade for Java（简单日志门面抽象框架），提供的是日志的 Facade API，需要配合 Log4j、Logback 或 java.util.logging 使用。

## 快速开始

（1）引入依赖

```groovy
compile "org.slf4j:slf4j-api:1.7.+"
compile "ch.qos.logback:logback-core:1.2.+"
compile "ch.qos.logback:logback-classic:1.2.+"
```

（2）编写 `logback.xml` 并防止到 `resources` 文件夹中

```java
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <encoder>
      <pattern>%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n</pattern>
    </encoder>
  </appender>
  <root level="DEBUG">          
    <appender-ref ref="STDOUT" />
  </root>  
</configuration>
```

（3）在代码中使用

```java
Logger LOGGER = LoggerFactory.getLogger(Main.class);
LOGGER.debug("Hello, Logback");
```

## 参考

1. [Logback Project - logback.qos.ch](https://logback.qos.ch/)
2. [LogBack 入门实践](https://segmentfault.com/a/1190000004693427)