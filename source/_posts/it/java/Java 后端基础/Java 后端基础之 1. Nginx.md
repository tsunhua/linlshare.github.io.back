---
title: Java 后端基础之 1. Nginx
date: 2017-12-28 11:56:00
tags: [Java,Nginx]
comments: true
---

### Nginx

是一个Web服务器，也可以用作反向代理，负载平衡器和HTTP缓存。

#### 安装

1. 编译安装

linux下安装方式：

```Shell
yum -y install gcc pcre pcre-devel zlib zlib-devel openssl openssl-devel //下载依赖库

configure --prefix=/opt/nginx/ //进行环境配置,设置安装位置

maks && make install // 编译安装
```

mac下安装方式：

```shell
brew install nginx // 会自动安装需要的依赖
```

提示信息：

```shell
The default port has been set in /usr/local/etc/nginx/nginx.conf to 8080 so that
nginx can run without sudo.

nginx will load all files in /usr/local/etc/nginx/servers/.

To have launchd start nginx now and restart at login:
  brew services start nginx
Or, if you don't want/need a background service you can just run:
  nginx
```

HomeBrew 是一款自由即开放源代码的软件包管理系统，用于Mac OS X系统上的软件安装过程。

依赖列表：

| gcc                   | 编译程序                |
| --------------------- | ------------------- |
| pcre pcre-devel       | configure过程需要的正则表达式 |
| zlib zlib-devel       | 传输内容压缩              |
| openssl openssl-devel | 开启https支持需要         |

注意：devel包含普通包，且多了头文件，编译时需要。

补充：Https、SSL与OpenSSL三者关系

| HTTPS   | Hyper Text Transfer Protocol over Secure Socket Layer | HTTP的加密版本，底层使用SSL作为加密协议    |
| ------- | ---------------------------------------- | -------------------------- |
| SSL     | Secure Socket Layer（安全套接字层）              | 在客户端和服务器之间建立一条SSL安全通道的加密协议 |
| OpenSSL | --                                       | TLS/SSL协议的开源实现，提供开放库和命令行程序 |
| TLS     | Transport Layer Security（传输层安全协议）        | 用于两个应用程序之间提供保密性和数据完整性      |

#### 启动与关闭

进入nginx安装目录的`sbin`文件夹

执行`nginx`，将启动两个nginx进程，分别是：

master 进程：守护进程

work进程 ：用于响应请求。

执行`nginx -s stop` 关闭进程

执行`nginx -s reload`重启进程

自启动：编辑系统启动自动执行的脚本文件，在centos下是/etc/rc.d/rc.local ，增加nginx启动命令即可。

#### 配置

编辑 nginx.conf 文件 

```nginx
# 顶层配置信息，管理服务器级别行为
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

# event指令与事件模型有关，配置处理连接有关信息
events {
    worker_connections  1024;
}

# http指令处理http请求
http {
    #文件扩展名与文件类型映射表
    include       mime.types;
    #默认文件类型
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;
     #开启高效文件传输模式，sendfile指令指定nginx是否调用sendfile函数来输出文件，对于普通应用设为 on，如果用来进行下载等应用磁盘IO重负载应用，可设置为off，以平衡磁盘与网络I/O处理速度，降低系统的负载。注意：如果图片显示不正常把这个改 成off。
    sendfile        on;
    #防止网络阻塞
    #tcp_nopush     on;

    #长连接超时时间，单位是秒
    #keepalive_timeout  0;
    keepalive_timeout  65;
    
    #开启gzip压缩输出
    #gzip  on;

    #虚拟主机的配置，根据server_name进行匹配，当匹配不到时将请求发给第一个server
    server { 
        #监听端口
        listen       8080;
        #域名可以有多个，用空格隔开
        server_name  localhost;

        #默认编码
        charset utf-8;

        #定义本虚拟主机的访问日志
        #access_log  logs/host.access.log  main;
        location / {
            root   html;
            index  index.html index.htm;
            allow 192.168.10.100;
            allow 172.29.73.0/24;
            deny all;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include servers/*;
}

```

**location 表达式**

syntax：`location [=|~|~*|^~|@] /uri/ {…}`

分为两种匹配模式 ：

1. 普通字符串匹配：无开头引导字符或以=开头表示普通字符串的匹配
2. 正则匹配：以`~`或`~*`开头表示正则匹配`~*`表示不区分大小写

多个location时匹配规则：先普通后正则，只识别URI部分。例如请求为 `/test/1/abc.do?arg=xxx`：

1. 先查找是否有=开头的精确匹配，即`location = /test/1/abc.do {...}`

2. 再查找普通匹配，以 *最大前缀* 为规则，如有以下两个location

    ```nginx
   location /test/ {...}
   location /test/1 {...}
    ```

   则匹配后一项

3. 匹配到一个普通格式后，搜索并未结束，而是暂存当前结果，并继续搜索正则模式

4. 在所有正则模式location中找到第一个匹配项后，以此匹配项为最终结果。所有正则匹配项匹配规则受定义前后顺序影响，但普通匹配不会

5. 如果未找到正则匹配项，则以3中缓存的结果为最终结果

6. 如果一个匹配都没有，返回404



`location =/ {…}` 与 `location / {...}` 的差别：

前一个是精确匹配，只响应`/`请求，所有`/xxx`类请求不会以前缀匹配形式匹配到它

而后一个正相反，所有请求必然以`/`开头，所以没有其他匹配结果时一定会执行到它。



`location ^~ / {...}` `^~`表示非正则，表示匹配到此模式后不再继续正则搜索。因为一个请求在普通匹配规则下没得到其它普通匹配结果时，最终会匹配到这里，而这里又不允许正则，相当于匹配到此为止。 `/test/abc.jsp`



`deny all;` 拒绝请求，返回403

`allow 192.168.10.0/24;` 允许指定IP段通过
`proxy_pass http://192.168.1.61:8080;` 将匹配的请求代理到另一地址进行处理



@类似于变量定义，比如：

```nginx
error_page 403 @page403;
locaion @page403 {
  proxy_pass http://www.xxx.com
}
```



 