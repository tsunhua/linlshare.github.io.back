---
title: Spring Boot
date: 2018-1-7 12:03:00
tags: [Java,Spring,SpringBoot]
comments: true
---


### 简述

1. 推出时间：从Maven仓库的时间看是2016.7.28
2. 目的：摆脱大量的XML配置文件以及复杂的Bean依赖关系，快速、敏捷地开发新一代基于Spring框架的应用程序
3. 思想：约定优于配置（convention over configuration）
4. 框架功能：集成大量常用第三库配置（Jackson、JDBC、Mongo、Redis、Mail等）

### 入门

### 创建定时任务

1. 在Spring Boot 的主类加入 `@EnableScheduling` 注解，启用定时任务的配置；

   ```java
   @SpringBootApplication
   @EnableScheduling
   public class Application {
   	public static void main(String[] args) {
   		SpringApplication.run(Application.class, args);
   	}
   }
   ```

2. 使用 `@Scheduled(fixedRate = 1000)` 注解实现类需要定时执行的方法。

   ```java
   @Component
   public class ScheduledTasks implements Runnable{
       private static final SimpleDateFormat dateFormat = new SimpleDateFormat("HH:mm:ss");
   
       @Override
       @Scheduled(fixedRate = 1000)
       public void run() {
           System.out.println("Current: " + dateFormat.format(new Date()));
       }
   }
   ```
