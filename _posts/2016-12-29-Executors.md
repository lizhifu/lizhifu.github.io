---
layout: post
title:  "Executor框架简介"
date:   2016-12-29 19:14:54
categories: java并发
tags: java Executor
---

* content
{:toc}

## Executor框架
Executor框架，基于Executor接口将任务提交和任务执行进行解耦设计，通过ExecutorService提供了简便方式来提交任务和获取任务执行结果，封装了任务执行的过程。
Executor基于生产者-消费者模式。生产者提交任务，执行任务的线程是消费者。
结构如图所示：
![executor-frame]({{"/css/pics/executor-frame.jpg"}}) 
Executor框架主要包含三个部分：

`任务`：包括Runnable和Callable，其中Runnable表示一个可以异步执行的任务，而Callable表示一个会产生结果的任务

`任务的执行`：包括Executor框架的核心接口Executor以及其子接口ExecutorService。在Executor框架中有两个关键类ThreadPoolExecutor和ScheduledThreadPoolExecutor实现了ExecutorService接口。

`异步计算的结果`：包括接口Future和其实现类FutureTask。

### Executor接口
是Executor的基础，接口定义如下：  
```
public interface Executor {  
    void execute(Runnable command);  
}  
```