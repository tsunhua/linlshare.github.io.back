---
title: MongoDB 使用笔记
date: 2018-11-09 11:45:03
tags: [Java,MongoDB]
comments: true
---

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

## 连接

（1）本地连接

```shell
$ mongo
```

（2）远程连接

```shell
$ mongo
> mongodb://admin:123456@localhost/
```

## 操作数据库

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



