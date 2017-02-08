---
layout: post
title:  "volatile和atomic"
date:   2017-02-08 23:33:12
categories: java并发 
tags: java并发 java   
---

* content
{:toc}

volatile：

       The Java programming language provides a second mechanism, volatile fields, that is more convenient than locking for some purposes. A field may be declared volatile, in which case the Java Memory Model ensures that all threads see a consistent value for the variable.

即，如果一个字段被声明成volatile，java线程内存模型确保所有线程看到这个变量的值是一致的。  



  

## volatile的实现  

在X86处理器下，获取得到的汇编指令如下：​

`Java代码：`

```java
instance = new Singleton(); //instance是volatile变量
```  

`汇编代码：`

```  
movb $0x0,0x1104800(%esi);
lock addl $0x0,(%esp);
```  

​即处理器做了两件事：  

1.  将当前处理器缓存行的数据会写回到系统内存。  
2.  这个写回内存的操作会引起在其他CPU里缓存了该内存地址的数据无效。  

处理器为提高运行速度，不直接和内存进行通信，而是先将内存中的数据读取到缓存中再进行操作​，但操作完之后不确定何时会写到内存，如果对声明了Volatile变量进行写操作，JVM就会向处理器发送一条Lock前缀的指令，将这个变量所在缓存行的数据写回到系统内存。但是就算写回到内存，如果其他处理器缓存的值还是旧的，再执行计算操作就会有问题，所以在多处理器下，为了保证各个处理器的缓存是一致的，就会实现缓存一致性协议，每个处理器通过嗅探在总线上传播的数据来检查自己缓存的值是不是过期了，当处理器发现自己缓存行对应的内存地址被修改，就会将当前处理器的缓存行设置成无效状态，当处理器要对这个数据进行修改操作的时候，会强制重新从系统内存里把数据读到处理器缓存里。  

Lock前缀指令会引起处理器缓存回写到内存。Lock前缀指令导致在执行指令期间，声言处理器的 LOCK# 信号。在多处理器环境中，LOCK# 信号确保在声言该信号期间，处理器可以独占使用任何共享内存。（因为它会锁住总线，导致其他CPU不能访问总线，不能访问总线就意味着不能访问系统内存），但是在最近的处理器里，LOCK＃信号一般不锁总线，而是锁缓存，毕竟锁总线开销比较大。在8.1.4章节有详细说明锁定操作对处理器缓存的影响，对于Intel486和Pentium处理器，在锁操作时，总是在总线上声言LOCK#信号。但在P6和最近的处理器中，如果访问的内存区域已经缓存在处理器内部，则不会声言LOCK#信号。相反地，它会锁定这块内存区域的缓存并回写到内存，并使用缓存一致性机制来确保修改的原子性，此操作被称为“缓存锁定”，缓存一致性机制会阻止同时修改被两个以上处理器缓存的内存区域数据。  
  
一个处理器的缓存回写到内存会导致其他处理器的缓存无效。IA-32处理器和Intel 64处理器使用MESI（修改，独占，共享，无效）控制协议去维护内部缓存和其他处理器缓存的一致性。在多核处理器系统中进行操作的时候，IA-32 和Intel 64处理器能嗅探其他处理器访问系统内存和它们的内部缓存。它们使用嗅探技术保证它的内部缓存，系统内存和其他处理器的缓存的数据在总线上保持一致。例如在Pentium和P6 family处理器中，如果通过嗅探一个处理器来检测其他处理器打算写内存地址，而这个地址当前处理共享状态，那么正在嗅探的处理器将无效它的缓存行，在下次访问相同内存地址时，强制执行缓存行填充。  

    volatile仅仅用来保证该变量对所有线程的可见性，但不保证原子性。



## 什么时候使用​volatile？

1.  对变量的写操作不依赖于当前值。
2.  该变量没有包含在具有其他变量的不变式中。

**为什么使用volatile时会有如上的限制呢？**  

当用用volatile的修饰的变量进行运算时，比如volatile的integer自增操作（i++）。其实会分成三步：  
1）读取volatile变量值到local；   
2）增加变量的值；  
3）把local的值写回，让其它的线程可见。  


以上三步的指令换成汇编可简单理解为：  
    
1）mov 

2）add

3）mov

4）lock     

由以上可知，对于volatile变量进行运算操作时，只有lock的时候才有保证所有线程见到的变量值一致​，多个线程在1）mov到相同的i，在3）时则会mov回相同的i，因此不能保证原子性。

在需要变量具有可见性和原子性时，需要用AtomicXXX。

       在AtomicXXX中，通过volatile来保证可见性，使用CAS（比较并交换）算法来保证原子性。

如下图所示，AtomicInteger，值使用了volatile进行修饰。 
![atomic]({{"/css/pics/atomic.png"}}) 
