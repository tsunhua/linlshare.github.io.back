---
title: etcd
date: 2018-11-27 14:17:35
tags: [IT,ETCD]
---

## etcd 是什么？（What is etcd）

etcd 是一个一致的分布式可靠的键值存储技术。可被用来做配置共享和服务发现。

- 开发语言：Go
- 共识算法：[Raft](https://raft.github.io/)
- 命名来源：表示分布式的 `etc` 目录，发音为`/ˈɛtsiːdiː/`。
- 使用的端口：2.0 后，使用 2379 作为外部客户端通信，使用 2380 作为内部服务间通信。
- 发起团队：CoreOS

## 安装

### Mac 安装

```shell
# 安装
brew install etcd
# 验证安装
etcd -version
etcdctl -version
```

### Docker 安装

参考 [Running etcd under Docker - CoreOS](https://coreos.com/etcd/docs/latest/v2/docker_guide.html) 及 [docker_practice/etcd/install](docker_practice/etcd/install) ：

```shell
# 使用 host IP
export ETCD_NODE1=127.0.0.1
# 安装 etcd
docker run --name etcd \
    -p 2379:2379 \
    -p 2380:2380 \
    --volume=etcd-data:/etcd-data \
    quay.io/coreos/etcd:latest \
    /usr/local/bin/etcd \
    --data-dir=/etcd-data --name node1 \
    --initial-advertise-peer-urls http://${ETCD_NODE1}:2380 --listen-peer-urls http://0.0.0.0:2380 \
    --advertise-client-urls http://${ETCD_NODE1}:2379 --listen-client-urls http://0.0.0.0:2379 \
    --initial-cluster node1=http://${ETCD_NODE1}:2380
# 验证安装
docker exec etcd etcd -version
docker exec etcd etcdctl -version
```

## 参考（Reference）

1. [etcd-io/etcd - Github](https://github.com/etcd-io/etcd)
2. [etcd-io/jetcd - Github](https://github.com/etcd-io/jetcd)
3. [etcd Documentation](https://etcd.readthedocs.io/en/latest/)
4. [etcd 服务注册与发现](https://ralphbupt.github.io/2017/05/04/etcd-%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0/)

