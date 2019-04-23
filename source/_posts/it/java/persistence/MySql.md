---
title: MySql
date: 2019-04-23 11:35:09
tags: [Java,MySql]
comments: true
---

## 安装

mac下安装mysql

```Shell
brew install mysql
```

## 启动和停止

```shell
# 启动
mysql.server start

# 停止
mysql.server stop
```

## 修改密码

```shell
mysqladmin -uroot password "new_password"
```

## 登录

```shell
mysql -uroot -p
```

## 备注

1. 默认的端口号为 3306
2. Mac下简单易用的MySql管理工具：Sequel Pro

## QA

### [Sequel Pro and MySQL connection failed](https://stackoverflow.com/questions/51179516/sequel-pro-and-mysql-connection-failed)

**问题描述：**

```
MySQL said: Authentication plugin 'caching_sha2_password' cannot be loaded: dlopen(/usr/local/lib/plugin/caching_sha2_password.so, 2): image not found
```

**解决方案：**

This is because Sequel Pro is not ready yet for a new kind of user login, as the error states: there is no driver. Quick fix for non-homebrew installs:

`Apple Logo > System Preferences > MySQL > Initialize Database`, then type your new password and select 'Use legacy password'

After restart you should be able to connect. Do it only on fresh installs, because you may lost your db tables otherwise.