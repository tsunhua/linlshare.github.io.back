---
title: DynamoDB
date: 2019-01-19 11:14:51
tags: [Java,aws,DynamoDB]
---

## 基本概念

1. **Table**（表），与其他数据库的表的概念一致，即为数据的集合。
2. **Items**（数据项），代表一行数据。
3. **Attributes**（属性），一个数据项可由多个属性构成，一个属性由属性名、属性值类型和属性值构成。
4. **Partition Key**（分区键），即是最简单的主键，由一个属性构成，一张表有且只有一个分区键。由于 DynamoDB 内部在存储数据时使用分区键的 Hash 值实现跨多个分区的数据项目平均分布，故分区键又称之为 **Hash Key**。
5. **Sort Key**（排序键），排序键和分区键构成另一种主键，用于在分区中按排序键排序。由于 DynamoDB 内部按照排序键值有序地将具有相同分区键的项目存储在互相紧邻的物理位置，故排序键又称之为 **Range Key**。
6. **Secondary Indexes**（二级索引），这样命名应该是将主键视为一级索引。DynamoDB 的二级索引有两种：
   * Global secondary index（GSI），创建跟主键不同的分区键和排序键的索引，DynamoDB 有最多创建 20 个 GSI 的限制；
   *  Local secondary index（LSI），创建跟主键的分区键相同但是排序键相异的索引，当且仅当创建表时可用，DynamoDB 有最多创建 5 个 LSI 的限制；

## 配置本地开发环境

### 下载 DynamoDB 本地版本

从 [计算机上的 DynamoDB（可下载版本）- aws](https://docs.aws.amazon.com/zh_cn/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html) 下载，并放置到合适的位置。

### 配置

```shell
> aws configure --profile local                                                                                    AWS Access Key ID [None]: fake-ak
AWS Secret Access Key [None]: fake-sk
Default region name [None]:
Default output format [None]:
```

### 启动

```shell
java -Djava.library.path=./DynamoDBLocal_lib -jar DynamoDBLocal.jar -sharedDb
```

## CLI 操作

> 注意：如果是本地版本需要添加 `--endpoint-url http://localhost:8000`

```shell
# 创建表格
aws dynamodb create-table \
    --table-name ab-debug-proxy \
    --attribute-definitions \
        AttributeName=host,AttributeType=S \
        AttributeName=port,AttributeType=N \
        AttributeName=channel,AttributeType=S \
    --key-schema AttributeName=host,KeyType=HASH AttributeName=port,KeyType=RANGE \
    --global-secondary-indexes IndexName=channel-index,KeySchema=["{AttributeName=channel,KeyType=HASH}"],Projection="{ProjectionType=ALL}",ProvisionedThroughput="{ReadCapacityUnits=1,WriteCapacityUnits=1}" \
    --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1 \
    --endpoint-url http://localhost:8000

# 删除表格
aws dynamodb delete-table \
    --table-name ab-debug-proxy \
    --endpoint-url http://localhost:8000

# 列出所有表格
aws dynamodb list-tables --endpoint-url http://localhost:8000

# 添加数据项
aws dynamodb put-item \
    --table-name ab-debug-proxy \
    --item '{"host": {"S": "192.168.1.0"},"port": {"N": "9090"},"channel": {"S": "sw"} }' \
    --return-consumed-capacity TOTAL \
    --endpoint-url http://localhost:8000

# 扫描数据项
aws dynamodb scan \
    --table-name ab-debug-proxy \
    --endpoint-url http://localhost:8000
```

## Java 操作

### 批量加载（Batch Load）

```java
List<KeyPair> keyPairList = new ArrayList<>();
for (Proxy proxy : proxyList) {
    KeyPair keyPair = new KeyPair();
    keyPair.setHashKey(proxy.getHost());
    keyPair.setRangeKey(proxy.getPort());
    keyPairList.add(keyPair);
}
Map<Class<?>, List<KeyPair>> keyPairForTable = new HashMap<>();
keyPairForTable.put(Proxy.class, keyPairList);
Map<String, List<Object>> stringListMap = dynamoDBMapper.batchLoad(keyPairForTable);
```

## 参考

1. [计算机上的 DynamoDB（可下载版本）- aws](https://docs.aws.amazon.com/zh_cn/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html)
2. [Instroduction - Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html)