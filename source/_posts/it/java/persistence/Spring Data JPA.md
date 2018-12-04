---
title: Spring Data JPA
date: 2018-12-04 18:02:06
tags: [Java,JPA,Spring]
---

## 用途

### 特性

1. 支持自由替换 Hibernate, EclipseLink, OpenJpa。

## 快速开始

### 依赖引入

```groovy
buildscript {
  repositories {
    mavenCentral()
  }
  dependencies {
    classpath("org.springframework.boot:spring-boot-gradle-plugin:2.0.5.RELEASE")
  }
}


apply plugin: 'java'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

sourceCompatibility = 1.8

repositories {
    mavenCentral()
}

dependencies {
  compile("org.springframework.boot:spring-boot-starter-data-jpa")

}
```



