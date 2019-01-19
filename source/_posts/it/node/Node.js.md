---
title: Node.js
date: 2018-12-18 09:47:20
tags: [Node.js]
---

## 安装

### 使用 nvm 安装（可以自由切换 node 版本）

（1）下载并执行安装脚本

```shell
# curl 
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
# 或者 wget
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

（2）下载并使用指定版本的 node.js

```shell
# 列出仓库中所有的 node 版本
$ nvm ls-remote
# 安装指定版本
$ nvm install 8.11.1
# 使用已安装的某个版本
$ nvm use 8.11.1
# 查看当前使用的 node 版本
$ nvm -v
```

（3）卸载指定版本的 node.js

```shell
# 如果要卸载的版本是当前使用的版本，那么需要先停用它
$ nvm deactivate
$ nvm uninstall 8.11.1
```

## 异步编程

- callback
- promise
- async/await

## 排错

### /usr/bin/env: node: No such file or directory

（1）背景：执行 `npm start` 命令时出现上述错误。

（2）原因：`npm` 执行时默认使用 `/usr/bin/node`  去执行，而通过 `sudo apt install nodejs` 安装的位置是在 `/usr/bin/nodejs` 。

（3）解决方案：

- 方案一：使用`nvm` 安装 node，并统一管理 node.js 版本，这是最佳方案；

- 方案二：创建 node 执行文件到 `/usr/bin/node` 的软连接，如 `ln -s /usr/local/NODEJS_HOME/bin/node /usr/bin/node`

## 参考

1. [How To Install Node.js on Ubuntu 18.04 - digitalocean.com](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04)
2. [creationix/nvm - github.com](creationix/nvm - github.com)
3. [【node错误】/usr/bin/env: node: No such file or directory - tencent.com](https://cloud.tencent.com/developer/article/1028166)