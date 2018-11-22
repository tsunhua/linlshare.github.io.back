---
title: Selenium
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

## Headless Chrome

没有 UI 界面的 Chrome 浏览器，便于进行自动化测试和在服务端环境运行，支持所有现代 Web 平台的特性。可参见 [官网](https://developers.google.com/web/updates/2017/04/headless-chrome) 了解更多。

### 支持情况

Chrome 59+

### CLI（Command Line Interface）

```shell
{chrome} \
  --headless \                   # 以 Headless 模式运行 Chrome
  --disable-gpu \                # 运行在带视窗的环境中时暂时需要该 Flag.
  --remote-debugging-port=9222 \
  https://www.chromestatus.com   # 要打开的 URL, 默认为 about:blank.
```

上述的 `{chrome}` 在 Mac 中为 `/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome` ，

在 Windows 中为 `/path/to/chrome.exe`。

建议设置 `alias` 将具体的路径对应到 `chrome` 命令，如下：

```shell
## 编辑 .bash_profile
vim ~/.bash_profile
## 末尾追加
alias chrome='/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome'
## 保存退出
:wq
## 使 .bash_profile 立即生效
source ~/.bash_profile
```

### 调试 Headless Chrome

执行以下命令

```shell
chrome --headless --disable-gpu --remote-debugging-port=9222 https://www.github.com
```

然后在 Chrome 浏览器窗口打开 `http://localhost:9222`  就可以使用 `dev-tools` 进行远程调试了。

### 在 Selenium 中使用 Headless Chrome

**（1）Java 环境下**

```java
ChromeOptions options = new ChromeOptions();
options.addArguments("headless");
options.addArguments("window-size=1200x600");
WebDriver driver = new ChromeDriver(options);
driver.get("http://www.github.com");
```

**（2）Python3 环境下**

```python
import os  
from selenium import webdriver  
from selenium.webdriver.common.keys import Keys  
from selenium.webdriver.chrome.options import Optionschrome_options = Options()  
chrome_options.add_argument("--headless")
chrome_options.binary_location ='/usr/bin/google-chrome-stable'
chrome_options.add_argument('--no-sandbox')driver = webdriver.Chrome(chrome_options=chrome_options)
driver.get('https://www.github.com')
```

