---
title: Guava Cache
date: 2018-11-23 17:32:02
tags: [IT,Guava,Cache]
comments: true
---

## Guava Cache 是做什么的？

内存缓存，类似于 ConcurrentMap，支持自动缓存、缓存回收和缓存移除回调。

## 两种加载方式

### 使用CacheLoader

当有默认的加载或计算方式使用该方式。示例如下：

```java
LoadingCache<Key, Value> cache = CacheBuilder.newBuilder()
    .maximumSize(1000)
    .expireAfterWrite(10, TimeUnit.MINUTES)
    .removalListener(MY_LISTENER)
    .build(
    new CacheLoader<Key, Value>() {
        public Value load(Key key) throws Exception {
            return createExpensiveValue(key);
        }
    });
//...
try {
   cache.get(key);
} catch (ExecutionException e){
  throw new OtherException(e.getCause());
}
```

### 使用 Callable

当没有默认加载运算，或者想要覆盖默认的加载运算，同时保留 “获取缓存 -- 如果没有 -- 则计算”（get-if-absent-compute）的原子语义时使用该方式。示例如下：

```java
Cache<Key, Value> cache =  CacheBuilder.newBuilder()
    .expireAfterWrite(1,TimeUnit.MINUTES)
    .removalListener(this)
    .build();
//...
// 1. get
try {
  cache.get(key, new Callable<Value>() {
    @Override
    public Value call() throws AnyException {
      return doThingsTheHardWay(key);
    }
  });
} catch (ExecutionException e) {
  throw new OtherException(e.getCause());
}

// 2. getIfPresent
cache.getIfPresent(key);
```

## 参考

1. [CachesExplained - guava](https://github.com/google/guava/wiki/CachesExplained)
2. [[Google Guava] 3-缓存 - 并发编程网](http://ifeve.com/google-guava-cachesexplained/)

