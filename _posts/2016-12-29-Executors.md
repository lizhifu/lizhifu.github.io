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

`任务`：包括Runnable和Callable，其中Runnable表示一个可以异步执行的任务，而Callable表示一个会产生结果的任务。

`任务的执行`：包括Executor框架的核心接口Executor以及其子接口ExecutorService。在Executor框架中有两个关键类ThreadPoolExecutor和ScheduledThreadPoolExecutor实现了ExecutorService接口。  

`异步计算的结果`：包括接口Future和其实现类FutureTask。  

### Executor接口
是Executor的基础，接口定义如下：  

```java    
public interface Executor {
    void execute(Runnable command);
}
```    

包含有一个方法executor，参数为一个Runable接口引用。

### ThreadPoolExecutor类
是线程池的核心实现类，用来执行被提交的任务。它通常由工厂类Executors来创建，Executors可以创建

* **SingleThreadExecutor**
* **FixedThreadPool**
* **CachedThreadPool**
* **ScheduledThreadExecutor**  
  

等不同的ThreadPoolExecutor。示例如下：  

`SingleThreadExecutor`

```java 
public class SingleThreadExecutorTest {

    public static void main(String[] args) {

        ExecutorService executor = Executors.newSingleThreadExecutor();

        IntStream.range(0, 5).forEach(i -> executor.execute(() -> {
            String threadName = Thread.currentThread().getName();
            System.out.println("finished: " + threadName);
        }));

        try {
            //close pool
            executor.shutdown();
            executor.awaitTermination(5, TimeUnit.SECONDS);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            if (!executor.isTerminated()) {
                executor.shutdownNow();
            }
        }
    }
}
```  

`FixedThreadPool`  

```java  
public class FixedThreadExecutorTest {

    public static void main(String[] args) {

        ExecutorService executor = Executors.newFixedThreadPool(4);

        IntStream.range(0, 6).forEach(i -> executor.execute(() -> {
            try {
                TimeUnit.SECONDS.sleep(1);
                String threadName = Thread.currentThread().getName();
                System.out.println("finished: " + threadName);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }));

        try {
            executor.shutdown();
            executor.awaitTermination(5, TimeUnit.SECONDS);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            if (!executor.isTerminated()) {
                executor.shutdownNow();
            }
        }
    }
}
```  

`CachedThreadPool`  

```java  
public class CachedThreadExecutorTest {

    public static void main(String[] args) {
        ExecutorService executor = Executors.newCachedThreadPool();
        IntStream.range(0, 6).forEach(i -> executor.execute(() -> {
            try {
                TimeUnit.SECONDS.sleep(1);
                String threadName = Thread.currentThread().getName();
                System.out.println("finished: " + threadName);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }));

        try {
            executor.shutdown();
            executor.awaitTermination(5, TimeUnit.SECONDS);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            if (!executor.isTerminated()) {
                executor.shutdownNow();
            }
        }
    }
} 
```  

`ScheduledThreadExecutor`

```java  
public class ScheduledThreadExecutorTest {

    public static void main(String[] args) {
        ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(1);
        executor.scheduleAtFixedRate( () -> System.out.println(System.currentTimeMillis()), 1000, 2000, TimeUnit.MILLISECONDS);

    }
}
```  



