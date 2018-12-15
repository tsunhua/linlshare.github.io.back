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

也可以自己到 [MongoDB 的下载中心](https://www.mongodb.com/download-center/community) 下载对应的系统和版本，如果是 Linux 的话可以使用 `wget` 下载：

```shell
wget "https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-4.0.4.tgz"
```

并配置环境变量，如下：

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

## 基本概念

### BSON

MongoDB 的文件存储格式为 BSON，所谓 BSON，即是 Binary JSON，为 JSON 文档对象的二进制编码格式，其扩展了 JSON 的数据类型，支持浮点数和整数的区分，支持日期类型，支持直接存储 JS 的正则表达式，支持 32 位和 64 位数字的区分，支持直接存储 JS 函数等等。看起来 BSON 对于 JS 还是挺友好的呵。

> 注意：从 Shell 终端中输入的数值都会被存储为 64 位浮点类型。

### 文档（Document）

一个文档就相当于关系型数据库中的**行**的概念，由多个键值有序组成，格式为 BSON。示例如下：

```json
{ "_id" : ObjectId("5c05e74a65a27abc9a619f8a"), "a_key" : "a_value" }
```

其中 `_id` 是系统自动生成的键，当然也可以在创建时自定义值。

### 集合（Collection）

一个集合就相当于关系型数据库中的**表**的概念，由多个文档组成。集合中的默认索引为 `_id` ，可以新建其他键的索引来优化查询，MongoDB 支持单字段索引（Single Field Index）、复合索引（Compound Index）以及多键索引（Multikey Index）等等，可以根据需求进行选用。

### 数据库（Database）

多个集合组成一个数据库，不同的数据库之间文件是隔离的。单个 MongoDB 实例可以容纳多个独立数据库。默认系统存在以下的保留数据库：

1. admin：用户权限相关
2. local：存储限于本地的集合
3. config：分片配置相关

## Shell 操作

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

### docuement 级别

```shell
# 插入文档
> db.a_collection.insert({
    "a_key": "a_value",
    "b_key": 100,
    "c_key": true
})

# 列出所有文档，并美化
> db.a_collection.find().pretty()
# MongoDB AND 且过滤器
> db.a_collection.find({
    "a_key": "a_value",
    "c_key": true
})
# MongoDB OR 或过滤器
> db.a_collection.find({
   $or:[
       { "a_key": "a_value"},
       { "a_key": "another_value" }
   ]
})
# MongoDB 投影，只返回指定的字段
> db.a_collection.find({},{"a_key", "c_key"})

# 更新单个文档
db.a_collection.update({"a_key": "a_value"},{$set:{"a_key": "another_value"}})
# 更新多个文档
db.a_collection.update({"a_key": "a_value"},{$set:{"a_key": "another_value"}},,{multi: true})

# 删除文档
db.a_collection.remove({"a_key": "a_value"})

# 后台执行创建单一复合索引操作
db.a_collection.createIndex({"a_key": 1,"c_key": -1},{unique: true,background: true})
# 查询所有索引
db.a_collection.getIndexes()
```

##  Java 操作

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

参考：[为什么 MongoDB 连接数被用满了？ - mongoing.com](http://www.mongoing.com/archives/3145)

## 参考

1. [MongoDB Driver Quick Start - mongoDB](https://mongodb.github.io/mongo-java-driver/3.9/driver/getting-started/quick-start/)
2. [MongoDB学习（二）：数据类型和基本概念 - Hejin.Wang](https://www.cnblogs.com/egger/archive/2013/04/27/3047191.html)
3. [MongoDB索引原理 - mongoing.com](http://www.mongoing.com/archives/2797)

