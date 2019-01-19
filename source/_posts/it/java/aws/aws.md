---
title: aws
date: 2018-11-09 17:54:48
tags: [Java,aws]
comments: true
---

### 配置

```shell
aws configure --profile user2
AWS Access Key ID [None]: AKIAI44QH8DHBEXAMPLE
AWS Secret Access Key [None]: je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
Default region name [None]: us-east-1
Default output format [None]: text
```

### 清除配置的 access key（clear the credentials in aws configure）

```shell
$ rm -rf ~/.aws/credentials
```

