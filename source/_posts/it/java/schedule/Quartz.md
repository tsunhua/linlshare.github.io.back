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

## CronTrigger

Quartz 的 Cron 表达式不同于 Linux 系统上使用的 Cron 表达式。区别如下：

```shell
# Linux
minute   hour   day   month   week
# Quartz
second	minute   hour   day   month   week	year(optional field)
```

是的，Quartz 扩充了 second 和 year，这是要特别注意的。

关于 Quartz Cron 表达式每个字段的取值，整理如下：

- second：[0, 59]。
- minute： [0, 59]。
- hour：[0, 23]。
- day：一个月中的第几天，取值 [1, 31]，注意不同月份有不同的上限值。
- month：[0, 11]，注意这跟 Linux 的有差异。还可取 [JAN, FEB, MAR, APR, MAY, JUN, JUL, AUG, SEP, OCT, NOV, DEC]
- week：星期几，取值 [1, 7]，其中 1 代表星期日。还可取 [SUN, MON, TUE, WED, THU, FRI, SAT]

字段中使用的特殊字符跟 Linux 无异，如下：

- 星号（*）：代表所有可能的值，例如month字段如果是星号，则表示在满足其它字段的制约条件后每月都执行该命令操作。
- 逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”
- 中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”
- 正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次。

## 参考

1. [quartz-2.2.x/quick-start](http://www.quartz-scheduler.org/documentation/quartz-2.2.x/quick-start.html)
2. [Lesson 6: CronTrigger - Quartz Tutorials](http://www.quartz-scheduler.org/documentation/quartz-2.2.x/tutorials/tutorial-lesson-06.html)
3. [每天一个linux命令（50）：crontab命令](https://www.cnblogs.com/peida/archive/2013/01/08/2850483.html)