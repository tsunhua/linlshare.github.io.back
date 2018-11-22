---
title: SSH
date: 2018-9-26 17:22:00
tags: [SSH]
comments: true
---


## SSH 背景

- 解决的问题：明文登录信息暴露问题。

- 历史：
  1. 1995 年，芬兰赫尔辛基理工大学的 **Tatu Ylonen** 发现自己学校存在嗅探密码的网络攻击，于是开发了 SSH （**Secure Shell**）通信安全协议，用于加密登录，并随后以免费软件形式发布，并创办 SSH 通信安全公司来继续开发和销售SSH。
  2. 截至2005年，**OpenSSH** 是唯一一种最流行的SSH实现，而且成为了大量操作系统的默认组件。

## SSH 原理

核心：**非对称加密**。

整个过程是这样的：

1. 远程主机收到用户的登录请求，把自己的公钥发给用户。
2. 用户使用这个公钥，将登录密码加密后，发送回来。
3. 远程主机用自己的私钥，解密登录密码，如果密码正确，就同意用户登录。

## 口令登录

用户使用 `ssh user@host` 登录远程主机时，系统会提示远程主机的公钥指纹，当用户确认接收该公钥指纹时，会保存到 `$HOME/.ssh/known_hosts` 中，下次登录时会跳过。

> 公钥指纹：公钥长度较长（这里采用RSA算法，长达1024位），很难比对，所以对其进行MD5计算，将它变成一个128位的指纹。上例中是 `98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d`，再进行比较。

## 公钥登录

所谓"公钥登录"，原理很简单，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。

用户将自己的公钥储存在远程主机的具体过程如下：

1. 使用 `ssh-keygen` 在本地目录 `$HOME/.ssh/` 生成公钥 `id_rsa.pub` 和密钥 `id_rsa` ；

2. 使用 `ssh-copy-id user@host` 将公钥上传至远程主机 host 中，实现细节是：

   ```shell
   $ ssh user@host 'mkdir -p .ssh && cat >> .ssh/authorized_keys' < ~/.ssh/id_rsa.pub
   ```

## SSH CONFIG FILE

可以通过配置文件快速进行 ssh 登录，配置文件有两种：

1. 用户配置文件 `~/.ssh/config`；
2. 系统配置文件 `/etc/ssh/ssh_config)`。

可配置项有：

- **Host**，配置标识，使用 `ssh [Host]` 进行快速登录；
- **HostName**，服务器所在地址，可以是域名或 IP；
- **User**，进行 ssh 登录的用户名；
- **Port**，指定服务器端口；
- **IdentityFile**，指定私钥文件的位置。

如在 `~/.ssh/config`文件中按如下配置，则可以通过 `ssh xx-server` 进行快速 ssh 登录。

```
Host xx-server
    HostName ssh.xx-server.com
    User xx_user
    Port 2200
    IdentityFile ~/.ssh/local_id_rsa
```

## 名词解释

| 名词           | 含义                                                         |
| -------------- | ------------------------------------------------------------ |
| X.509          | 一种通用的**证书格式**，包含证书持有人的公钥，加密算法等信息 |
| pkcs1 ~ pkcs12 | 公钥加密（非对称加密）的一种标准，一般存储为 `*.pN`，`*.p12` 是包含证书和密钥的封装格式 |
| *.der          | 证书的**二进制存储格式**（不常用）                           |
| *.pem          | 证书或密钥的 **Base64 文本存储格式**，可以单独存放证书或密钥，也可以同时存放 |
| *.key          | **单独存放的 pem 格式的密钥**，一般保存为 `*.key`            |
| *.cer *.crt    | 两个指的都是证书，Linux 下叫 crt，Windows 下叫 cer；存储格式可以是 `pem` 也可以是 `der` |
| *.pfx          | 微软 IIS 的实现                                              |
| *.jks          | Java Keytool 实现的证书格式                                  |

## 排错

### SSH 一直登陆不上，又没显示错误信息

在 ssh 命名最后添加 -v/-vv 输出连接信息。

## 参考

- [Secure Shell - 维基百科](https://zh.wikipedia.org/zh-cn/Secure_Shell)
- [SSH原理与运用（一）：远程登录](http://www.ruanyifeng.com/blog/2011/12/ssh_remote_login.html)
- [SSH CONFIG FILE - ssh.com](https://www.ssh.com/ssh/config/)
- [SSL中，公钥、私钥、证书的后缀名都是些啥？ - 海棠依旧的回答 - 知乎](https://www.zhihu.com/question/29620953/answer/242467271)

