---
title: Splash
date: 2019-03-26 11:38:59
tags: [Robot,Splash]
---

## 安装

### Linux + Docker

```shell
sudo docker run -p 8050:8050 -p 5023:5023 scrapinghub/splash
```

## 快速开始

```shell
http://0.0.0.0:8050/
```

## Splash HTTP API

### 请求方式

1. GET，将参数转为 URL 参数；
2. POST，将参数编码为 JSON 格式并使用 `Content-Type: application/json` 请求体。

## 获取 Cookie 的 Lua 脚本

```lua
function main(splash, args)
  splash.images_enabled = false
  splash.resource_timeout = 90
  splash:set_user_agent(args.ua)
  splash:on_request(function(request) request:set_proxy{host = args.host, port = args.port} end)
  assert(splash:go(args.url))
  assert(splash:wait(0.5))
  local cookie = splash:evaljs('document.cookie')
  return cookie
end
```

## 部署到生产环境

1. 作为守护进程启动；
2. 奔溃重启；
3. 控制内存消耗；
4. 运行多个 Splash 实例；
5. 请求队列应该放到负载均衡里面；
6. 使用跟开发环境一致的版本。

```shell
docker run -d -p 8050:8050 --memory=3G --restart=always scrapinghub/splash:3.3.1 --maxrss 4000 --max-timeout 300 --slots 5
```

