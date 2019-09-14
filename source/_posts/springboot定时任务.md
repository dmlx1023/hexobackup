---
title: springboot定时任务
date: 2019-09-03 14:49:38
tags: springboot quartz
---

  在开发中，定时任务是非常常见的，java中通常会使用quartz,对于springboot来说也提供了非常方便的定时任务方案。

## 使用

只需要在启动类加上**@EnableScheduling**就可以开启定时任务

## 配置

在需要处理的任务方法上加上【@Scheduled】，类上加上【@Component】既可以将其配置为一个任务。例如

【@Scheduled(initialDelay = 1L, fixedRate = 1000L)】代表一个延迟一秒每秒执行一次的定时任务。

### 参数说明

#### cron

cron表达式不可以和除了zone外的其他参数一起使用对于zone也使用默认的本地时间不做配置。

```
cron 代表cron表达式，非常灵活。
常见的表达式：
0/5 * * * * ? 每5s执行
11 /1 * * * ? 每分钟的11s执行
20 01 01 * * ? 每天一点一分20秒执行
0 0 1 1 * ? 每月1号凌晨1点执行一次
0 26,29,33 * * * ?    在26分、29分、33分执行一次
0 0/5 14,18 * * ?    每天的下午2点至2：55和6点至6点55分两个时间段内每5分钟一次触发
0 0-5 14 * * ?       每天14:00至14:05每分钟一次触发
```

#### fixedDelay/fixedDelayString

以固定的延迟执行任务，即距离上次任务结束固定时间来执行任务。

#### fixedRate/fixedRateString

以固定的频率执行任务，即使上次任务执行完了也会等到固定时间到才会执行。

#### initialDelay/initialDelayString

服务启动后第一次执行的延迟时间。

###  线程池

默认的使用单线程，对于执行过长的任务来说，必须等到上个任务执行完才会执行下个任务，通常是不会使用的，我们可以对执行任务的线程池进行配置，此时会有2个线程在交替执行任务。

```java
@Configuration
public class ExecutorsConfig implements SchedulingConfigurer {
    private static int POOL_SIZE = 2;
    @Override
    public void configureTasks(ScheduledTaskRegistrar taskRegistrar) {
        ThreadPoolTaskScheduler threadPoolTaskScheduler = new ThreadPoolTaskScheduler();
        threadPoolTaskScheduler.setPoolSize(POOL_SIZE);
        threadPoolTaskScheduler.setThreadNamePrefix("my-scheduled-task-pool-");
        threadPoolTaskScheduler.initialize();
        taskRegistrar.setScheduler(threadPoolTaskScheduler);
    }
}

```

