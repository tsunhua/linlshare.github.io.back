---
title: ADSL ProxyPool
date: 2019-05-10 15:12:24
tags: [ADSL,ProxyPool]
---

## 1 原理

### 1.1 ADSL 技术

**ADSL** (**A**symmetric **D**igital **S**ubscriber **L**ine，非对称数字用户线路) 技术。

采用频分多路复用技术把普通电话线分成电话、上行和下行三个相对独立的信道，信道之间不会互相干扰。

技术局限：

1. 在不影响正常通话通信情况下，上行速度最高 3.5Mbps
2. 在不影响正常通话通信情况下，下行速度最高 24Mbps
3. 电信服务提供商接入设备和用户终端之间的距离不能超过 5 公里

网络登录标准：

| 标准  | 含义                                                         | 是否提供动态 IP |
| ----- | ------------------------------------------------------------ | --------------- |
| STM   | Synchronous Transport Module，同步传输模块                   | 否              |
| ATM   | Asynchronous Transfer Mode，异步传输模式                     | 否              |
|       | Packet Transfer Mode (始于 ADSL 2版本，如下)                 |                 |
| PPPoA | **P**oint-to-**P**oint **P**rotocol **o**ver **A**TM，基于 ATM 的端对端协议 | 是              |
| PPPoE | **P**oint-to-**P**oint **P**rotocol **o**ver **E**thernet，基于以太网的端对端协议 | 是              |

PPP，点对点协议：

工作在数据链路层，在两个节点之间创建直接的连接，并提供认证、传输加密和压缩功能。

PPP 应用：

1. 宽带接入，拨号接入，使用 PPPoE 和 PPPOA 协议解决 IP 报文在没有数据链路协议时在调制调节器线路传输的问题；
2. 物理网络，包括串口线、电话线、移动电话和光纤等。

> 宽带（Broadband）：
>
> 1. OECD2006年报告称，任何传输速率在256Kbps以上的互联网连接，可称为宽带。
> 2. FCC解释，任何传输速率在4Mbps以上的互联网连接，可称为宽带。

PPPoE：

将 PPP 协议封装在以太网框架中的一种网络隧道协议。

- 有登录和口令，方便计费；
- 当连接时分配 IP 地址，方便 IP 地址动态复用。

### 1.2 动态拨号 VPS

VPS (Virtual Private Server，虚拟专用服务器)，将一台服务器分割成多个虚拟专享服务器的服务。

动态拨号 VPS 是基于 ADSL 拨号上网的虚拟专用主机。

不同的动态拨号 VPS 提供商提供不同的配置方法：

| 动态拨号 VPS 提供商                               | 配置方式                                                     |
| ------------------------------------------------- | ------------------------------------------------------------ |
| [云立方](http://www.yunlifang.cn/dynamicvps.asp)  | 1. 运行 `ppp.sh` 脚本，输入用户名密码即可；<br/>2. `adsl-start ` 进行开始拨号，网卡为 ppp0；<br/>3. `adsl-stop` 停止拨号。 |
| [无极](http://cloud.871020.com/vpsadm/pppoe.html) | 1. 运行 `p.sh` 脚本，输入用户名密码即可；<br/>2. `pppoe-start` 拨号；<br/>3. `pppoe-stop` 停止拨号；<br/> 4. `pppoe-status` 查看拨号状态。 |

## 2 设置代理服务器

在 Linux 下搭建代理服务器有两种简单的方式，TinyProxy 和 Squid。

### 2.1 TinyProxy

#### （1）安装

```shell
# 在 Ubuntu 下
apt-get install -y epel-release
apt-get update -y
apt-get install -y tinyproxy
```

#### （2）配置

找到 `/etc/tinyproxy/tinyproxy.conf` 文件，将 `Allow 127.0.0.1`  改为允许外部连接的主机，如想任意主机都能连，只需要注释掉即可，即 `# Allow 127.0.0.1`。

保存配置后，需要重启才能生效，即执行 `service tinyproxy start`。

#### （3）验证

```shell
curl -x <主机地址> httpbin.org/get
```

## 3 动态获取 IP

使用一台远程主机作为接口，接收并保存拨号主机的 IP 变更，然后对外提供代理接口。具体时序图如下：

![自建代理时序图](ADSL ProxyPool/自建代理时序图.jpg)

