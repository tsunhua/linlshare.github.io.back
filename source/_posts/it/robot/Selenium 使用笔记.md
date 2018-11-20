---
title: Selenium 使用笔记
date: 2018-11-20 17:18:59
tags: [Java,Robot,Selenium]
comments: true
---

## 安装 Selenium

**（1）Java 编程环境下**

针对 Gradle

```groovy
compile 'org.seleniumhq.selenium:selenium-java:RELEASE'
```

 针对 Maven

```xml
<dependency>
    <groupId>org.seleniumhq.selenium</groupId>
    <artifactId>selenium-java</artifactId>
    <version>RELEASE</version>
</dependency>
```

**（2）Python 编程环境下**

```shell
pip install selenium
```

## 安装和配置 Driver

### 下载 Driver

不同的操作系统和浏览器有不同的 Driver 需根据需求选取：

- Chrome [driver](https://sites.google.com/a/chromium.org/chromedriver/downloads)
- Edge [driver](https://developer.microsoft.com/en-us/microsoft-edge/tools/webdriver/)
- Firefox [driver](https://github.com/mozilla/geckodriver/releases)
- Safari [driver](https://webkit.org/blog/6900/webdriver-support-in-safari-10/)

### 配置 Driver（以 Mac/Linux 为例）

（1）将下载的 driver zip 压缩包解压

（2）将文件复制到系统的 bin 目录

```shell
sudo cp path/to/driver /usr/local/bin
```

（3）赋予 driver 可执行权限（以chromdriver为例）

```shell
sudo chmod a+x /usr/local/bin/chromdriver
```

> 注意：Driver 的版本是跟 Selenium 相匹配的，如果发现有 NoSuchMethodError，请更新两者版本到最新稳定版。

## 自动生成 Selenium 代码

在 Firefox 中安装插件 [Katalon Recorder](https://addons.mozilla.org/en-US/firefox/addon/katalon-automation-record/) 可以方便地进行录制用户行为，然后导出各个语言环境下的 Selenium 代码。使用路径：Record --> [Some interactions] --> Stop --> Export。