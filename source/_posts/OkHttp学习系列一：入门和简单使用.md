---
title: OkHttp学习系列一：入门和简单使用
date: 2016-04-03 16:29:14
tags:
---

参考并有选择地翻译了：[http://square.github.io/okhttp/](http://square.github.io/okhttp/)

## 综述

HTTP是是现代应用访问网络的方式。它是现在我们交换数据和媒体的方式。高效地使用HTTP可以让数据加载更快和节省带宽（bandwidth）。

OkHttp是一个适用于Android和Java的HTTP 和 HTTP/2的客户端。它很高效，默认支持：

- HTTP/2支持允许到同一个主机（host）的所有的请求共享一个socket
- 连接池技术减少了请求延迟（如果HTTP/2不可用）
- 悄无声息的GZIP支持减少了下载的体积
- 响应缓存完全避免了网络服务于重复的请求

当网络出错时，OkHttp坚持：默默地从普通的连接问题中恢复网络。如果你的服务中有多个IP地址，OkHttp会在第一次连接失败后尝试可选的地址。这对于IPv4+IPv6和拥有冗余数据中心的服务来说是很必须的。OkHttp采用现代的TLS（Transport Layer Security，传输层安全）（SNI和ALPN）初始化新的连接，当握手失败时会退回TLS1.0。

使用OkHttp相当easy。它的请求/响应的API是用流畅的建造者模式构建并且具有不变性（？）。它同时支持同步阻塞调用和使用回调异步调用。

OkHttp支持Android 2.3和以上版本。对应Java，最小的要求是1.7版本。

>小知识：OkHttp是Square这家移动支付公司开发并贡献到开源社区的开发库。[Square Open Source主页]([http://square.github.io](http://square.github.io/))展示了该公司开源的一些产品，诸如dagger、picasso、retrofit等都是出自该公司，该公司的开源团队由25人组成，其中最出名的是[Jake Wharton](https://github.com/JakeWharton)，他是ActionBarSherlock、butterknife、ViewPagerIndicator、NineOldAndroids等开源库的作者


## 示例

#### GET请求

下面的代码展示了如何从一个URL中获取内容数据并以字符串形式打印出来。

```java
public class GetExample {
  OkHttpClient client = new OkHttpClient();
  String run(String url) throws IOException {
    Request request = new Request.Builder()
        .url(url)
        .build();
    Response response = client.newCall(request).execute();
    return response.body().string();
  }

  public static void main(String[] args) throws IOException {
    GetExample example = new GetExample();
    String response = example.run("https://raw.github.com/square/okhttp/master/README.md");
    System.out.println(response);
  }
}
```

#### POST请求

下面的例子演示了如何POST数据到服务器中

```java
public class PostExample {
  public static final MediaType JSON
      = MediaType.parse("application/json; charset=utf-8");

  OkHttpClient client = new OkHttpClient();

  String post(String url, String json) throws IOException {
    RequestBody body = RequestBody.create(JSON, json);
    Request request = new Request.Builder()
        .url(url)
        .post(body)
        .build();
    Response response = client.newCall(request).execute();
    return response.body().string();
  }

  String bowlingJson(String player1, String player2) {
    return "{'winCondition':'HIGH_SCORE',"
        + "'name':'Bowling',"
        + "'round':4,"
        + "'lastSaved':1367702411696,"
        + "'dateStarted':1367702378785,"
        + "'players':["
        + "{'name':'" + player1 + "','history':[10,8,6,7,8],'color':-13388315,'total':39},"
        + "{'name':'" + player2 + "','history':[6,10,5,10,10],'color':-48060,'total':41}"
        + "]}";
  }

  public static void main(String[] args) throws IOException {
    PostExample example = new PostExample();
    String json = example.bowlingJson("Jesse", "Jake");
    String response = example.post("http://www.roundsapp.com/post", json);
    System.out.println(response);
  }
}
```

## 下载

- [okhttp-3.2.2.jar](http://repo1.maven.org/maven2/com/squareup/okhttp3/okhttp/3.2.0/okhttp-3.2.0.jar)
- [okio-1.6.0.jar](https://search.maven.org/remote_content?g=com.squareup.okio&a=okio&v=LATEST)

Okio用于给OkHttp提供快速的I/O和可调整大小的缓存池。
OkHttp的源代码，示例可以在[GitHub](http://github.com/square/okhttp)中取得。

#### Meven配置

 
<pre class="lang:default decode:true " >&lt;dependency&gt;
  &lt;groupId&gt;com.squareup.okhttp3&lt;/groupId&gt;
  &lt;artifactId&gt;okhttp&lt;/artifactId&gt;
  &lt;version&gt;3.2.0&lt;/version&gt;
&lt;/dependency&gt;</pre> 


#### Gradle配置

```
compile 'com.squareup.okhttp3:okhttp:3.2.0'
```

----------------

><span style="font-size:12px">本文标题: <a href="{{ permalink }}">{{ title }}</a>
文章作者: <a href="http://linlshare.github.io/">Lshare</a>  
许可协议: <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">©署名-非商用-相同方式共享 4.0</a></span>