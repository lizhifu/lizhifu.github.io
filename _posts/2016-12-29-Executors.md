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
是线程池的核心实现类，用来执行被提交的任务。它通常由工厂类Executors来创建，主要由下列4个构件组成：  
* **corePool**：核心线程池的大小  
* **maximumPool**：最大线程池的大小  
* **BlockingQueue**：用来暂时保存任务的工作队列  
* **RejectedExecutionHandler**：当ThreadPoolExecutor已经关闭或ThreadPoolExecutor已经饱和
时（达到了最大线程池大小且工作队列已满），execute()方法将要调用的Handler

工厂类Executors可以创建

* **SingleThreadExecutor**    单线程的线程池
* **FixedThreadPool**    固定大小的线程池。
* **CachedThreadPool**    可缓存的线程池。
  

等不同的ThreadPoolExecutor。示例如下：  

**SingleThreadExecutor**  

`源码`  

```java  
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
            0L, TimeUnit.MILLISECONDS,
            new LinkedBlockingQueue<Runnable>()));
}  
```

SingleThreadExecutor的corePoolSize和maximumPoolSize被设置为1。其他参数与
FixedThreadPool相同。SingleThreadExecutor使用无界队列LinkedBlockingQueue作为线程池的工
作队列(列的容量为Integer.MAX_VALUE)。   
SingleThreadExecutor适用于需保证顺序执行的各个任务，并且在任意时间点，不会有多个线程是活动的场景。  


`示例：`  

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

**FixedThreadPool**  
`源码`  

```java  
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
            0L, TimeUnit.MILLISECONDS,
            new LinkedBlockingQueue<Runnable>());
}
```

FixedThreadPool的corePoolSize和maximumPoolSize都被设置为创建FixedThreadPool时指定的参数nThreads。  
当线程池中的线程数大于corePoolSize时，keepAliveTime为多余的空闲线程等待新任务的最长时间，  这个时间后多余的线程将被终止。这里把keepAliveTime设置为0L，意味着多余的空闲线程会被立即终止。  
FixedThreadPool适用于为满足资源管理而需限制当前线程数的场景，适用于负载较重的服务器。  

`示例：`  

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

**CachedThreadPool**  
`源码`  

```java  
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
            60L, TimeUnit.SECONDS,
            new SynchronousQueue<Runnable>());
}
```

CachedThreadPool的corePoolSize被设置为0，即corePool为空；maximumPoolSize被设置为Integer.MAX_VALUE，即maximumPool是无界的。这里把keepAliveTime设置为60L，意味着CachedThreadPool中的空闲线程等待新任务的最长时间为60秒，空闲线程超过60秒后将会被终止。  
CachedThreadPool是大小无界的线程池，适用于执行很多短期异步任务的小程序，或负载较轻的服务器。     


`示例：`  

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

FixedThreadPool和SingleThreadExecutor使用无界队列LinkedBlockingQueue作为线程池的工作队列。  
CachedThreadPool使用没有容量的SynchronousQueue作为线程池的工作队列，但
CachedThreadPool的maximumPool是无界的。这意味着，如果主线程提交任务的速度高于
maximumPool中线程处理任务的速度时，CachedThreadPool会不断创建新线程。极端情况下，
CachedThreadPool会因为创建过多线程而耗尽CPU和内存资源。  

                                                                ----《java并发编程的艺术》

### Future接口  
Future接口和实现Future接口的FutureTask类用来表示异步计算的结果。   
当我们把Runnable接口或Callable接口的实现类提交给ThreadPoolExecutor或ScheduledThreadPoolExecutor时，
ThreadPoolExecutor或ScheduledThreadPoolExecutor会向我们返回一个FutureTask对象。   

```java   
public interface Future<V> {
    //取消任务的执行。参数指定是否立即中断任务执行，或者等任务结束。
    boolean cancel(boolean mayInterruptIfRunning);

    //任务是否已经取消，任务正常完成前将其取消，则返回 true。
    boolean isCancelled();

    //任务是否已经完成。需要注意的是如果任务正常终止、异常或取消，都将返回true。
    boolean isDone();

    //等待任务执行结束，然后获得V类型的结果。
    //InterruptedException 线程被中断异常， ExecutionException 任务执行异常，
    //如果任务被取消，还会抛出CancellationException
    V get() throws InterruptedException, ExecutionException;

    //多了设置超时时间。参数timeout指定超时时间，uint指定时间的单位，在枚举类TimeUnit中有相关的定义。
    //如果计算超时，将抛出TimeoutException
    V get(long timeout, TimeUnit unit)
            throws InterruptedException, ExecutionException, TimeoutException;
}
```

### Runnable接口和Callable接口  
Runnable接口和Callable接口的实现类，都可以被ThreadPoolExecutor或Scheduled-
ThreadPoolExecutor执行。它们之间的区别是Runnable不会返回结果，而Callable可以返回结果。  
除了可以自己创建实现Callable接口的对象外，还可以使用工厂类Executors来把一个Runnable包装成一个Callable。  

```java  
public interface Callable<V> {  
    /** 
     * Computes a result, or throws an exception if unable to do so. 
     * 
     * @return computed result 
     * @throws Exception if unable to compute a result 
     */  
    V call() throws Exception;  
}  
  
  
public interface Runnable {  
     
    public abstract void run();  
}  
```  

由上可见：  

*  Callable需要实现call方法，Runnable需要实现run方法；  
*  Callable有返回类型V，Runnable则没有；  
*  Callable抛出异常，Runnable没有。  

```java  
//接口ExecutorService继承自Executor，它的目的是为我们管理Thread对象，从而简化并发编程  
public interface ExecutorService extends Executor {  
  
    <T> Future<T> submit(Callable<T> task);  
     
    <T> Future<T> submit(Runnable task, T result);  
  
    Future<?> submit(Runnable task);  
      
    ...     
}  
```  

用Executor构建线程池：  
1. 调用Executors类中的静态方法newCachedThreadPool或newFixedThreadPool等，返回的是一个实现了ExecutorService接口的ThreadPoolExecutor类或者是一个实现了ScheduledExecutorServiece接口的类对象；  
2. 调用submit提交Runnable或Callable对象；  
3. 如果想要取消一个任务，或如果提交Callable对象，那就要保存好返回的Future对象；  
4. 当不再提交任何任务时，调用shutdown方法。  

`示例：`

```java
public class ThreadTest {

    public static void main(String[] args) {
        ExecutorService executorService = Executors.newFixedThreadPool(10);
        executorService.execute(new RunnableA());
        executorService.execute(new RunnableB());
        Future future = executorService.submit(new MyCallable());

        try {
            System.out.println(future.get());
        } catch (InterruptedException e1) {
            e1.printStackTrace();
        } catch (ExecutionException e1) {
            e1.printStackTrace();
        }
        executorService.shutdown();
    }
}

class RunnableA implements Runnable {
    @Override
    public void run() {
        System.out.println("RunnableA");
    }
}

class RunnableB implements Runnable {
    @Override
    public void run() {
        System.out.println("RunnableB");
    }
}

class MyCallable implements Callable<String> {
    @Override
    public String call() throws Exception {
        return "MyCallable";
    }
}
```




