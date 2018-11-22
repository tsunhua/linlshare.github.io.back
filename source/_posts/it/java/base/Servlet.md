---
title: Servlet
date: 2017-12-29 14:55:00
tags: [Java,Servlet]
comments: true
---


### Servlet

Server + Applet，服务端小程序

#### 编写流程

1. 写一个类，继承自HttpServlet，重写init和destroy方法和service方法
2. 在`web.xml`中写入`<servlet>` 节点，并完成`<servlet-name>`和`<servlet-class>`子节点
3. 在`web.xml`中写入`<servlet-mapping>`节点，并完成`<servlet-name>`和`<url-pattern>`子节点

#### url-pattern 规则

#### Servlet处理流程

`init() -> service() -> destroy()`

#### Servlet 包结构

| pkg                      | class               | desc |
| ------------------------ | ------------------- | ---- |
| javax.servlet            | Servlet             |      |
|                          | ServletRequest      |      |
|                          | ServletResponse     |      |
|                          | ServletConfig       |      |
|                          | ServletContext      |      |
|                          | GenericServlet      |      |
|                          | ServletInputStream  |      |
|                          | ServletOutputStream |      |
| javax.servlet.http       | HttpServletRequest  |      |
|                          | HttpServletResponse |      |
|                          | HttpSession         |      |
|                          | HttpServlet         |      |
|                          | Cookie              |      |
| javax.servlet.annotation |                     |      |
| javax.servlet.descriptor |                     |      |

