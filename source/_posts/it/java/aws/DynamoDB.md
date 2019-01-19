---
title: DynamoDB
date: 2019-01-19 11:14:51
tags: [Java,aws,DynamoDB]
---

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
# 列出所有表格
aws dynamodb list-tables 
# 创建表格
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema AttributeName=Artist,KeyType=HASH AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput ReadCapacityUnits=1,WriteCapacityUnits=1
# 添加Item
aws dynamodb put-item \
--table-name Music  \
--item \
    '{"Artist": {"S": "No One You Know"}, "SongTitle": {"S": "Call Me Today"}, "AlbumTitle": {"S": "Somewhat Famous"}}' \
--return-consumed-capacity TOTAL  

aws dynamodb put-item \
    --table-name Music \
    --item '{ \
        "Artist": {"S": "Acme Band"}, \
        "SongTitle": {"S": "Happy Day"}, \
        "AlbumTitle": {"S": "Songs About Life"} }' \
    --return-consumed-capacity TOTAL 
```

## Java 操作

### BatchLoad

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