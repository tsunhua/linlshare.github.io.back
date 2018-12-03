---
title: MongoDB
date: 2018-11-09 11:45:03
tags: [Java,MongoDB]
comments: true
---

MongoDB 是一款开源的面向文档的数据库（document database）， [NoSQL](https://zh.wikipedia.org/zh-cn/NoSQL) 中一种，同样使用文档存储实现 NoSQL 的 DB 还有 MarkLogic、OrientDB、CouchDB 等等。

## 安装

Mac 用户可以直接使用 Homebrew 安装，命令如下：

```shell
$ sudo brew install mongodb
```

也可以自己到 [MongoDB 的下载中心](https://www.mongodb.com/download-center/community) 下载并配置环境变量，如下：

```shell
$ export PATH={MONGODB_DIR}/bin:$PATH
```

## 启动

```shell
# {mongo_db_file_path} 为指定的数据库文件存放位置，不支持~符号。如果使用默认位置 /data/db ，也需要先手动创建。
$ mongod --dbpath={mongo_db_file_path}
```

## 终端连接

（1）本地连接

```shell
$ mongo
```

（2）远程连接

```shell
$ mongo
> mongodb://admin:123456@localhost/
```

## 终端操作

### database 级别

```shell
# 列出所有的数据库
> show dbs

# 查看当前使用的数据库
> db

# 切换当前使用的数据库
> use a_db

# 创建数据库
> use new_db

# 删除数据库
> db.dropDatabase()
```

### collection 级别

```shell
# 显示数据库中的所有 collection
> show collections

# 列出 collection 中的所有列
> db.a_collection.find()

# 删除 collection
> db.a_collection.drop()

# 新建 collection
> db.createCollection("new_collection")

# 重命名 collection
> db.a_collection.renameCollection("new_name")

# 清空 collection 中数据
> db.a_collection.drop({})
```

##  Java 版本

### 模块划分

1. bson：高性能的编码解码。
2. mongodb-driver-core：核心库，抽取出来主要是用于自定义 API。
3. mongodb-driver-legacy：兼容旧的 API 的同步 Java Driver。
4. mongodb-driver-sync：只包含 MongoCollection 泛型接口，服从一套新的跨 Driver 的 CRUD 规范。
5. mongodb-driver：mongodb-driver-legacy + mongodb-driver-sync，新项目推荐使用它！
6. mongodb-driver-async：新的异步 API，充分利用 Netty 或者 Java7 的 AsynchronousSocketChannel 已达到快而非阻塞的 IO。
7. mongo-java-driver（uber-jar）：包含 bson,  mongodb-driver-core 和 mongodb-driver。

## Q&A

### 一个服务中该使用一个还是多个 MongoClient？

通常一个服务应使用一个全局的 MongoClient，并且 MongoClient 中已经实现了一个连接池，最大值默认为 1000000 的连接限制，这相当于没有限制。

参考：[为什么 MongoDB 连接数被用满了？](http://www.mongoing.com/archives/3145)

