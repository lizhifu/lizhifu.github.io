---  
layout: post  
title:  “guava Cache”  
date:   2017-04-08 12:14:13  
categories: cache    
tags: java cache  
---  

* content  
{:toc}  

凡是位于速度相差较大的两种软/硬件之间，协调两者间数据传输速度的结构，都可称为缓存。通常可用来提高系统的运行效率。  

在java技术中，通常指把常用的数据提前读取到内存中，可以减少每次都去创建实例或者IO读取等操作的开销。  






实现java缓存，最简单的即把数据保存在ConcurrentHashMap中，然后此map进行增删查操作。   
不过这种方法中对象的有效性和使用周期不可控，还容易造成OOM。因此，一个实现良好的Cache类应该能迅速查找（键值对的方式），线程安全、生存周期可配置、按需自动回收内存等机制。  

本地缓存中，google的guava Cache使用方便、也较为强大，实现原理类似ConcurrentHashMap，引入Segment数组实现锁分离提高并发性能。   

## Cache接口及其实现   

`Cache实例`  

```java  
final static Cache<Integer, String> cache = CacheBuilder.newBuilder()  
	//设置cache的初始大小为10 
	.initialCapacity(10)  
	//设置并发数为5，即同一时间最多只能有5个线程往cache执行写入操作  
	.concurrencyLevel(5)  
	//设置cache中的数据在写入之后的存活时间为10秒  
	.expireAfterWrite(10, TimeUnit.SECONDS)  
	//构建cache实例  
	.build();  
```  

`常用方法`

```java  
/** 
 * 该接口的实现被认为是线程安全的，即可在多线程中调用 
 * 通过被定义单例使用 
 */  
public interface Cache<K, V> {  
  
  /** 
   * 通过key获取缓存中的value，若不存在直接返回null 
   */  
  V getIfPresent(Object key);  
  
  /** 
   * 通过key获取缓存中的value，若不存在就通过valueLoader来加载该value 
   * 整个过程为 "if cached, return; otherwise create, cache and return" 
   * 注意valueLoader要么返回非null值，要么抛出异常，绝对不能返回null 
   */  
  V get(K key, Callable<? extends V> valueLoader) throws ExecutionException;  
  
  /** 
   * 添加缓存，若key存在，就覆盖旧值 
   */  
  void put(K key, V value);  
  
  /** 
   * 删除该key关联的缓存 
   */  
  void invalidate(Object key);  
  
  /** 
   * 删除所有缓存 
   */  
  void invalidateAll();  
  
  /** 
   * 执行一些维护操作，包括清理缓存  
   */  
  void cleanUp();  
}  
```  

## 缓存清除  

任何Cache的容量都是有限的，而缓存清除策略就是决定数据在什么时候应该被清理掉。  
Guava Cache提了以下几种清除策略：  

### 基于存活时间（Timed Eviction）   
（1）expireAfterAccess(long, TimeUnit)：缓存项在创建后，在给定时间内没有被读/写访问，则清除。  
（2）expireAfterWrite(long, TimeUnit)：缓存项在创建后，在给定时间内没有被写访问（创建或覆盖），则清除。  

### 基于容量（size-based eviction）  

通过CacheBuilder.maximumSize(long)方法可以设置Cache的最大容量数，当缓存数量达到或接近该最大值时，
Cache将清除掉那些最近最少使用的缓存。  
以上是这种方式是以缓存的“数量”作为容量的计算方式，还有另外一种基于“权重”的计算方式。比如每一项缓存所占据的内存空间大小都不一样，可以看作它们有不同的“权重”（weights）。你可以使用CacheBuilder.weigher(Weigher)指定一个权重函数，
并且用CacheBuilder.maximumWeight(long)指定最大总重。  

### 显式清除  
任何时候，你都可以显式地清除缓存项，而不是等到它被回收，Cache接口提供了如下API：  
  
（1）个别清除：Cache.invalidate(key)  
（2）批量清除：Cache.invalidateAll(keys)  
（3）清除所有缓存项：Cache.invalidateAll()  

### 基于引用（Reference-based Eviction）  

在构建Cache实例过程中，通过设置使用弱引用的键、或弱引用的值、或软引用的值，从而使JVM在GC时顺带实现缓存的清除，不过一般不轻易使用这个特性。   
（1）CacheBuilder.weakKeys()：使用弱引用存储键  
（2）CacheBuilder.weakValues()：使用弱引用存储值  
（3）CacheBuilder.softValues()：使用软引用存储值  

## 清除发生的时间  

如果设置的存活时间为一分钟，一分钟后这个key立即清除。要实现这个功能，那Cache中就必须存在线程来进行周期性地检查、清除等工作，如redis、ehcache都是这样实现的。   
然而，GuavaCache中，并不存在任何线程。它实现机制是在写操作时顺带做少量的维护工作，偶尔在读操作时做。  

因此，这种方式实现和使用会比较简单，但是也会带来问题：缓存存活时间长，一直占用内存。  
如果希望降低缓存延迟，可以通过定时任务去调用Cache.cleanUp()来实现。

## 刷新  

刷新和回收不太一样。 刷新表示为键加载新值，这个过程可以是异步的。  
在刷新操作进行时，缓存仍然可以向其他线程返回旧值，而不像回收操作，读缓存的线程必须等待新值加载完成。

## 统计    

CacheBuilder.recordStats()用来开启Guava Cache的统计功能。  
统计打开后，Cache.stats()方法会返回CacheStats对象以提供如下统计信息：  
hitRate()：缓存命中率；  
averageLoadPenalty()：加载新值的平均时间，单位为纳秒；  
evictionCount()：缓存项被回收的总数，不包括显式清除。  






