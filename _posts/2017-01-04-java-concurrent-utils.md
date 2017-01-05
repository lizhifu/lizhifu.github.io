---
layout: post
title:  "java并发工具类"
date:   2017-01-04 15:22:14
categories: java并发
tags: java concurrent
---

在java的并发包里提供了几个常用的并发工具类，如CountDownLatch,CyclicBarrier和Semaphore提供了并发流程控制的方法，Exchanger提供了线程间交换数据的方法。  





## CountDownLatch(等待多线程完成)
CountDownLatch允许一个或多个线程等待其他线程完成操作。  

`示例：`  

```java  
public class CountDownLatchTest {
    private static final int N = 10;

    public static void main(String[] args) throws InterruptedException {
        CountDownLatch doneSignal = new CountDownLatch(N);
        CountDownLatch startSignal = new CountDownLatch(1);//开始执行信号

        for (int i = 1; i <= N; i++) {
            new Thread(new Worker(i, doneSignal, startSignal)).start();//线程启动了
        }
        System.out.println("begin------------");
        startSignal.countDown();//开始执行
        doneSignal.await();//等待所有的线程执行完毕
        System.out.println("end--------------");

    }

    static class Worker implements Runnable {
        private final CountDownLatch doneSignal;
        private final CountDownLatch startSignal;
        private int beginIndex;

        Worker(int beginIndex, CountDownLatch doneSignal,
               CountDownLatch startSignal) {
            this.startSignal = startSignal;
            this.beginIndex = beginIndex;
            this.doneSignal = doneSignal;
        }

        public void run() {
            try {
                startSignal.await(); //等待开始执行信号的发布
                beginIndex = (beginIndex - 1) * 10 + 1;
                for (int i = beginIndex; i < beginIndex + 10; i++) {
                    System.out.println(i);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                doneSignal.countDown();
            }
        }
    }
}
```  

## CyclicBarrier(同步屏障)
CyclicBarrier让一组线程到达一个屏障时被阻塞，直到最后一个线程到达屏障时，这一组线程才能继续执行。  

`示例：`  
```java   
public class CyclicBarrierTest {

    public static void main(String[] args) {

        ExecutorService exec = Executors.newCachedThreadPool();
        final Random random = new Random();

        final CyclicBarrier barrier = new CyclicBarrier(4, () -> System.out.println("准备完毕"));

        for (int i = 0; i < 4; i++) {
            exec.execute(() -> {
                try {
                    Thread.sleep(random.nextInt(1000));
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "  准备就绪");
                try {
                    barrier.await();   //等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            });
        }
        exec.shutdown();
    }
}

```  

### CountDownLatch和CyclicBarrier区别
CountDownLatch的计数器只能使用一次，CyclicBarrier的计数器可以通过reset方法重置，因此可以处理更复杂的场景，比如出现异常可以重置计数器，并让线程重新执行。

## Semaphore(信号量)
Semaphore用于控制同时访问特定资源的线程数量，通过协调各个线程来保证合理的使用公共资源。  
应用场景： 适用公共资源有限时的流量控制，比如需要和数据库交互而数据库连接有限，此时必须控制线程的数量小于等于数据库
，否则会无法获取连接。可以通过Semaphore做流量控制。  

`示例：` 
```java
public class SemaphoreTest {
    private static final int max_database_count = 10;
    private static final int thread_count = 20;
    private static ExecutorService executorService = Executors.newFixedThreadPool(thread_count);

    private static Semaphore semaphore = new Semaphore(max_database_count);

    public static void main(String[] args) {
        for (int i = 0 ; i < thread_count ; i++ ) {
            executorService.execute(new Runnable() {
                @Override
                public void run() {
                    try {
                        semaphore.acquire();
                        System.out.println("save data");
                        semaphore.release();
                    } catch (InterruptedException e) {

                    }
                }
            });
        }
        executorService.shutdown();
    }
}
```  

在示例中，有20个线程在执行，然而最多只有10个并发执行。

## Exchanger(线程间交换数据)
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

