---
title: Spring Data MongoDB
date: 2018-12-05 14:25:40
tags: [Java,Spring,MongoDB]
---

## 用途

快速集成 MongoDB，不用写一行 MongoDB 的 CRUD 语句。而是使用 Spring Data 独有的方法命名方式定义数据库操作，并且可以方便地替换各种数据库，比如 MySQL。

## 快速开始

### （0）开始之前

确保已有可连接的 MongoDB

### （1）依赖引入

在 `build.gradle` 中添加如下依赖。

```groovy
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:2.0.5.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

repositories {
    mavenCentral()
}

sourceCompatibility = 1.8
targetCompatibility = 1.8

dependencies {
    compile("org.springframework.boot:spring-boot-starter-data-mongodb")
}
```

### （2）配置 MongoDB 连接

这里配置了 MongoDB 的连接地址和使用的数据库，还配置了扫描 Repositories 的位置。Repositories 我们后面会讲到是什么。

```java
@Configuration
@EnableMongoRepositories(basePackages = "com.example.dao")
public class MongoConfig {

  @Bean
  public MongoOperations mongoTemplate() throws UnknownHostException {
    return new MongoTemplate(mongoDbFactory());
  }

  @Bean
  public MongoDbFactory mongoDbFactory() throws UnknownHostException {
    return new SimpleMongoDbFactory(new MongoClientURI("mongodb://localhost:27017/test"));
  }

}
```

### （3）定义一个简单的实体类

实体类是一个 POJO，不过会多一些注解。简单介绍下这些注解吧：

1. `@Document` ，用于自定义 MongoDB 中 Collection 的名称，**默认情况下 collection 值为空，使用类名的小写形式作为 Collection 的名称**；
2. `@Id` ，用于指定 MongoDB 内部使用字段 `_id` 的值，如果不指定，则使用自动生成的值。
3. `@Field` ，用于指定字段存储时的名称，如果不指定，则直接使用字段名。
4. `@Indexed`，用于为指定字段添加索引，会调用 MongoDB 的 `createIndex` 方法。值得注意的是：**必须 `@Document` 注解，否则不会自动生成索引**。

```java
@Document(collection = "Customer")
public class Customer {
    @Id
    public String id;
	
    @Indexed
    @Field("first_name")
    public String firstName;
    
    @Field("last_name")
    public String lastName;
    
    public Customer() {}

    public Customer(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}
```

### （4）定义一个 Repository

`MongoRepository` 中定义的基本的 CRUD 操作，你可以自定义查询方法，不过要遵守一定的规范，Spring Data MongoDB 会根据方法名和参数去执行数据库操作。这个规范参见下文 <u>支持的查询方法关键字列表</u>。此处只需要了解有 `findByXx` 的方法名即可。

```java
public interface CustomerRepository extends MongoRepository<Customer, String> {

    public Customer findByFirstName(String firstName);
    public List<Customer> findByLastName(String lastName);
}
```

### （5）让 Spring Boot 自动装配 CustomerRepository

```java
@SpringBootApplication
public class Application implements CommandLineRunner {

	@Autowired
	private CustomerRepository repository;

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

	@Override
	public void run(String... args) throws Exception {
		repository.deleteAll();

		// save a couple of customers
		repository.save(new Customer("Alice", "Smith"));
		repository.save(new Customer("Bob", "Smith"));

		System.out.println("-------------------------------");

		// fetch an individual customer
		System.out.println(repository.findByFirstName("Alice"));
	}
}
```

### （6）使用 MongoDB 命令行查询

```shell
$ mongo
> use test
> db.Customer.find({})
```

## 深入探讨

### 常用的匹配注解列表

| Annotation       | Desc                                                         |
| ---------------- | ------------------------------------------------------------ |
| `@Id`            | 用于指定 MongoDB 内部使用字段 `_id` 的值，如果不指定，则使用自动生成的值。 |
| `@Field`         | 用于指定数据库中存储的字段名。                               |
| `@Document`      | 用于指定该类的实例对应 MongoDB 的某个指定 Collection 下的 Document。 |
| `@Indexed`       | 用于为指定字段添加索引。                                     |
| `@CompoundIndex` | 用于指定复合索引。                                           |
| `@Transient`     | 用于将某些字段排除，不与数据库匹配。                         |
| `@Version`       | 用于指定字段的版本，默认值为 0，在每次更新字段后自增。       |

### 支持的查询方法关键字列表

| Keyword                              | Sample                                                       | Logical result                                               |
| ------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `After`                              | `findByBirthdateAfter(Date date)`                            | `{"birthdate" : {"$gt" : date}}`                             |
| `GreaterThan`                        | `findByAgeGreaterThan(int age)`                              | `{"age" : {"$gt" : age}}`                                    |
| `GreaterThanEqual`                   | `findByAgeGreaterThanEqual(int age)`                         | `{"age" : {"$gte" : age}}`                                   |
| `Before`                             | `findByBirthdateBefore(Date date)`                           | `{"birthdate" : {"$lt" : date}}`                             |
| `LessThan`                           | `findByAgeLessThan(int age)`                                 | `{"age" : {"$lt" : age}}`                                    |
| `LessThanEqual`                      | `findByAgeLessThanEqual(int age)`                            | `{"age" : {"$lte" : age}}`                                   |
| `Between`                            | `findByAgeBetween(int from, int to)`                         | `{"age" : {"$gt" : from, "$lt" : to}}`                       |
| `In`                                 | `findByAgeIn(Collection ages)`                               | `{"age" : {"$in" : [ages…]}}`                                |
| `NotIn`                              | `findByAgeNotIn(Collection ages)`                            | `{"age" : {"$nin" : [ages…]}}`                               |
| `IsNotNull`, `NotNull`               | `findByFirstnameNotNull()`                                   | `{"firstname" : {"$ne" : null}}`                             |
| `IsNull`, `Null`                     | `findByFirstnameNull()`                                      | `{"firstname" : null}`                                       |
| `Like`, `StartingWith`, `EndingWith` | `findByFirstnameLike(String name)`                           | `{"firstname" : name} (name as regex)`                       |
| `NotLike`, `IsNotLike`               | `findByFirstnameNotLike(String name)`                        | `{"firstname" : { "$not" : name }} (name as regex)`          |
| `Containing` on String               | `findByFirstnameContaining(String name)`                     | `{"firstname" : name} (name as regex)`                       |
| `NotContaining` on String            | `findByFirstnameNotContaining(String name)`                  | `{"firstname" : { "$not" : name}} (name as regex)`           |
| `Containing` on Collection           | `findByAddressesContaining(Address address)`                 | `{"addresses" : { "$in" : address}}`                         |
| `NotContaining` on Collection        | `findByAddressesNotContaining(Address address)`              | `{"addresses" : { "$not" : { "$in" : address}}}`             |
| `Regex`                              | `findByFirstnameRegex(String firstname)`                     | `{"firstname" : {"$regex" : firstname }}`                    |
| `(No keyword)`                       | `findByFirstname(String name)`                               | `{"firstname" : name}`                                       |
| `Not`                                | `findByFirstnameNot(String name)`                            | `{"firstname" : {"$ne" : name}}`                             |
| `Near`                               | `findByLocationNear(Point point)`                            | `{"location" : {"$near" : [x,y]}}`                           |
| `Near`                               | `findByLocationNear(Point point, Distance max)`              | `{"location" : {"$near" : [x,y], "$maxDistance" : max}}`     |
| `Near`                               | `findByLocationNear(Point point, Distance min, Distance max)` | `{"location" : {"$near" : [x,y], "$minDistance" : min, "$maxDistance" : max}}` |
| `Within`                             | `findByLocationWithin(Circle circle)`                        | `{"location" : {"$geoWithin" : {"$center" : [ [x, y], distance]}}}` |
| `Within`                             | `findByLocationWithin(Box box)`                              | `{"location" : {"$geoWithin" : {"$box" : [ [x1, y1], x2, y2]}}}` |
| `IsTrue`, `True`                     | `findByActiveIsTrue()`                                       | `{"active" : true}`                                          |
| `IsFalse`, `False`                   | `findByActiveIsFalse()`                                      | `{"active" : false}`                                         |
| `Exists`                             | `findByLocationExists(boolean exists)`                       | `{"location" : {"$exists" : exists }}`                       |

> Tip：将以上的 `findBy` 替换成 `deleteBy` 含义就变成了：查找后进行删除操作。

## 参考

1. [Spring Data MongoDB - Reference Documentation - spring.io](https://docs.spring.io/spring-data/mongodb/docs/current/reference/html/#repositories.create-instances.spring)
2. [Accessing Data with MongoDB - spring.io](https://spring.io/guides/gs/accessing-data-mongodb/)