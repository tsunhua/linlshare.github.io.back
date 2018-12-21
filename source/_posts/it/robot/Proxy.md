---
title: Proxy
date: 2018-12-20 16:33:35
tags: [Proxy,Charles]
---

## 代理（Proxy）

代理是一种提供客户端与服务器进行非直接链接的服务。提供代理服务的服务器称之为**代理服务器**（Proxy Server）。而持有资源实体的服务器称之为**源服务器**。

## 代理服务器分类

### 按代理协议

- HTTP 代理：应用层协议代理，支持访问未加密站点
- SOCKS 4/5 代理：会话层协议代理，SOCKS 5 支持所有底层使用 TCP 和 UDP 的网络应用代理，SOCKS 4 仅支持 TCP 代理。另外 SOCKS 5 还支持 IPv6。
- TLS / SSL 代理：应用层协议代理，支持访问加密站点
-  POP3 / SMTP 代理：邮件服务代理
- FTP 代理：文件传输代理
- ...

### 按匿名程度

以下四种代理的区别是由于代理服务器配置的 `REMOTE_ADDR`、` HTTP_VIA` 和 `HTTP_X_FORWARDED_FOR` 值不同导致。当没有使用代理时仅  `REMOTE_ADDR` 是有值的。

- 高度匿名代理（Elite proxy或High Anonymity Proxy）

  高匿代理其实就是修改请求头，将 `HTTP_VIA` 与 `HTTP_X_FORWARDED_FOR` 属性删除，服务器由此误认为客户端没有使用代理。

  ```json
  REMOTE_ADDR = Proxy IP
  HTTP_VIA = not determined
  HTTP_X_FORWARDED_FOR = not determined
  ```

- 匿名代理（Anonymous Proxy）

  ```json
  REMOTE_ADDR = proxy IP
  HTTP_VIA = proxy IP
  HTTP_X_FORWARDED_FOR = proxy IP
  ```

- 透明代理（Transparent Proxy）

  ```json
  REMOTE_ADDR = Proxy IP
  HTTP_VIA = Proxy IP
  HTTP_X_FORWARDED_FOR = Your IP
  ```

- 混淆代理（Distorting Proxy）

  ```json
  REMOTE_ADDR = Proxy IP
  HTTP_VIA = Proxy IP
  HTTP_X_FORWARDED_FOR = Random IP address
  ```

安全性上：高匿 > 混淆 > 匿名 > 透明。

## 抓包工具

### Charles

#### 开始之前

1. 安装 JDK 环境；
2. 前往[官网](https://www.charlesproxy.com/download/)下载安装适合操作系统的 Charles；
3. 如系统使用了 VPN 等软件，先暂时关闭。

#### 快速开始

1. 启用 HTTP 代理

   在菜单栏选择 `Proxy -> Mac OS X Proxy` 启动 HTTP 代理，如无效，可以直接打开 `系统偏好设置 -> 网络 -> 进阶 -> 代理服务器`，然后设置为 `127.0.0.1:8888` 即可。

2. 启用 SSL 代理

   - 在菜单栏选择 `Help -> SSL Proxying -> Install Charles Root Certificate` 然后在系统的**钥匙串访问**中，**双击这个证书**，然后展开**信任**一栏，在 SSL 一栏选择**始终信任**；
   - 然后在 `Proxy -> SSL Proxying Settings -> add` 添加 Host 为 `*`，Port 为 `443`，这样就可以针对所有站点进行 SSL 代理了。 

## 参考

1. [代理服务器 - 维基百科](https://zh.wikipedia.org/wiki/%E4%BB%A3%E7%90%86%E6%9C%8D%E5%8A%A1%E5%99%A8)
2. [proxy代理类型:透明代理 匿名代理 混淆代理和高匿代理 - gohom.win](http://gohom.win/2016/01/20/proxy-type/)
3. [透明代理和匿名代理的区别 - cnblogs.com](https://www.cnblogs.com/sanler/p/7250660.html)
4. [Mac下使用charles遇到的问题以及解决办法 - cnblogs.com](https://www.cnblogs.com/season-huang/p/6269841.html)
5. [Charles 从入门到精通 - devtang.com](https://blog.devtang.com/2015/11/14/charles-introduction/)