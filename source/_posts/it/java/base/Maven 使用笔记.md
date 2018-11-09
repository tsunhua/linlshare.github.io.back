---
title: Maven 使用笔记
date: 2017-12-29 19:34:00
tags: [Java,Maven]
comments: true
---

### Maven

项目管理工具

- 构建项目（Builds）
- 依赖管理（Dependencies）
- 配置管理（SCMs）
- 发布管理（Release）
- 文档编制（Documentation）
- 报告（Reporting）

特点：

- 微内核（只解析XML，其他由Maven插件处理）
- 约定优于配置
- 定义项目模型

安装：

```Shell
brew install maven
mvn -version
```

创建Maven项目

```shell
mvn archetype:generate -DgroupId=org.flyne.demo -DartifactId=maven-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
# 这是一个goal
```

项目结构

```
maven-demo
|– pom.xml
`– src
|– main
| `– java
|　　　　`– org
|　　　　　　`– flyne
|　　　　　　　　`– demo
|　　　　　　　　　　`– App.java
`– test
`– java
`– org
`– flyne
`– demo
`– AppTest.java
```

POM文件，项目对象模型（Project Object Model）

项目的核心配置文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">  
  <modelVersion>4.0.0</modelVersion>  
  <groupId>com.tiantian.mavenTest</groupId>  
  <artifactId>projectB</artifactId>  
  <version>1.0-SNAPSHOT</version>  
  <packaging>jar</packaging>  
   
  <dependencies>  
    <dependency>  
      <groupId>junit</groupId>  
      <artifactId>junit</artifactId>  
      <version>3.8.1</version>  
      <scope>test</scope>  
              <optional>true</optional>  
    </dependency>  
  </dependencies>  
</project>  
```

构建项目

```Shell
mvn package
# 这是一个phase
mvn compile
# 这也是一个phase，包含如下的阶段：
# 1. validate
# 2. generate-sources
# 3. process-sources
# 4. generate-resources
# 5. process-resources
# 6. compile
```

Maven常用阶段（phase）

- validate：验证项目是否正确，所有必须的信息是否可用
- compile：编译项目的源码
- test：使用单元测试框架对编译后的源代码逆行测试
- package：接受编译好的代码，打包成可发布的格式，如jar
- verify：运行任何检查，验证包是否有效且达到质量标准
- install：将包安装到Maven本地仓库，供本地其他Maven项目使用
- deploy：将最终的包复制到远程仓库，供其他开发人员和Maven项目使用
- clean：清理上一次构建生成的文件
- site：生成项目站点文档