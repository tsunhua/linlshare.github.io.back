---
title: Github 使用笔记
date: 2017-12-02 19:35:00
tags: [Github]
comments: true
---


## 配置Github环境

### 安装Git
参见 [Git 官网](https://git-scm.com/book/zh/v1/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)

### 配置ssh-key

1. 检查ssh-key的设置

 ```
# 第一次安装时没有该目录
$ cd ~/.ssh 
 ```

2. 生成新ssh-key

 ```
# rsa算法，C后面接邮箱账号；表示根据邮箱生成key
$ ssh-keygen -t rsa -C "example@example.com"
 ```
3. 添加ssh-key到Github

 登陆Github-->Account Settings--->SSH Public keys ---> add another public keys

4. 测试

 ```
$ ssh -T git@github.com
 ```

5. 设置用户信息

 ```
## 当电脑只需用到一个Github账号时，可以使用全局的用户信息
$ git config --global user.name "name"//用户名
$ git config --global user.email "example@example.com"//邮箱
 ```

## 当电脑需要多个Github账号切换时

1. 配置第二个账号的ssh-key

 ```
$ssh-keygen -t rsa -C "example@example.com"
$ssh-add ~/.ssh/id_rsa_second
 ```

2. 配置ssh-key到github
3. 修改~/.ssh/config文件

 ```
#默认的github
Host github.com
  HostName github.com
  IdentityFile ~/.ssh/id_rsa
#第二个github
Host github_second
  HostName github.com
  IdentityFile ~/.ssh/id_rsa_second
 ```

4. 使用别名pull/push代码

 ```
 git clone git@github_second:username/reponame
 ```

5. 取消global用户信息

 ```
git config --global --unset user.name
git config --global --unset user.email
 ```

6. 每次commit前都要执行下面代码

 ```
git config  user.email "example@example.com"
git config  user.name "name"
 ```

## 生成<github_name>.github.io博客

1. 登录github，创建一个repository，命名为<github_name>.github.io
2. 进入项目，点击Settings，在Github Pages部分点击Launch automatic page generator，下一步，下一步就可以了。
3. 通过http://<github_name>.github.io访问你的静态站点

## 使用git提交更新

>Tip：当对git命令不熟悉时，可以通过`$git xxx --help`查看帮助文档，注意xxx代表不熟悉的命令，比如clone。也可以查看[廖雪峰的Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)和[Git官网](https://git-scm.com/book/zh/v2)学习。

1. 克隆远程仓库

 ```
# 电脑中只有一个github账号时
git clone https://github.com/username/reponame
# 电脑中有两个个github账号时，github_second表示第二个账号的host
git clone git@github_second:username/reponame
 ```

2. 移除所有文件

 ```
# 进入仓库文件夹（工作区）
$cd reponame
# 强制移除所有文件
$git rm -rf .
 ```

3. 添加更新到仓库暂存区中

 可以复制要添加的文件到仓库文件夹（工作区）中，然后执行命令
`$git add *`

4. 提交更新到本地仓库

 ```
$git commit -a -m "注释"
 ```

5. 提交更新到远程服务器

 ```
# origin表示源仓库
$git push origin master
 ```

## 分支管理

### 新建分支

```
# 切换到分支newbranch，-b表示没有就创建（branch/build）
$git checkout -b newbranch
# 创建文件hello
$touch hello
# 添加到暂存区
$git add hello
# 提交到本地仓库
$git commit -m "add a file hello"
# 提交到远程仓库的newbranch分支，-u表示set-upstream
$git push -u origin newbranch
```

### 设置默认分支

在项目的Settings-->Branches下可以修改Default branch
修改默认分支，其实就是把版本库的头指针HEAD
指向了其他分支，通过`$git branch -r`（r表示remotes）可以查看所有远程分支和默认分支。

### 合并分支

```
$git merge newbranch
```

### 切换分支

```
# 切换到master分支工作
$git checkout master
```

### 删除分支

Git在删除分支时为避免数据丢失，默认禁止删除尚未合并的分支。所以，如果分支尚未合并，使用`$git branch -d newbranch`会报错。
使用`$git branch -D newbranch`可以强制删除分支。

### 删除远程分支

```
$git push origin --delete testbranch
```

## 使用github协同开发

### 开发者fork源仓库

每个开发者都从源仓库中Fork代码，然后独立开发。完了，在pull request合并到源仓库中。

### 把开发者仓库clone到本地

```
# 电脑中只有一个github账号时
git clone https://github.com/username/reponame
# 电脑中有两个个github账号时，github_second表示第二个账号的host
git clone git@github_second:username/reponame
```

### 构建功能分支进行开发

```
>>> git checkout develop
# 切换到`develop`分支

>>> git checkout -b feature-discuss
# 分出一个功能性分支

>> touch discuss.js
# 假装discuss.js就是我们要开发的功能

>> git add .
>> git commit -m 'finish discuss feature'
# 提交更改

>>> git checkout develop
# 回到develop分支

>>> git merge --no-ff feature-discuss
# 把做好的功能合并到develop中

>>> git branch -d feature-discuss
# 删除功能性分支

>>> git push origin develop
# 把develop提交到自己的远程仓库中
```

### pull request

点击绿色按钮pull request，将自己仓库的分支合并到源分支中

### 管理员测试、合并

1. review代码

```
>> git checkout develop
# 进入他本地的develop分支

>> git checkout -b khhhshhh-develop
# 从develop分支中分出一个叫khhhshhh-develop的测试分支测试我的代码

>> git pull https://github.com/khhhshhh/practice.git develop
# 把我的代码pull到测试分支中，进行测试
```
2. 合并分支

```
>> git checkout develop
>> git merge --no-ff khhhshhh-develop
>> git push origin develop
```