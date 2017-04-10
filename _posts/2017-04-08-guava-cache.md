---
layout: post
title:  ��guava Cache��
date:   2017-04-08 12:14:13
categories: cache
tags: java cache
---

* content
{:toc}

����λ���ٶ����ϴ��������/Ӳ��֮�䣬Э�����߼����ݴ����ٶȵĽṹ�����ɳ�Ϊ���档ͨ�����������ϵͳ������Ч�ʡ�

��java�����У�ͨ��ָ�ѳ��õ�������ǰ��ȡ���ڴ��У����Լ���ÿ�ζ�ȥ����ʵ������IO��ȡ�Ȳ����Ŀ�����






ʵ��java���棬��򵥵ļ������ݱ�����ConcurrentHashMap�У�Ȼ���map������ɾ�������   
�������ַ����ж������Ч�Ժ�ʹ�����ڲ��ɿأ����������OOM����ˣ�һ��ʵ�����õ�Cache��Ӧ����Ѹ�ٲ��ң���ֵ�Եķ�ʽ�����̰߳�ȫ���������ڿ����á������Զ������ڴ�Ȼ��ơ�  

���ػ����У�google��guava Cacheʹ�÷��㡢Ҳ��Ϊǿ��ʵ��ԭ������ConcurrentHashMap������Segment����ʵ����������߲������ܡ�  

## Cache�ӿڼ���ʵ��  

`Cacheʵ��`  

```java  
final static Cache<Integer, String> cache = CacheBuilder.newBuilder()  
	//����cache�ĳ�ʼ��СΪ10 
	.initialCapacity(10)  
	//���ò�����Ϊ5����ͬһʱ�����ֻ����5���߳���cacheִ��д�����  
	.concurrencyLevel(5)  
	//����cache�е�������д��֮��Ĵ��ʱ��Ϊ10��  
	.expireAfterWrite(10, TimeUnit.SECONDS)  
	//����cacheʵ��  
	.build();  
```  

`���÷���`

```java  
/** 
 * �ýӿڵ�ʵ�ֱ���Ϊ���̰߳�ȫ�ģ������ڶ��߳��е��� 
 * ͨ�������嵥��ʹ�� 
 */  
public interface Cache<K, V> {  
  
  /** 
   * ͨ��key��ȡ�����е�value����������ֱ�ӷ���null 
   */  
  V getIfPresent(Object key);  
  
  /** 
   * ͨ��key��ȡ�����е�value���������ھ�ͨ��valueLoader�����ظ�value 
   * ��������Ϊ "if cached, return; otherwise create, cache and return" 
   * ע��valueLoaderҪô���ط�nullֵ��Ҫô�׳��쳣�����Բ��ܷ���null 
   */  
  V get(K key, Callable<? extends V> valueLoader) throws ExecutionException;  
  
  /** 
   * ��ӻ��棬��key���ڣ��͸��Ǿ�ֵ 
   */  
  void put(K key, V value);  
  
  /** 
   * ɾ����key�����Ļ��� 
   */  
  void invalidate(Object key);  
  
  /** 
   * ɾ�����л��� 
   */  
  void invalidateAll();  
  
  /** 
   * ִ��һЩά������������������  
   */  
  void cleanUp();  
}  
```  

## �������  

�κ�Cache�������������޵ģ�������������Ծ��Ǿ���������ʲôʱ��Ӧ�ñ��������  
Guava Cache�������¼���������ԣ�  

### ���ڴ��ʱ�䣨Timed Eviction��   
��1��expireAfterAccess(long, TimeUnit)���������ڴ������ڸ���ʱ����û�б���/д���ʣ��������  
��2��expireAfterWrite(long, TimeUnit)���������ڴ������ڸ���ʱ����û�б�д���ʣ������򸲸ǣ����������  

### ����������size-based eviction��  

ͨ��CacheBuilder.maximumSize(long)������������Cache������������������������ﵽ��ӽ������ֵʱ��
Cache���������Щ�������ʹ�õĻ��档  
���������ַ�ʽ���Ի���ġ���������Ϊ�����ļ��㷽ʽ����������һ�ֻ��ڡ�Ȩ�ء��ļ��㷽ʽ������ÿһ�����ռ�ݵ��ڴ�ռ��С����һ�������Կ��������в�ͬ�ġ�Ȩ�ء���weights���������ʹ��CacheBuilder.weigher(Weigher)ָ��һ��Ȩ�غ�����
������CacheBuilder.maximumWeight(long)ָ��������ء�  

### ��ʽ���  
�κ�ʱ���㶼������ʽ���������������ǵȵ��������գ�Cache�ӿ��ṩ������API��  
  
��1�����������Cache.invalidate(key)  
��2�����������Cache.invalidateAll(keys)  
��3��������л����Cache.invalidateAll()  

### �������ã�Reference-based Eviction��  

�ڹ���Cacheʵ�������У�ͨ������ʹ�������õļ����������õ�ֵ���������õ�ֵ���Ӷ�ʹJVM��GCʱ˳��ʵ�ֻ�������������һ�㲻����ʹ��������ԡ�   
��1��CacheBuilder.weakKeys()��ʹ�������ô洢��  
��2��CacheBuilder.weakValues()��ʹ�������ô洢ֵ  
��3��CacheBuilder.softValues()��ʹ�������ô洢ֵ  

## ���������ʱ��  

������õĴ��ʱ��Ϊһ���ӣ�һ���Ӻ����key���������Ҫʵ��������ܣ���Cache�оͱ�������߳������������Եؼ�顢����ȹ�������redis��ehcache��������ʵ�ֵġ�   
Ȼ����GuavaCache�У����������κ��̡߳���ʵ�ֻ�������д����ʱ˳����������ά��������ż���ڶ�����ʱ����  

��ˣ����ַ�ʽʵ�ֺ�ʹ�û�Ƚϼ򵥣�����Ҳ��������⣺������ʱ�䳤��һֱռ���ڴ档  
���ϣ�����ͻ����ӳ٣�����ͨ����ʱ����ȥ����Cache.cleanUp()��ʵ�֡�

## ˢ��  

ˢ�ºͻ��ղ�̫һ���� ˢ�±�ʾΪ��������ֵ��������̿������첽�ġ�  
��ˢ�²�������ʱ��������Ȼ�����������̷߳��ؾ�ֵ����������ղ�������������̱߳���ȴ���ֵ������ɡ�

## ͳ��    

CacheBuilder.recordStats()��������Guava Cache��ͳ�ƹ��ܡ�  
ͳ�ƴ򿪺�Cache.stats()�����᷵��CacheStats�������ṩ����ͳ����Ϣ��  
hitRate()�����������ʣ�  
averageLoadPenalty()��������ֵ��ƽ��ʱ�䣬��λΪ���룻  
evictionCount()����������յ���������������ʽ�����  






