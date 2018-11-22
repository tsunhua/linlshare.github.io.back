---
title: Gradle
date: 2018-11-7 19:44:00
tags: [Gradle]
comments: true

---

### 使用初始化配置

（1）场景

当需要所有的 gradle 项目都进行同样的配置时。

（2）过程

1. 在 `USER_HOME/.gradle` 目录创建名为 `init.gradle`的文本文件；
2. 在 `init.gradle` 编写初始化脚本。

（3）案例：配置自建的 Maven 私有服务器

```groovy
// init.gradle
allprojects {
 
  ext.RepoConfigurator = {
    maven {
      url 'http://maven-xx-inc.com/repository/maven-public/'
    }
    maven {
      url 'http://nexus.mobisummer-inc.com/nexus/content/groups/public'
    }
  }
 
  buildscript.repositories RepoConfigurator
  repositories RepoConfigurator
}
```



