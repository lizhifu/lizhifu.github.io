---
layout: post
title:  "java并发包-atomic包"
date:   2017-02-06 22:14:17
categories: java并发
tags: java并发 atomic 
---

* content
{:toc}

在java1.8中，atomic包中一共17个类，4种原子更新方式，分别是原子更新基本类型，原子更新数组，原子更新引用和原子更新字段。Atomic包里的类基本都是使用Unsafe实现的包装类。  





主要类如下所示：​

1 布尔类型的AtomicBoolean

2 整型AtomicInteger、AtomicIntegerArray、AtomicIntegerFieldUpdater

3 长整型AtomicLong、AtomicLongArray、AtomicLongFieldUpdater

4 引用型AtomicMarkableReference、AtomicReference、AtomicReferenceArray、AtomicReferenceFieldUpdater、AtomicStampedReference

5 累加器DoubleAccumulator、DoubleAdder、LongAccumulator、LongAdder、Striped64

也可分为如下4组：

1 标量类（Scalar）：AtomicBoolean，AtomicInteger，AtomicLong，AtomicReference
​
2 数组类：AtomicIntegerArray，AtomicLongArray，AtomicReferenceArray
​
3 更新器类：AtomicLongFieldUpdater，AtomicIntegerFieldUpdater，AtomicReferenceFieldUpdater
​
4 复合变量类：AtomicMarkableReference，AtomicStampedReference

5 增量类：​DoubleAccumulator、DoubleAdder、LongAccumulator、LongAdder、Striped64
​

​atomic包的作用： 在多线程环境下，无锁的进行原子操作。即当某个线程进入方法，执行其中的指令时，不会被其他线程打断。

java不能直接访问操作系统底层，只能通过本地方法来进行访问。在​Atomic包中则是主要通过Unsafe类来进行硬件级别的原子操作。

UnSafe类中常用的方法​addAndGet，getAndSet等都是通过compareAndSwap即CAS操作来实现。



​## 原子更新基本类型类

​用于通过原子的方式更新基本类型，Atomic包提供了以下三个类：

AtomicBoolean：原子更新布尔类型。
AtomicInteger：原子更新整型。
AtomicLong：原子更新长整型。
​
AtomicInteger的常用方法如下：

int addAndGet(int delta) ：以原子方式将输入的数值与实例中的值（AtomicInteger里的value）相加，并返回结果
boolean compareAndSet(int expect, int update) ：如果输入的数值等于预期值，则以原子方式将该值设置为输入的值。
int getAndIncrement()：以原子方式将当前值加1，注意：这里返回的是自增前的值。
void lazySet(int newValue)：最终会设置成newValue，使用lazySet设置值后，可能导致其他线程在之后的一小段时间内还是可以读到旧的值。


## 原子更新数组类

通过原子的方式更新数组里的某个元素，Atomic包提供了以下三个类：

AtomicIntegerArray：原子更新整型数组里的元素。
AtomicLongArray：原子更新长整型数组里的元素。
AtomicReferenceArray：原子更新引用类型数组里的元素。
​
AtomicIntegerArray类主要是提供原子的方式更新数组里的整型，其常用方法如下

int addAndGet(int i, int delta)：以原子方式将输入值与数组中索引i的元素相加。
boolean compareAndSet(int i, int expect, int update)：如果当前值等于预期值，则以原子方式将数组位置i的元素设置成update值。

## 原子更新引用类型
原子更新基本类型的AtomicInteger，只能更新一个变量，如果要原子的更新多个变量，就需要使用这个原子更新引用类型提供的类。Atomic包提供了以下三个类：

AtomicReference：原子更新引用类型。
AtomicReferenceFieldUpdater：原子更新引用类型里的字段。
AtomicMarkableReference：原子更新带有标记位的引用类型。可以原子的更新一个布尔类型的标记位和引用类型。构造方法是AtomicMarkableReference(V initialRef, boolean initialMark)

## 原子更新字段类
如果我们只需要某个类里的某个字段，那么就需要使用原子更新字段类，Atomic包提供了以下三个类：

AtomicIntegerFieldUpdater：原子更新整型的字段的更新器。
AtomicLongFieldUpdater：原子更新长整型字段的更新器。
AtomicStampedReference：原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于原子的更数据和数据的版本号，可以解决使用CAS进行原子更新时，可能出现的ABA问题。
原子更新字段类都是抽象类，每次使用都时候必须使用静态方法newUpdater创建一个更新器。原子更新类的字段的必须使用public volatile修饰符。

​
## 增量类
​
    数据 striping 就是把逻辑上连续的数据分为多个段，使这一序列的段存储在不同的物理设备上。通过把段分散到多个设备上可以增加访问并发性，从而提升总体的吞吐量。

Striped64
    Striped64是jdk1.8提供的用于支持如Long累加器，Double累加器这样机制的基础类，它用于类支持动态 striping 到 64bit 值上。设计核心思路就是通过内部的分散计算来避免竞争(比如多线程CAS操作时的竞争)。其内部包含一个基础值和一个单元哈希表。没有竞争的情况下，要累加的数会累加到这个基础值上；如果有竞争的话，会将要累加的数累加到单元哈希表中的某个单元里面。所以整个Striped64的值包括基础值和单元哈希表中所有单元的值的总和。
​
Cell 类
    Cell 类是 Striped64 的静态内部类。通过注解 @sun.misc.Contended 来自动实现缓存行填充，让Java编译器和JRE运行时来决定如何填充。本质上是一个填充了的、提供了CAS更新的volatile变量。

Striped64主要提供了longAccumulate和doubleAccumulate方法来支持子类。方法的作用是将给定的值x累加到当前值(Striped64本身)上​。

详细解析见：http://brokendreams.iteye.com/blog/2259857​

LongAdder、DoubleAdder

    LongAdder是jdk1.8提供的累加器，基于Striped64实现。它常用于状态采集、统计等场景。AtomicLong也可以用于这种场景，但在线程竞争激烈的情况下，LongAdder要比AtomicLong拥有更高的吞吐量，但会耗费更多的内存空间。

     

LongAccumulator、DoubleAccumulator

    LongAccumulator和LongAdder类似，也基于Striped64实现。但要比LongAdder更加灵活(要传入一个函数接口)，LongAdder相当于是LongAccumulator的一种特例。








