---
title: Jconsole
date: 2018-12-13 21:27:50
tags: [Tool,Jconsole,Java]
---

## Jconsole 是干嘛的？

Jsonsole 是 `JDK_HOME/bin` 目录下的一个可执行程序，用于 Java 性能分析，其 GUI 虽简陋，但功能还是可以的。

## 快速开始

在终端运行以下命令后就可以监控本地的 Java 程序了。

```shell
$ jconsole
```

## 更进一步

```shell
# 监控远程 Java 程序
$ jconsole hostName:portNum
# 监控指定进程 ID 的程序
$ jconsole processID
```

## 参考

1. [Using JConsole - oracle.com](https://docs.oracle.com/javase/7/docs/technotes/guides/management/jconsole.html)