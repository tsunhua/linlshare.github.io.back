---
title: Java EE
date: 2018-10-29 20:14:00
tags: [Java,JavaEE,服务器]
comments: true
---

## Java EE

Java平台企业版，2018年3月更名为 **Jakarta EE**。Jave EE 是一系列技术标准所组成的平台，包括：

- Servlet
- EJB
- JDBC
- JSP
- JSTL
- ...

## 服务器与容器（Server and Container）

### 服务器的定义

依[*维基*](https://zh.wikipedia.org/zh-cn/%E6%9C%8D%E5%8A%A1%E5%99%A8)，服务器（软件）是指一个管理资源并为用户提供服务的计算机软件。可分为：

- 文件服务器（File Server），提供文件存取服务。
- 数据库服务器（Database Server），提供数据库存取服务。
- 邮件服务器（Mail Server），提供邮件存取服务。
- **网页服务器**（Web Server），提供网页浏览服务，通常所说的 Web 服务器，含义更广，包括了应用程序服务器。
- FTP 服务器（FTP Server），提供文件传输服务。
- **应用程序服务器**（Application Server），提供应用程序接口服务。
- **代理服务器**（Proxy Server），提供代理服务，分正向和反向代理。
- ...

### 正向代理（Forward Proxy）与反向代理（Reverse Proxy）

正向代理和反向代理以代理对象为区分，正向代理代理的是客户端，而目标服务器对真实的客户端的请求是无感的；反向代理代理的是服务器群（或簇），而客户端对真实处理请求的服务器是无感的。更多信息参见：[*反向代理为何叫反向代理？ - 刘志军的回答 - 知乎*](https://www.zhihu.com/question/24723688/answer/128105528)。

### 容器的定义

该语境下的容器，既不是上街买菜用的容器，也不是存取其他类的集合（比如 List），而是一个运行在 JVM 的 Java 程序，本身作为一个组件的运行时 ，起组件与服务器之间的接口作用，实际上也可以称之为服务器软件，只是当处在大的服务器软件包裹下时，选择另一种称呼罢了。

随着网络服务的要求越来越复杂，开发人员使用规范和组件的概念将服务划分，组件实现了规范，容器运行着组件，最新的 *规范-组件-功能-容器* 对应关系如下：

| 规范    | 组件        | 功能                                                         | 容器         |
| ------- | ----------- | ------------------------------------------------------------ | ------------ |
| JSR 369 | Servlet 4.0 | Server Applet，服务端小程序，处理基于 HTTP 的 Web 请求，响应动态 Web 内容。 | Servlet 容器 |
| JSR 245 | JSP 2.1     | JavaServer Pages，实现动态网页，其基于 Servlet 技术，故 JSP 容器是 Servlet 容器的子集。 | JSP 容器     |
| JSR 220 | EJB 3.0     | [Enterprise JavaBean](https://zh.wikipedia.org/zh-cn/EJB#EJB_3.0)，封装业务逻辑，包括 Session Bean，Entity Bean 和 Message Driven Bean。 | EJB 容器     |

### 常见的 Web 服务器

- Apache HTTP Server
- Nginx
- IIS
- Tomcat
- Jetty
- WildFly（原名JBoss AS或者JBoss）
- Netty

## 架构（Architecture）

软件架构是指软件的基本结构，阮一峰在 [*软件架构入门*](http://www.ruanyifeng.com/blog/2016/09/software-architecture.html) 中谈及架构的分类，可分为：分层架构（Layered Architecture）、事件驱动架构（Event-driven Architecture）、微核架构（Microkernel Architecture）、微服务架构（Microservices Architecture）、云架构（Cloud Architecture）。

- 分层架构的核心是 “数据”，围绕数据的呈现、逻辑处理、持久化和存储进行分层；
- 事件驱动架构的核心是 “事件”，围绕事件的整个发出、队列、分发、通道、处理进行分层；
- 微核架构的核心是 “插件”，将每个小功能做成可插拔的插件形式嵌入内核；
- 微服务架构的核心是 “微服务”，将每个服务单独运行，并通过远程通信协议联系在一起；
- 云架构的核心是 “虚拟化”，没有中央数据库，由一个虚拟中间件（Virtualized Middleware）和若干处理单元（Processing Unit）组成，数据通过虚拟中间件中的数据中间件进行同步。

## 框架（Framework）

参考[*维基*](https://en.wikipedia.org/wiki/Software_framework)，软件框架是指一种通用的，实现基本功能的软件，用户可以在上面添加业务特定的代码。软件框架可能包括支持程序，编译器，代码库，工具集和应用程序编程接口（API），它汇集了所有不同的组件，以支持项目或系统的开发。框架相较于普通的代码库有如下特征：

1. 控制反转，由框架决定程序的控制流程。
2. 可扩展性。
3. 封闭性，框架代码不可修改。

常见的 Java EE 开发框架：

- Spring
- Spring MVC
- Spring Cloud
- Hibernate
- MyBatis
- Dubbo
- Kafka

常见的 Java EE 开发框架集：

- Spring Boot
- SSH（Spring + Spring MVC + Hibernate）
- SSM（Spring + Spring MVC + MyBatis）