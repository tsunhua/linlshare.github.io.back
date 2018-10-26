### 背景

后台基本使用 Amazon 的全家桶（EC2、DynamoDB、S3、Step Fuction 等等）构建。现在需要根据访问者的 IP 确定访问者的国家或地区。

已知：

1. 访问者 IP

2. 一个 [ipdata.csv](https://github.com/LinLshare/Blog/blob/master/data/S3%20Select%20%E5%AE%9E%E8%B7%B5%20%E2%80%94%20%E7%A1%AE%E5%AE%9A%E7%94%A8%E6%88%B7%E5%9B%BD%E5%AE%B6%E5%9C%B0%E5%8C%BA/ipdata.csv) 文件，已放置在 S3 的桶 `ow-public-us` 中，格式如下

   | ip_from  | ip_to    | country_code | country_name |
   | -------- | -------- | ------------ | ------------ |
   | 0        | 16777215 | -            | -            |
   | 16777216 | 16777471 | AU           | Australia    |
   | 16777472 | 16778239 | CN           | China        |

### 流程

#### 1. 引入 S3 Select

```groovy
compile "com.amazonaws:aws-java-sdk-s3:1.11.379"
```

#### 2. 构建 `AmazonS3` 对象

```java
public AmazonS3 createAmazonS3(){
    final AwsSupport awsSupport = new AwsSupport();
    ClientConfiguration clientConfiguration = new ClientConfiguration();
    clientConfiguration.setSocketTimeout((int) TimeUnit.SECONDS.toMillis(70));
    AmazonS3ClientBuilder builder = AmazonS3ClientBuilder.standard()
                                                            .withCredentials(awsSupport.getCredentials())
                                                            .withClientConfiguration(
                                                                clientConfiguration);
    // ). region
    final Region region = awsSupport.getCurrentRegion();
    if (region != null) {
        builder.withRegion(region.getName());
    }
    return builder.build();
}

```

#### 3. 构建 `SelectObjectContentRequest` 对象 

本文中输入的为 CSV 无压缩数据，输出为 Json 类型数据。

```java
public static SelectObjectContentRequest createBaseCSVRequest(String bucket,
                                                                String key,
                                                                String query) {
    SelectObjectContentRequest request = new SelectObjectContentRequest();
    request.setBucketName(bucket);
    request.setKey(key);
    request.setExpression(query);
    request.setExpressionType(ExpressionType.SQL);

    InputSerialization inputSerialization = new InputSerialization();
    inputSerialization.setCsv(new CSVInput());
    inputSerialization.setCompressionType(CompressionType.NONE);
    request.setInputSerialization(inputSerialization);

    OutputSerialization outputSerialization = new OutputSerialization();
    outputSerialization.setJson(new JSONOutput());
    request.setOutputSerialization(outputSerialization);
    return request;
}
```

#### 4. 转化 IP 为 IP LONG

将 IP 字符串 转为 long 型数值，方便进行 IP 国家地区定位。

```java
public static long ip2Long(String ipAddress) {
    if (Strings.isNullOrEmpty(ipAddress)) {
        return 0L;
    }
    long result = 0;
    String[] ipAddressInArray = ipAddress.split("\\.");

    for (int i = 3; i >= 0; i--) {
        long ip = Long.parseLong(ipAddressInArray[3 - i]);
        // left shifting 24,16,8,0 and bitwise OR
        // 1. 192 << 24
        // 1. 168 << 16
        // 1. 1 << 8
        // 1. 2 << 0
        result |= ip << (i * 8);
    }
    return result;
}
```

#### 5. 请求并获取国家地区信息

```java
// _1 代表第一列 ip_from
// _2 代表第二列 ip_to
// _3 代表第三列 country_code
// 注意： SQL 中的变量需要用单引号括起来
SelectObjectContentResult selectObjectContentResult =
    createAmazonS3().selectObjectContent(createBaseCSVRequest("ow-public-us",
                                                                    "ipdata.csv",
                                                                    "SELECT s.\'country_code\' FROM S3Object s WHERE s._1<=\'" +
                                                                    ipLong +
                                                                    "\' AND s._2>=\'" +
                                                                    ipLong + "\' LIMIT 1"));
selectObjectContentResult.getPayload()
                            .getRecordsInputStream(new SelectObjectContentEventVisitor() {
                            @Override
                            public void visit(SelectObjectContentEvent.RecordsEvent event) {
                                try {
                                String content =
                                    new String(event.getPayload().array(), "utf-8");
                                LOGGER.debug("Country is --> {}", content);
                                JsonObject object = Json.fromJson(content, JsonObject.class);
                                String country = object.get("_3").getAsString();
                                } catch (UnsupportedEncodingException e) {
                                e.printStackTrace();
                                }
                            }
                            });
```

### 预警

在编辑 S3 Select 的 SQL 语句时，使用下列形式是不支持的：

```java
// 出错：AmazonS3Exception: The column index at line 1, column 8 is invalid. Please check the service documentation and try again. (Service: Amazon S3; Status Code: 400; Error Code: InvalidColumnIndex;
String sql = "SELECT s.\"country_code\" FROM S3Object s WHERE s._1<=\'" + ipLong +"\' AND s._2>=\'" + ipLong + "\' LIMIT 1";

// 出错：AmazonS3Exception: Invalid Path component, expecting either an IDENTIFIER or STAR, got: LITERAL,at line 1, column 10. (Service: Amazon S3; Status Code: 400; Error Code: ParseInvalidPathComponent;
String sql = "SELECT s.\'country_code\' FROM S3Object s WHERE s._1<=\'" + ipLong +"\' AND s._2>=\'" + ipLong + "\' LIMIT 1";

// 出错：AmazonS3Exception: The column index at line 1, column 8 is invalid. Please check the service documentation and try again. (Service: Amazon S3; Status Code: 400; Error Code: InvalidColumnIndex;
String sql = "SELECT s.country_code FROM S3Object s WHERE s._1<=\'" + ipLong +"\' AND s._2>=\'" + ipLong + "\' LIMIT 1";
```

但是第一种写法在 Python 库 `boto3` 中是支持的，可以参见 [参考2](https://medium.com/@michal.adamkiewicz/s3-select-new-revolution-at-rest-8e1749d2bff2)。

### 参考

1. [使用 适用于 Java 的开发工具包 从对象中选择内容 - Amazon](https://docs.aws.amazon.com/zh_cn/AmazonS3/latest/dev/SelectObjectContentUsingJava.html)
2. [S3 Select — new revolution “at rest” - Medium](https://medium.com/@michal.adamkiewicz/s3-select-new-revolution-at-rest-8e1749d2bff2)