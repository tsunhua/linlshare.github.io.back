---
title: WebMagic
date: 2018-12-07 10:06:53
tags: [Java,Robot,WebMagic]
---

## WebMagic 是干嘛的？

WebMagic 是一个 Java 平台上的开源爬虫框架，其设计参考了 Scrapy，实现则参考了 HttpClient 和 Jsoup。其由四大组件组成：

1. Downloader，负责下载网页，使用 HttpClient。
2. PageProcessor，负责解析网页和链接发现，使用 Jsoup 和 Xsoup。
3. Scheduler，负责管理待抓取的 URL 和去重。
4. Pipeline，负责结果数据的持久化。

## 快速开始

### （1）依赖引入

```groovy
ext {
  versions = [
    "web_magic": '0.7.3'
  ]
}

dependencies {
  // 这里有自己项目的日志实现
  compile project(':base')

  compile("us.codecraft:webmagic-core:${versions.web_magic}") {
    exclude group: 'org.slf4j', module: 'slf4j-log4j12' // 移除默认的日志实现
  }
  compile("us.codecraft:webmagic-extension:${versions.web_magic}") {
    exclude group: 'org.slf4j', module: 'slf4j-log4j12'
  }
}
```

### （2）快速开始

爬取 https://github.com/code4craft/ 页面上可以发现的所有 Github 仓库信息。

```java

public class GithubRepoPageProcessor implements PageProcessor {

  private Site site = Site.me().setRetryTimes(3).setSleepTime(200).setTimeOut(10000);

  @Override
  public void process(Page page) {
    String regex = "(https://github\\.com/code4craft/([\\w-_]+)/)";
    page.addTargetRequests(page.getHtml()
                               .links()
                               .regex(regex)
                               .all());
    if(!Pattern.matches(regex,page.getUrl().get())){
      //skip this page
      page.setSkip(true);
    }
    page.putField("author", page.getUrl().regex("https://github\\.com/(\\w+)/.*").toString());
    page.putField("name",
                  page.getHtml()
                      .xpath("//meta[@property='og:title']/@content")
                      .toString());
    if (page.getResultItems().get("name") == null) {
      page.setSkip(true);
    }
//    page.putField("readme", page.getHtml().xpath("//div[@id='readme']/tidyText()"));
  }

  @Override
  public Site getSite() {
    return site;
  }

  public static void main(String[] args) {
    Spider.create(new GithubRepoPageProcessor())
          .addUrl("https://github.com/code4craft/")
          .thread(5)
          .run();
  }
}
```

## 更进一步

### Pipeline 接口参数分析

Pipeline 接口会在每个 Page 解析完成之后回调一次。其中的参数如下：

**（1）Task**

```json
{
    "exitWhenComplete": true,
    "pageCount": 0, // 抓取的第几页
    "scheduler": {
        "duplicateRemover": {}
    },
    "site": {
        "acceptStatCode": [
            200
        ],
        "allCookies": {},
        "cookies": {},
        "cycleRetryTimes": 0,
        "disableCookieManagement": false,
        "domain": "github.com",
        "headers": {
            ":method": "GET",
            "origin": "https://github.com"
        },
        "retrySleepTime": 1000,
        "retryTimes": 3,
        "sleepTime": 100,
        "timeOut": 10000,
        "useGzip": true
    },
    "spawnUrl": true,
    "startTime": 1544165065094,
    "status": "Running",
    "threadAlive": 1,
    "uUID": "github.com"
}
```

**（2）ResultItems**

```json
{
    "all": {
        // 自定义的字段在这里
        "a_key":"a_value"
    },
    "request": {
        "binaryContent": false,
        "cookies": {},
        "headers": {},
        "priority": 0,
        "url": "https://github.com/code4craft?tab=repositories"
    },
    "skip": false
}
```

## 排错

### Https下无法抓取只支持TLS1.2的站点

作者 code4craft 针对 [ISSUE 701](https://github.com/code4craft/webmagic/issues/701) 提供了如下的解决方案：

```
更新会在0.7.4版本发布。

临时适配方式，修改HttpClientGenerator中的buildSSLConnectionSocketFactory方法，

return new SSLConnectionSocketFactory(createIgnoreVerifySSL(), new String[]{"SSLv3", "TLSv1", "TLSv1.1", "TLSv1.2"},
                    null,
                    new DefaultHostnameVerifier())
重写自己实现的HttpClientDownloader，并设置到Spider中。
```

### java.net.UnknownHostException

请检查网络连接。

## 参考

1. [WebMagic in Action - webmagic.io](http://webmagic.io/docs/zh/)