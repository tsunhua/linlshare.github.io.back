---
title: Git 实战记录
date: 2018-10-17 11:44:00
tags: [Git,版本控制]
comments: true
---


### 提交当前分支的更改到远程的同名分支

```
$ git push origin HEAD
```

### 删除远程分支

```
$ git push origin :dev-sth
# 相当于
$ git push origin --delete dev-sth
```

### 撤销 git commit 但未 git push 的修改

```
#找到想要撤销的 commit id
$ git log
#撤销，但不对代码修改进行撤销，还可以再次 commit
$ git reset <commit id> 
#撤销并忽略该次 commit 的代码修改
$ git reset --hard <commit id>
```

