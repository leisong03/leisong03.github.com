---
layout: post
title:  " Java ThreadPoolExecutor"
date:   2017-08-23 14:00:00 +0800
categories: Java 
tags: Java 
author: JiuYang Chen
---



* content
{:toc}





本篇简单介绍`Java`中的`ThreadPoolExecutor`。

## `ThreadPoolExecutor`

`ThreadPoolExecutor`作为`java.util.concurrent`包对外提供基础实现，以内部线程池的形式对外提供管理任务执行，线程调度，线程池管理等服务。

> 使用线程池的好处是减少在创建和销毁过程上所花的时间以及系统资源开销，解决资源不足的问题。如果不是用线程池，有可能造成系统创建大量的同类线程而导致消耗完内存或者“切换过度”的问题。`From 阿里编程规范`

`ThreadPoolExecutor` 构造函数：

```java

* public ThreadPoolExecutor(int corePoolSize,
                            int maximumPoolSize,
                            long keepAliveTime,
                            TimeUnit unit,
                            BlockingQueue<Runnable> workQueue)
							
* public ThreadPoolExecutor(int corePoolSize,
                            int maximumPoolSize,
                            long keepAliveTime, 
                            TimeUnit unit, 
                            BlockingQueue<Runnable> workQueue, 
                            ThreadFactory threadFactory)

* public ThreadPoolExecutor(int corePoolSize,
                            int maximumPoolSize,
                            long keepAliveTime,
                            TimeUnit unit,
                            BlockingQueue<Runnable> workQueue,
                            RejectedExecutionHandler handler)
							
* public ThreadPoolExecutor(int corePoolSize, 
                            int maximumPoolSize, 
                            long keepAliveTime, 
                            TimeUnit unit, 
                            BlockingQueue<Runnable> workQueue, 
                            ThreadFactory threadFactory, 
                            RejectedExecutionHandler handler)

```							
							
### `ThreadPoolExecutor`参数
							
* **int corePoolSize** ：核心线程池大小

* **int maximumPoolSize** ：最大线程池大小

* **long keepAliveTime** ：线程池中超过corePoolSize数目的空闲线程最大存活时间，可以allowCoreThreadTimeOut(true)使得核心线程有效时间

* **TimeUnit unit** ：keepAliveTime时间单位

* **BlockingQueue workQueue** ：阻塞任务队列

	注：BlockingQueue 都可用于传输和保持提交的任务。可以使用此队列与池大小进行交互：

	1.1 如果运行的线程少于 corePoolSize，则 Executor 始终首选添加新的线程，而不进行排队。
	
	2.2 如果运行的线程等于或多于 corePoolSize，则 Executor 始终首选将请求加入队列，而不添加新的线程。
	
	3.3 如果无法将请求加入队列，则创建新的线程，除非创建此线程超出 maximumPoolSize，在这种情况下，任务将被拒绝

* **ThreadFactory threadFactory** ：新建线程工厂

* **RejectedExecutionHandler handler** ：当提交任务数超过maxmumPoolSize+workQueue之和时，任务会交给RejectedExecutionHandler来处理			

### 线程池配置方案

1.1 构造一个固定线程数目的线程池，配置的corePoolSize与maximumPoolSize大小相同，同时使用了一个无界LinkedBlockingQueue存放阻塞任务，因此多余的任务将存在再阻塞队列，不会由RejectedExecutionHandler处理 

```java

public static ExecutorService newFixedThreadPool(int nThreads) {  
        return new ThreadPoolExecutor(nThreads, nThreads,  
                                      0L, TimeUnit.MILLISECONDS,  
                                      new LinkedBlockingQueue<Runnable>());  
    }  

```

2.2 构造一个缓冲功能的线程池，配置corePoolSize=0，maximumPoolSize=Integer.MAX_VALUE，keepAliveTime=60s,以及一个无容量的阻塞队列 SynchronousQueue，因此任务提交之后，将会创建新的线程执行；线程空闲超过60s将会销毁 

```java

public static ExecutorService newCachedThreadPool() {  
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,  
                                      60L, TimeUnit.SECONDS,  
                                      new SynchronousQueue<Runnable>());  
    }  

```

3.3 构造一个只支持一个线程的线程池，配置corePoolSize=maximumPoolSize=1，无界阻塞队列LinkedBlockingQueue；保证任务由一个线程串行执行 

```java

public static ExecutorService newSingleThreadExecutor() {  
        return new FinalizableDelegatedExecutorService  
            (new ThreadPoolExecutor(1, 1,  
                                    0L, TimeUnit.MILLISECONDS,  
                                    new LinkedBlockingQueue<Runnable>()));  
    }  

```











