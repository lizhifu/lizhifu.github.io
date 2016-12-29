---
layout: post
title:  "Executor��ܼ��"
date:   2016-12-29 19:14:54
categories: java����
tags: java Executor
---

* content
{:toc}

##Executor���
Executor��ܣ�����Executor�ӿڽ������ύ������ִ�н��н�����ƣ�ͨ��ExecutorService�ṩ�˼�㷽ʽ���ύ����ͻ�ȡ����ִ�н������װ������ִ�еĹ��̡�
Executor����������-������ģʽ���������ύ����ִ��������߳��������ߡ�
�ṹ��ͼ��ʾ��
![executor-frame]({{"/css/pics/executor-frame.jpg"}}) 
Executor�����Ҫ�����������֣�

`����`������Runnable��Callable������Runnable��ʾһ�������첽ִ�е����񣬶�Callable��ʾһ����������������

`�����ִ��`������Executor��ܵĺ��Ľӿ�Executor�Լ����ӽӿ�ExecutorService����Executor������������ؼ���ThreadPoolExecutor��ScheduledThreadPoolExecutorʵ����ExecutorService�ӿڡ�

`�첽����Ľ��`�������ӿ�Future����ʵ����FutureTask��

###Executor�ӿ�
��Executor�Ļ������ӿڶ������£�
```java
public interface Executor {
    void execute(Runnable command);
}
```