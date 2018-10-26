---
title: Java 后端基础之 7. MySql
date: 2018-1-6 12:02:00
tags: [Java,MySql]
comments: true
---


### Mysql

#### 安装

mac下安装mysql

```Shell
brew install mysql
```

#### 启动和停止

```shell
# 启动
mysql.server start

# 停止
mysql.server stop
```

#### 修改密码

```shell
mysqladmin -uroot password "new_password"
```

#### 登录

```shell
mysql -uroot -p
```

#### 备注

1. 默认的端口号为 3306
2. Mac下简单易用的MySql管理工具：Sequel Pro