---
title: Spring Cache
date: 2018-12-05 22:46:21
tags: [Java,Spring,Cache]
---

## 为什么要使用 Spring Cache 管理缓存？

让 Spring 来管理 Bean 的缓存具有以下优势：

1. Spring 支持 HashMap 缓存，Redis 缓存以及自定义的缓存方式；
2. Spring 缓存几乎不需要写代码，只需要配置好并声明好注解。

## 快速开始

### （1）依赖引入

这里使用 Spring 的依赖管理器来管理 Spring Cache 的版本，会自动处理内部的模块间依赖，这也是推荐的方式。

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

dependencies {
    compile("org.springframework.boot:spring-boot-starter-cache")
}
```

### （2）启用缓存

在 SpringApplication 配置类的地方添加以下注解以启用缓存功能。

```java
@SpringBootApplication
@EnableCaching
```

### （3）ConcurrentHashMap 缓存

当没有配置其他缓存库时，默认使用 `ConcurrentHashMap` 作为缓存仓库。

#### （3.1）一个简单的实体类

```java
public class Customer {
    public String id;
    public String firstName;
    public String lastName;
    
    public Customer() {}

    public Customer(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}
```

#### （3.2）一个 Repository

```java
public class CustomerRepository {
    
    @Cachable
    Customer getByFirstName(String firstName){
        // 这里应该是从数据库查询数据，DEMO 简省成直接新建了。
        return new Customer(firstName, "Jobs");
    }
}
```

#### （3.3）测试一下

如果缓存成功了，那么以下代码执行结果的 HashCode 是一致的。

```java
@Component
public class AppRunner implements CommandLineRunner {

    private static final Logger logger = LoggerFactory.getLogger(AppRunner.class);

    private final CustomerRepository customerRepository;

    public AppRunner(CustomerRepository customerRepository) {
        this.customerRepository = customerRepository;
    }

    @Override
    public void run(String... args) throws Exception {
        logger.info("John -->" + customerRepository.getByFirstName("John").hashCode());
        logger.info("John -->" + customerRepository.getByFirstName("John").hashCode());
        logger.info("John -->" + customerRepository.getByFirstName("John").hashCode());
    }
}
```

### （4）配合 Redis 缓存

#### （4.1）添加 Redis 依赖

在前面的依赖之下再额外新增 Redis 相关的依赖，如下：

```groovy
// 本环境中的 spring-data-redis 为 1.8.7.RELEASE 版本
// 高版本的配置略有不同，请留意
compile ("org.springframework.data:spring-data-redis") 
compile "redis.clients:jedis:2.9.0"
```

#### （4.2）配置 Redis

```java
@Configuration
public class RedisConfig {
  private final static Logger LOGGER = LoggerFactory.getLogger(RedisConfig.class);
  private final static Map<String, Long> CACHE_EXPIRE_MAP = new HashMap<>();

  static {
    CACHE_EXPIRE_MAP.put("cache1", 5 * 60L); //second
  }
    
  @Bean
  RedisConnectionFactory redisConnectionFactory() {
    JedisConnectionFactory jedisConFactory = new JedisConnectionFactory();
    jedisConFactory.setHostName("localhost");
    jedisConFactory.setPort(6379);
    return jedisConFactory;
  }

  @Bean
  StringRedisTemplate stringRedisTemplate(RedisConnectionFactory redisConnectionFactory) {
    return new StringRedisTemplate(redisConnectionFactory);
  }

  @Bean
  RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory) {
    RedisTemplate template = new RedisTemplate();
    template.setConnectionFactory(redisConnectionFactory);
    template.afterPropertiesSet();
    // 默认为 JdkSerializationRedisSerializer, 配合 @Cacheable 时 KEY 会有序列化值在中间
    // 使用 StringRedisSerializer 则不会如此
    template.setKeySerializer(new StringRedisSerializer());
    return template;
  }

  @Bean
  public RedisCacheManager cacheManager(RedisTemplate redisTemplate) {
    RedisCacheManager cm = new RedisCacheManager(redisTemplate);
    cm.setCacheNames(CACHE_EXPIRE_MAP.keySet());
    cm.setExpires(CACHE_EXPIRE_MAP);
    cm.setUsePrefix(true);
    cm.setCachePrefix(cacheName -> (cacheName + ":").getBytes());
    return cm;
  }
}
```

#### （4.2）序列化实体类

Spring 在将实体类缓存到 Redis 中时进行了序列化操作，如果不对实体类进行序列化将会报错。

```java
public class Customer implements Serializable {
    public String id;
    public String firstName;
    public String lastName;
    
    public Customer() {}

    public Customer(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}
```

#### （4.3）在需要缓存的位置使用注解，并指定缓存名

如果在使用 Redis 缓存时，没有指定缓存名，将会报错：`no cache could be resolved for at least one cache should be provided per cache operation`。

```java
public class CustomerRepository {
    
    @Cachable("cache1")
    Customer getByFirstName(String firstName){
        // 这里应该是从数据库查询数据，DEMO 简省成直接新建了。
        return new Customer(firstName, "Jobs");
    }
}
```

#### （4.4）测试一下

测试代码同（3.3）。除此之外还可以通过 Redis CLI 检验缓存结果：

```shell
> KEYS *
1) "cache1:cb5775e6-1b39-4f63-85c8-13f134a54f32"
> GET "cache1:cb5775e6-1b39-4f63-85c8-13f134a54f32"
> TTL "cache1:cb5775e6-1b39-4f63-85c8-13f134a54f32"
```

## 参考

1. [Caching Data with Spring - spring.io](https://spring.io/guides/gs/caching/)
2. [Caching - spring.io](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-caching.html#boot-features-caching-provider-redis)
3. [A Guide To Caching in Spring - baeldung.com](https://www.baeldung.com/spring-cache-tutorial)
4. [Spring Data Redis](https://docs.spring.io/spring-data/redis/docs/2.1.3.RELEASE/reference/html/#redis.repositories.expirations)
5. [spring使用redis做缓存 - cnblogs.com](https://www.cnblogs.com/morethink/p/7798602.html)