---
layout: post
title:  "java阻塞队列"
date:   2017-01-11 01:02:14
categories: java并发 
tags: java queue
---

* content
{:toc}

阻塞队列是支持插入和移除方法的队列。  

*  插入方法： 当队列满时，队列会阻塞插入元素的线程； 
*  移除方法： 当队列空时，获取元素的线程会等待队列变非空.
阻塞队列常用语生产者和消费者场景，生产者向队列里添加元素，消费者从队列里取出元素。  






阻塞队列不可用时，对队列的操作有四种处理方式：  

*  抛出异常  
*  返回特殊值  
*  一直阻塞  
*  超时退出  

### 抛出异常  
当队列满时，再往队列里插入元素，会抛出IllegalStateException（Queue full）异常。  
当队列空时，再从队列里获取元素，会抛出NoSuchElementException异常。  

### 返回特殊值  
插入元素时，成功则返回true。  
取出元素时，没有则返回null。  

### 一直阻塞  
当队列满时，如果生产者插入元素，队列一直阻塞生产者，直到队列可用或响应中断退出。  
当队列空时，如果消费者获取元素，队列一直阻塞消费者，直到队列不为空。

### 超时退出  
当队列满时，生产者插入元素，队列会阻塞生产者线程一段时间，超过则退出。  


## java里阻塞队列  
jdk1.7提供了7个阻塞队列:  
  
* ArrayBlockingQueue: 数组结构组成的有界阻塞队列。  
* LinkedBlockingQueue： 链表结构组成的有界阻塞队列。  
* PriorityBlockingQueue： 优先级排序的无界阻塞队列。  
* DelayQueue： 优先级队列实现的无界阻塞队列。  
* SynchronousQueue： 不存储元素的阻塞队列。  
* LinkedTransferQueue： 链表结构组成的无界阻塞队列。  
* LinkedBlockingDeque： 链表结构组成的双向阻塞队列。 
