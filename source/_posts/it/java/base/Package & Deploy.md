---
title: Package & Deploy
date: 2018-12-15 14:37:41
tags: [Java,JavaEE,Package,Deploy]
---

## 开发测试阶段的打包部署

- [ ] 配置登录远程机器的 SSH config
- [ ] 本地使用 Gradle 或者 Maven 打包
- [ ] 使用 scp 安全拷贝 jar 包到远程机器
- [ ] 使用 `sudo apt install openjdk-8-jre-headless` 安装 JDK 环境
- [ ] 使用 `java -jar` 完成部署

## 分布式打包部署

- [ ] 部署 docker 环境
- [ ] 在 docker 中部署 etcd
- [ ] 部署 kafka
- [ ] 部署应用服务

## 排错

### [Can't execute jar- file: “no main manifest attribute”](https://stackoverflow.com/questions/9689793/cant-execute-jar-file-no-main-manifest-attribute)

在 Linux 上运行 jar 包时出现无法执行的错误，原来是没有配置主类，解决方案如下：

```groovy
apply plugin: 'java'

jar {
    manifest {
        attributes 'Main-Class': 'com.package.app.Class'
    }
}
```

### 打出的包不包含任何第三方依赖

解压打出来的 jar 包发现里面没有第三方依赖无法运行，这时候有两种解决方案：

（1）把第三方的 jar 包收集放到一个 class path 目录，然后就可以正常启动了。不过这种方法很笨，因为现在的依赖都是通过 gradle 或者 maven 引入，手动去找这些 jar 包并放到服务器上的 class path 目录，这很费劲，不过好处是打出的包很小，上传很快。

（2）使用 [shadow](https://imperceptiblethoughts.com/shadow/) 将所有依赖合并成一个 jar 包，这很神奇。

```groovy
buildscript {
  repositories {
    maven {
      url "https://plugins.gradle.org/m2/"
    }
  }
  dependencies {
    classpath "com.github.jengelman.gradle.plugins:shadow:4.0.3"
  }
}

apply plugin: 'java'
apply plugin: "com.github.johnrengelman.shadow"

shadowJar {
  manifest {
    attributes 'Main-Class': 'com.example.Main'
  }
}
```