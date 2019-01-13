---
title: Redis
date: 2018-10-28 23:02:00
tags: [Java,Redis]
comments: true
---

## 安装

### （1）Mac HomeBrew

```shell
$ brew install redis
```

### （2）按部就班

```shell
# 需要前置依赖 make、gcc
$ sudo apt-get update
$ sudo apt install make
$ sudo apt-get install gcc

# 下载、解压和编译 redis
$ wget http://download.redis.io/releases/redis-5.0.3.tar.gz
$ tar xzf redis-5.0.3.tar.gz
$ cd redis-5.0.3
$ make
```

## 命令行使用

### 启动 Redis Server

```shell
$./src/redis-server
```

### 进入 Redis 命令行

（1）本地连接

```shell
$redis-cli
redis 127.0.0.1:6379>
redis 127.0.0.1:6379> PING

PONG
```

（2）远程连接

```shell
$ redis-cli -h host -p port -a password
```

### 取键相关命令

```shell
# 查找所有 key
> KEYS *
# 查找符合给定正则的 key
> KEYS pattern

# 删除某个 key
> DEL a_key
# 检查某个 key 是否存在
> EXISTS a_key
# 以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。
> TTL a_key
# 获知某个 key 存储值的类型
> TYPE a_key

# 获取Size，可能会把失效的也计算在内
> DBSIZE
```

### 取值相关命令

```shell
# 设置指定 key 的值
> SET a_key a_value
# 获取指定 key 的字符串值
> GET a_key
```

> 注意：对有标点符号的 key，要用双引号（“”）包裹，否则会返回 `nil` 。比如使用 Spring Cache + Redis 时，会序列化缓存方法返回值，这是的 KEY 就要用双引号括起来，示例如下，
>
> `GET "cache1:\xac\xed\x00\x05t\x00$cb5775e6-1b39-4f63-85c8-13f134a54f32"`

### List 相关命令

```shell
# 获知列表长度
> Llen a_key
# 获取列表指定范围内的元素。其中 0 表示列表的第一个元素， 1 表示列表的第二个元素，以此类推。 你也可以使用负数下标，以 -1 表示列表的最后一个元素， -2 表示列表的倒数第二个元素，以此类推。
> Lrange a_key start end

# 对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。
> Ltrim a_key start end

# 移除并返回列表的最后一个元素。
> Lpop a_key
```

### 删库跑路相关命令

```shell
# 删除所有数据库的所有key
> FLUSHALL
# 删除当前数据库的所有key
> FLUSHDB
```

## Jedis 使用

### 快速开始

（1）引入依赖

```groovy
compile "redis.clients:jedis:3.0.0"
```

（2）简单使用

```java
Jedis jedis = new Jedis("localhost");
jedis.set("foo", "bar");
String value = jedis.get("foo");
```

## 排错

### 编译安装时出现：jemalloc/jemalloc.h: No such file or directory

```
make MALLOC=libc
```

### 编译安装时出现：cc adlist.o /bin/sh:1:cc:not found

 缺少 gcc 环境，安装 gcc 即可。

### 服务器连接 Redis 失败

（1）背景

服务器部署 Redis ，代码部署在另一主机，调用 Redis 时发生以下异常：

```
Caused by: redis.clients.jedis.exceptions.JedisDataException: DENIED Redis is running in protected mode because protected mode is enabled, no bind address was specified, no authentication password is requested to clients. In this mode connections are only accepted from the loopback interface. If you want to connect from external computers to Redis you may adopt one of the following solutions: 
1) Just disable protected mode sending the command 'CONFIG SET protected-mode no' from the loopback interface by connecting to Redis from the same host the server is running, however MAKE SURE Redis is not publicly accessible from internet if you do so. Use CONFIG REWRITE to make this change permanent.
2) Alternatively you can just disable the protected mode by editing the Redis configuration file, and setting the protected mode option to 'no', and then restarting the server.
3) If you started the server manually just for testing, restart it with the '--protected-mode no' option. 
4) Setup a bind address or an authentication password. NOTE: You only need to do one of the above things in order for the server to start accepting connections from the outside.
```

（2）原因

查看 `redis.conf` 发现 `bind 127.0.0.1` ，意味着只有部署 Redis 的机器 可以正常访问 Redis。

还有 Redis 运行在保护模式下。

（3）解决方案

方案一：配置 bind 地址为内网的 IP 地址，修改 `protected-mode no` ，然后重启 Redis。

## 参考

1. [Download - redis.io](https://redis.io/download)
2. [Redis 命令参考 - redis.net](http://www.redis.net.cn/order/)