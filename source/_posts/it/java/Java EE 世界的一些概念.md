---
title: Java EE 世界的一些概念
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

依[维基百科](https://zh.wikipedia.org/zh-cn/%E6%9C%8D%E5%8A%A1%E5%99%A8)，服务器（软件）是指一个管理资源并为用户提供服务的计算机软件。可分为：

- 文件服务器（File Server），提供文件存取服务。
- 数据库服务器（Database Server），提供数据库存取服务。
- 邮件服务器（Mail Server），提供邮件存取服务。
- **网页服务器**（Web Server），提供网页浏览服务。
- FTP 服务器（FTP Server），提供文件传输服务。
- **应用程序服务器**（Application Server），提供应用程序接口服务。
- **代理服务器**（Proxy Server），提供代理服务，分正向和反向代理。
- ...

### 正向代理（Forward Proxy）与反向代理（Reverse Proxy）

正向代理和反向代理以代理对象为区分，正向代理代理的是客户端，而目标服务器对真实的客户端的请求是无感的；反向代理代理的是服务器群（或簇），而客户端对真实处理请求的服务器是无感的。更多信息参见：[反向代理为何叫反向代理？ - 刘志军的回答 - 知乎](https://www.zhihu.com/question/24723688/answer/128105528)。

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