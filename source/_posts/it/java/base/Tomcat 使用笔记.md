---
title: Tomcat 使用笔记
date: 2017-12-28 24:56:00
tags: [Java,Tomcat]
comments: true
---

### Tomcat

#### 安装

1. 从官网下载压缩包，放入程序安装的路径，需要的话设置下环境变量和Catalina_home，

2. mac下可以通过homebrew安装，命令为：

   ```Shell
   brew install tomcat
   ```

   安装完成后，提示信息为：

   ```shell
   ==> Caveats
   To have launchd start tomcat now and restart at login:
     brew services start tomcat
   Or, if you don't want/need a background service you can just run:
     catalina run
   ==> Summary
   🍺  /usr/local/Cellar/tomcat/8.5.24: 632 files, 12.8MB, built in 51 seconds
   ```

Catalina是一个servlet容器，用于处理servlet。Tomcat的核心由三部分组成：

| Web容器     | 处理静态页面         |
| --------- | -------------- |
| Servlet容器 | 处理Servlet文件    |
| JSP容器     | 将JSP翻译为Servlet |

#### 目录结构

| bin     | 执行文件目录，包含启动和关闭tomcat的脚本 |             |                   |
| ------- | ----------------------- | ----------- | ----------------- |
| conf    | web.xml                 | webapp的默认配置 |                   |
|         | server.xml              | 服务器默认配置文件   |                   |
|         | Catalina                | localhost   | 配置WEB站点的根目录和虚拟子目录 |
| lib     | 存放公用的jar包，包含jsp等支持      |             |                   |
| webapps | 默认的WEB站点根目录             |             |                   |
| logs    | 存放日志                    |             |                   |
| work    | jsp的工作目录                |             |                   |

#### 配置WEB站点的根目录和虚拟子目录

**WEB站点的根目录**：webapps物理上存储的位置，默认为tomcat的webapps目录，可以通过以下方式自定义：

1. 修改`conf/server.xml`文件的Host节点的`appBase`值，该值可以是绝对路径和相对路径：

```Xml
   <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
```

2. 在`<catalina_home>/conf/Catalina/localhost`下增加ROOT.xml 文件，内容为：

```xml
<Context docBase="<站点根目录>" path="" reloadable="true" />
```

`docBase` 表示对应path的文档根路径。

`path ` 为空表示更改根目录的上下文环境。

``reloadable` 属性标示tomcat是否要在运行时监视class文件是否有改动，若有改动则自动重新加载webapps。

3. 在`conf/server.xml`文件中配置`Context`节点，修改`docBase`属性

**虚拟子目录**：通过浏览器访问的路径，其修改方式如下：

1. 在`<catalina_home>/conf/Catalina/localhost`下增加XML文件，内容为：

```xml
<Context docBase="<站点物理位置>" reloadable="true" />
```

此时，XML文件名就是虚拟子目录的名称，而path属性弃用。

2. 在`conf/server.xml`文件中配置`Context`节点，修改`path`属性

#### 部署描述符

`web.xml`即是web应用的部署描述符

#### 目录的默认页面

通过修改根目录下`web.xml`中的`<welcom-file-list>`节点进行全局配置，通过修改单个webapp下的`WEB-INF`x下的`web.xml`文件中的`<welcom-file-list>`节点