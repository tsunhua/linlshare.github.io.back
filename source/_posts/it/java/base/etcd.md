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
# 进入etcd 命令行交互
docker exec -it etcd /bin/sh
# 验证安装
etcd -version
etcdctl -version
# 验证是否启动
curl http://127.0.0.1:2379/version
```

### Docker Compose 快速部署

参考 Docker Compose 的使用说明，编辑 `docker-compose.yml` 文件如下：

```yaml
version: "3.6"
services:

  node1:
    image: quay.io/coreos/etcd
    volumes:
      - node1-data:/etcd-data
    expose:
      - 2379
      - 2380      
    networks:
      cluster_net:
        ipv4_address: 172.16.238.100
    environment:
      - ETCDCTL_API=3
    command:
      - /usr/local/bin/etcd
      - --data-dir=/etcd-data
      - --name
      - node1
      - --initial-advertise-peer-urls
      - http://172.16.238.100:2380
      - --listen-peer-urls
      - http://0.0.0.0:2380
      - --advertise-client-urls
      - http://172.16.238.100:2379
      - --listen-client-urls
      - http://0.0.0.0:2379
      - --initial-cluster
      - node1=http://172.16.238.100:2380,node2=http://172.16.238.101:2380,node3=http://172.16.238.102:2380
      - --initial-cluster-state
      - new
      - --initial-cluster-token
      - docker-etcd

  node2:
    image: quay.io/coreos/etcd
    volumes:
      - node2-data:/etcd-data
    networks:
      cluster_net:
        ipv4_address: 172.16.238.101
    environment:
      - ETCDCTL_API=3
    expose:
      - 2379
      - 2380
    command:
      - /usr/local/bin/etcd
      - --data-dir=/etcd-data
      - --name
      - node2
      - --initial-advertise-peer-urls
      - http://172.16.238.101:2380
      - --listen-peer-urls
      - http://0.0.0.0:2380
      - --advertise-client-urls
      - http://172.16.238.101:2379
      - --listen-client-urls
      - http://0.0.0.0:2379
      - --initial-cluster
      - node1=http://172.16.238.100:2380,node2=http://172.16.238.101:2380,node3=http://172.16.238.102:2380
      - --initial-cluster-state
      - new
      - --initial-cluster-token
      - docker-etcd

  node3:
    image: quay.io/coreos/etcd
    volumes:
      - node3-data:/etcd-data
    networks:
      cluster_net:
        ipv4_address: 172.16.238.102
    environment:
      - ETCDCTL_API=3
    expose:
      - 2379
      - 2380
    command:
      - /usr/local/bin/etcd
      - --data-dir=/etcd-data
      - --name
      - node3
      - --initial-advertise-peer-urls
      - http://172.16.238.102:2380
      - --listen-peer-urls
      - http://0.0.0.0:2380
      - --advertise-client-urls
      - http://172.16.238.102:2379
      - --listen-client-urls
      - http://0.0.0.0:2379
      - --initial-cluster
      - node1=http://172.16.238.100:2380,node2=http://172.16.238.101:2380,node3=http://172.16.238.102:2380
      - --initial-cluster-state
      - new
      - --initial-cluster-token
      - docker-etcd

volumes:
  node1-data:
  node2-data:
  node3-data:

networks:
  cluster_net:
    driver: bridge
    ipam:
      driver: default
      config:
      -
        subnet: 172.16.238.0/24
```

## etcdctl v3（主流）

> Tip：可以通过 `ETCDCTL_API=3 etcdctl -h` 查看 v3 版本的命令行帮助页

#### （1）查看所有键值对

```shell
# 指定版本为 v3 且 key 前缀为空，也就是所有 key 了
ETCDCTL_API=3 etcdctl get --prefix=true ""
```

#### （2）put

```shell
ETCDCTL_API=3 etcdctl put /testdir/testkey "你好 etcd"
```

## etcdctl v2（兼容）

> Tip：可以通过 `etcdctl -h` 查看 v2 版本的命令行帮助页

#### （1）set

设置某个键的值，支持选项：

```shell
--ttl '0'                该键值的超时时间（单位为秒），不配置（默认为 0）则永不超时
--swap-with-value value  若该键现在的值是 value，则进行设置操作
--swap-with-index '0'    若该键现在的索引值是指定索引，则进行设置操作
```

示例：

```shell
etcdctl set /testdir/testkey "Hello etcd"
```

#### （2）get

获取指定键的值，支持选项：

```shell
--sort       对结果进行排序
--consistent 将请求发给主节点，保证获取内容的一致性
```

示例：

```shell
etcdctl get /testdir/testkey
```

#### （3）update

更新某个键的值，支持选项：

```shell
--ttl '0'    该键值的超时时间（单位为秒），不配置（默认为 0）则永不超时
```

示例：

```shell
etcdctl update /testdir/testkey "你好 etcd"
```

#### （4）rm

删除某个键，支持选项：

```shell
--dir            删除空目录或键值对
--recursive, -r  删除当前键及其子键(当为目录时)
--with-value     当值匹配时删除
--with-index '0' 当索引匹配时删除
```

示例：

```shell
etcdctl rm /testdir/testkey --with-value "Hello etcd"
```

#### （5）ls

列出目录（默认为根目录 `/`）下的键和子目录，默认不显示子目录中内容。支持选项：

```shell
--sort         将输出结果排序
--recursive    如果目录下有子目录，则递归输出其中的内容
-p             对于输出为目录，在最后添加 / 进行区分
```

示例：

```shell
etcdctl ls -r -p
```

## 集群操作

使用 `member` 命令进行 etcd 实例与集群的操作：

1.  `list`  列出 etcd 集群中的所有实例
2. `add` 添加 etcd 实例到集群中
3.  `remove`  从集群中删除 etcd 实例
4. `update` 更新集群中的 etcd 实例

示例：

```shell
# v2
etcdctl member list
# v3
ETCDCTL_API=3 etcdctl member list
```

## REST API （v2）

```shell
# 查看版本
curl http://127.0.0.1:2379/version
# get
curl http://127.0.0.1:2379/v2/keys/testdir/testkey
```

## REST API （v3alpha）

```shell
HOST=http://ecp-etcd-7fbedb40ccf7b594.elb.us-east-1.amazonaws.com:2379
declare -A KV=(["config/http_server_port"]=8080 ["config/db_type"]="dynamo" ["config/aws_region"]="us-east-1" ["config/kafka_brokers"]="172.19.0.9:9092")

# show version
curl $HOST/version

# put key value
for k in ${!KV[@]}
do
	key=$(echo -n $k | base64)
	value=$(echo -n ${KV[$k]} | base64)
	## delete key before
	curl -L $HOST/v3alpha/kv/deleterange -X POST -d "{\"key\": \"${key}\"}"
	## put new key and value
	curl -L $HOST/v3alpha/kv/put -X POST -d "{\"key\":\"${key}\", \"value\": \"${value}\"}"
done

# show all keys
curl -L $HOST/v3alpha/kv/range -X POST -d '{"key": "AA==", "range_end": "AA=="}'
#curl -X POST -d '{"key": "L2FwcA==", "range_end": "L2I="}' $HOST/v3alpha/kv/range
```

已知问题：

1. 当 put 的 value 中包含字符 “-” 时会抛出 `{\"error\":\"invalid character '\\\\n' in string literal\",\"code\":3}`。

## 参考（Reference）

1. [etcd-io/etcd - Github](https://github.com/etcd-io/etcd)
2. [etcd-io/jetcd - Github](https://github.com/etcd-io/jetcd)
3. [etcd Documentation](https://etcd.readthedocs.io/en/latest/)
4. [etcd 服务注册与发现](https://ralphbupt.github.io/2017/05/04/etcd-%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0/)
5. [初试ETCD -  Tony Deng](https://tonydeng.github.io/2015/11/24/etcd-the-first-using/)

