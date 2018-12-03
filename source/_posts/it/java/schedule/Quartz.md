---
title: Quartz
date: 2018-12-03 10:49:29
tags: [Quartz]
---

## Quartz 是什么？

[Quartz](https://github.com/quartz-scheduler/quartz) 是一款 Java 平台上开源的任务调度器。

## 快速开始

### （1）引入依赖

```groovy
compile "org.quartz-scheduler:quartz:2.3.0"
compile "org.quartz-scheduler:quartz-jobs:2.3.0"
```

### （2）初始化

```java
// 从工厂中获取 Scheduler 对象
Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();

// 启动
scheduler.start();
```

### （3）新建一个 Job

```java
public class TestJob implements Job{

  @Override
  public void execute(JobExecutionContext context) throws JobExecutionException {
    System.err.println("Hello World!  TestJob is executing.");
  }
}
```

### （4）调度一个 Job

```java
// 添加 Job 的携带数据
JobDetail job = newJob(TestJob.class).withIdentity("job1", "group1").build();
// 新建一个触发器
Trigger trigger = newTrigger().withIdentity("trigger1", "group1")
    .startNow()
    .withSchedule(simpleSchedule().withIntervalInSeconds(5)
                  .repeatForever())
    .build();
// 开始调度
scheduler.scheduleJob(job, trigger);
```

## 参考

1. [quartz-2.2.x/quick-start](http://www.quartz-scheduler.org/documentation/quartz-2.2.x/quick-start.html)