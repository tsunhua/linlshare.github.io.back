---
title: Docker
date: 2018-11-27 17:47:54
tags: [Docker]
---

## Docker 是什么？

[Docker](https://github.com/moby/moby) 是一种虚拟化的容器，**隔离了文件系统、网络互联和进程**等等，但比之传统的虚拟化技术，精简了内核和硬件的虚拟，容器内的应用进程直接运行在宿主内核。

## 安装

### macOS

**（1）Homebrew 安装**

```shell
> brew cask install docker
```

**（2）下载安装**

[Docker.dmg 下载](https://download.docker.com/mac/stable/Docker.dmg)

### 检查安装情况

```shell
> docker --version
> docker-compose --version
> docker-machine --version
```

### 使用镜像加速器

鉴于国内的网络情况，拉取镜像会很慢，这时候需要配置镜像加速器，使用国内的镜像服务器。

**国内的镜像服务器地址**：	https://registry.docker-cn.com。

**配置方式**：对于使用 Docker 客户端的用户，依次点击 `Docker 图标 --> Settings/ Perferences --> Daemon --> Registry mirrors`，输入加速器地址，然后点击 Apply & Restart 即可。

**检查配置是否生效**：执行 `docker info` 查看 `Registry Mirrors` 字段的值。

## 快速开始：启动一个 Nginx 服务器

（1）安装并启动一个 Nginx 服务器，将本地的 8080 端口映射到 Docker 的 80 端口。

```shell
> docker run -d -p 8080:80 --name webserver nginx
```

（2）通过 `docker ps` 查看运行中的 docker 容器列表。

（3）通过 `http://localhost:8080` 即可正常访问。

（4）停止 Nginx 服务器

```shell
> docker stop webserver
```

（4）从 Docker 中删除 Nginx 服务器

```shell
> docker rm webserver
```

## 基本概念

### 镜像（Image）

虚拟概念，并非一个 ISO 的压缩文件，而是使用 Union FS 分层存储技术存储的多层文件系统联合组成，存放的是 `root` 文件系统，包括容器运行时所需的程序、库、资源、配置等文件，但不包含动态数据。

### 容器（Container）

容器是镜像运行时的实体，实质就是一个个的 Docker 进程，可以对 Docker 进程进行如下操作：

- 创建
- 启动
- 停止
- 删除
- 暂停

容器也采用分层存储，容器运行时，以镜像为基础层，在其上创建一个当前容器的存储层，称之为容器存储层。容器存储层的生命周期与容器同步，故不应向存储层写入任何数据，所有的文件写入操作，都应**使用数据卷（Volume）或者绑定宿主目录**。

### Registry

用来集中存储和分发 Docker 镜像的服务，一个 Registry 可以包含多个仓库（Repository），每个仓库可以包含多个标签（Tag），每个标签对应一个镜像。

常用的 Public Registry 列表：

1. [Docker Hub](https://hub.docker.com/)
2. [Quay.io](https://quay.io/repository/)
3. [Google Container Registry](https://cloud.google.com/container-registry/)
4. [网易云镜像服务](https://c.163.com/hub#/m/library/)
5. [DaoCloud 镜像市场](https://hub.daocloud.io/)
6. [阿里云镜像库](https://cr.console.aliyun.com/) 
7. [时速云镜像仓库](https://hub.tenxcloud.com/)

国内针对 Docker Hub 的镜像服务（加速器）有：

1. [阿里云加速器](https://cr.console.aliyun.com/#/accelerator)
2. [DaoCloud 加速器](https://www.daocloud.io/mirror#accelerator-doc)

## 命令

### 镜像相关

| 命令                           | 功能               |
| ------------------------------ | ------------------ |
| docker images                  | 列出所有拉取的镜像 |
| docker inspect [name]          | 检视镜像的详细信息 |
| docker rmi [镜像标签或镜像 ID] | 删除镜像           |
| docker pull [name]             | 拉取镜像           |


### 容器相关

| 命令                                 | 功能                   |
| ------------------------------------ | ---------------------- |
| docker ps -a                         | 列出所有容器及其状态   |
| docker ps                            | 列出所有运行中的容器   |
| docker  rm [容器 ID]                 | 删除容器               |
| docker crate -it [镜像标签或镜像 ID] | 新建一个容器           |
| docker start [容器 ID]               | 运行处于终止状态的容器 |
| docker kill [容器 ID]                | 强制终止容器           |
| docker kill [容器 ID]                | 停止容器               |
| docker restart [容器 ID]             | 重启一个容器           |
| docker logs [容器ID]                 | 查看容器日志           |

### 更进一步

| 命令                                       | 功能                       |
| ------------------------------------------ | -------------------------- |
| docker exec -ti [容器ID] /bin/bash         | 进入具体容器中运行交互命令 |
| docker exec [容器 ID 或容器 name] 具体命令 | 直接执行具体容器的命令     |

## 排错

### Error: Cannot Start Container: stat /bin/sh: no such file or directory”

（1）使用 `docker inspect`  检视镜像的 `Cmd` 选项；

（2）如果 `Cmd` 中不包含 `/bin/sh` 那意味着可能被你重写了。

### Run container but exited immediately



## 参考

1. [Docker -- 从入门到实践](https://legacy.gitbook.com/book/yeasy/docker_practice/details)（绝佳的入门文档）
2. [docker-library/docs - Github](https://github.com/docker-library/docs) （docker 官方镜像的使用文档）