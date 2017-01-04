---
layout: post
title:  "java并发工具类"
date:   2017-01-04 15:22:14
categories: java并发
tags: java concurrent
---

## java并发工具类

### CountDownLatch(等待多线程完成)
CountDownLatch允许一个或多个线程等待其他线程完成操作。

### CyclicBarrier(同步屏障)
CyclicBarrier让一组线程到达一个屏障时被阻塞，直到最后一个线程到达屏障时，这一组线程才能继续执行。

#### CountDownLatch和CyclicBarrier区别
CountDownLatch的计数器只能使用一次，CyclicBarrier的计数器可以通过reset方法重置，因此可以处理更复杂的场景，比如出现异常可以重置计数器，并让线程重新执行。

### Semaphore(信号量)
Semaphore用于控制同时访问特定资源的线程数量，通过协调各个线程来保证合理的使用公共资源。

### Exchanger(线程间交换数据)
Exchanger是一个用于线程间协作的工具类。Exchanger提供一个同步点，在这个同步点，两个线程可以进行数据交换。这两个线程通过exchange方法交换数据，如果第一个线程先执行exchange，它会一直等待第二个线程也执行exchange方法，当两个线程都达到同步点时，这两个线程就可以交换数据。

`示例：`

```java
public class ExchangerTest {
    private static final Exchanger<String> exgr = new Exchanger<String>();
    private static ExecutorService threadPool = Executors.newFixedThreadPool(2);

    public static void main(String[] args) {
        threadPool.execute(new Runnable() {
            @Override
            public void run() {
                String a = "i am a";
                try {
                    String b = exgr.exchange(a);
                    System.out.println(b);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }
        });

        threadPool.execute(new Runnable() {
            @Override
            public void run() {
                String b = "i am b";
                try {
                    String a = exgr.exchange(b);
                    System.out.println(a);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });

        threadPool.shutdown();
    }
}
```

