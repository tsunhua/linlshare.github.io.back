---
title: 使用 Hexo 和 Github Page 搭建个人 wiki
date: 2018-10-27 24:00:00
tags: [Hexo,Wiki]
comments: true
---

## Hexo 安装

### 需要环境

- Node.js
- Git

### 命令

```
npm install hexo-cli -g
npm install hexo --save
npm install hexo-server
npm install hexo-deployer-git --save

#如果命令无法运行，可以尝试更换taobao的npm源
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

### 基本配置

```
#Hexo 将会在指定文件夹中新建所需要的文件。
$ hexo init <folder>
$ cd <folder>
$ npm install
```

```
# 安装Hexo插件
npm install hexo-generator-index --save
npm install hexo-generator-archive --save
npm install hexo-generator-category --save
npm install hexo-generator-tag --save
npm install hexo-server --save
npm install hexo-deployer-git --save
npm install hexo-deployer-heroku --save
npm install hexo-deployer-rsync --save
npm install hexo-deployer-openshift --save
npm install hexo-renderer-marked@0.2 --save
npm install hexo-renderer-stylus@0.2 --save
npm install hexo-generator-feed@1 --save
npm install hexo-generator-sitemap@1 --save
```

```
# 在本地查看效果
$hexo server
```

### 主题配置

1. 修改根目录的_config.yml

```
# Hexo Configuration
## Docs: http://hexo.io/docs/configuration.html
## Source: https://github.com/tommy351/hexo/

# Site #整站的基本信息
title: IT协会 #网站标题
subtitle: 学习 总结 分享 #网站副标题
description: 学习 总结 分享 #网站描述
author:  itxiehui#网站作者
email: gdinit@163.com #联系邮箱
language: zh-CN

# URL
## If your site is put in a subdirectory
url: http://itxiehui.github.io #你的域名
root: /
permalink: :year/:month/:day/:title/
tag_dir: tags
archive_dir: archives
category_dir: categories
code_dir: downloads/code

......

# Pagination
## Set per_page to 0 to disable pagination
per_page: 10 #每页10篇文章
pagination_dir: page

# Disqus #社会化评论disqus
disqus_shortname:

# Extensions
## Plugins: https://github.com/tommy351/hexo/wiki/Plugins
## Themes: https://github.com/tommy351/hexo/wiki/Themes
theme: jacman #修改主题
exclude_generator:
Plugins:
- hexo-generator-feed
- hexo-generator-sitemap

......

# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy: # 部署位置
  type: github
  repository: https://github.com/cnfeat/cnfeat.github.io.git
  branch: master     
```

### 写作

使用hexo new "newpost"创建一个新文件，然后使用hexo d -g生成并部署到github上。注意在_config.yml中配置的deploy的repository要看是否电脑有多个github账号。

```
#常用命令
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #将.deploy目录部署到GitHub
```

```
#简写
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```

## 使用 Wikitten 主题

访问 [hexo-theme-Wikitten](https://github.com/zthxxx/hexo-theme-Wikitten) 了解如何安装该款主题。

## 默认显示文章目录（toc as default）

在 `hemes/Wikitten/layout/common` 找到 `article.ejs` ，并修改

```ejs
<% if (post.toc) { %>
<div id="toc" class="toc-article">
<strong class="toc-title"><%= __('article.catalogue') %></strong>
<%- toc(post.content) %>
</div>
<% } %>
```

为

```ejs
<% if (post.toc!=false) { %>
<div id="toc" class="toc-article">
<strong class="toc-title"><%= __('article.catalogue') %></strong>
<%- toc(post.content) %>
</div>
<% } %>
```

然后重新生成部署（`hexo g -d`）即可。

## 引用本地图片

按照官方的 [asset-folders](https://hexo.io/zh-cn/docs/asset-folders.html) 的方法处理图片引用，在本地预览时是无法显示的，参考文章 [hexo中完美插入本地图片](http://etrd.org/2017/01/23/hexo%E4%B8%AD%E5%AE%8C%E7%BE%8E%E6%8F%92%E5%85%A5%E6%9C%AC%E5%9C%B0%E5%9B%BE%E7%89%87/) ，总结出如下的解决方案：

（1）开启 `post_asset_folder`

```ym
// config.yml
post_asset_folder: true
```

（2）安装 `hexo-asset-image` 插件

```shell
npm install https://github.com/CodeFalling/hexo-asset-image --save
```

（3）使用方式

1. 在与 `_posts/my_article.md`的平级位置上创建文件夹 `my_article`；
2. 放置 `my_article.md` 文件中引用到的图片，例如 `my_pic.jpg`；
3. 在 `my_article.md` 以 `![my_pic](my_article/my_pic.jpg)` 方式引用。

