---
title: Java 后端基础之 3. JSP
date: 2017-12-29 18:56:00
tags: [Java,JSP]
comments: true
---

### JSP

JavaServer Pages，是一门脚本语言，用于动态生成HTML、XML等。可以混合Java进行编程。

运行于JSP容器中，流行的有Tomcat、Jetty。

JSP的解析过程：JSP —>Servlet

#### SUN公司的历史

全称：Stanford University Network

| year | event            |
| ---- | ---------------- |
| 1982 | 创立               |
| 1986 | 纳斯达克上市           |
| 1995 | 开发了Java技术，由JCP维护 |
| 2009 | 被Oracle收购        |

#### JSP 规范

|         | 规范      | 发布时间 |
| ------- | ------- | ---- |
| JSP 1.2 | JSR-53  | 2001 |
| JSP 2.0 | JSR-152 | 2003 |
| JSP 2.3 | JSR-245 | 2006 |

#### JSP处理

JSP 引擎从磁盘加载 JSP 页面并将其转换为一个 servlet 的内容。这种转换是非常简单的，所有模板文本转换为 println()语句，所有 JSP 元素转换为 Java 代码实现页面的相应的动态行为。

JSP 引擎编译 servlet 到一个可执行的类中，并将原始请求转发给一个 servlet 引擎。

#### JSP基本语法

JSP程序，由`<%` 开始，`%>`结束。

输出方式：

1. 使用内置的java对象

   ```Jsp
   <%
   	out.println("xxx");
   %>
   ```

2. 使用JSP表达式

   ```jsp
   <%="xxx"%>
   ```

   ​