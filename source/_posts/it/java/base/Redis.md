---
title: Redis
date: 2018-10-28 23:02:00
tags: [Java,Redis]
comments: true
---

## 安装

```shell
$ brew install redis
```

## 使用

### 启动 Redis Server

```shell
$./redis-server
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
```

### 取值相关命令

```shell
# 设置指定 key 的值
> SET a_key a_value
# 获取指定 key 的字符串值
> GET a_key
```

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

## 参考

- [Redis 命令参考](http://www.redis.net.cn/order/)