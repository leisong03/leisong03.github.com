---
layout: post
title:  "java 并发库之 Executors"
date:   2017-05-09 20:00:00 +0800
categories: Java 
tags: Java
author: JiuYang Chen
---



* content
{:toc}





本篇简单介绍`java`并发库的`Executors`概念。

## `new Thread弊端`

```java

new Thread(new Runnable() {
 
	@Override
	public void run() {
		// TODO Auto-generated method stub
	}
}).start();

```

`new Thread` 的弊端如下：

* a. 每次new Thread新建对象性能差
* b. 线程缺乏统一管理，可能无限制新建线程，相互之间竞争，及可能占用过多系统资源导致死机或oom
* c. 缺乏更多功能，如定时执行、定期执行、线程中断

相比`new Thread`，`Java`提供的四种线程池的好处在于：

* a. 重用存在的线程，减少对象创建、消亡的开销，性能佳。
* b. 可有效控制最大并发线程数，提高系统资源的使用率，同时避免过多资源竞争，避免堵塞。
* c. 提供定时执行、定期执行、单线程、并发数控制等功能。

## `Executors`

`Executors` 线程池

* newFixedThreadPool：返回一个包含指定数目线程的线程池，如果任务数量多于线程数目，那么没有没有执行的任务必须等待，直到有任务完成为止。

* newSingleThreadExecutor：返回以个包含单线程的Executor,将多个任务交给此Exector时，这个线程处理完一个任务后接着处理下一个任务，若该线程出现异常，将会有一个新的线程来替代。

* newCachedThreadPool：根据用户的任务数创建相应的线程来处理，该线程池不会对线程数目加以限制，完全依赖于JVM能创建线程的数量，可能引起内存不足。

```java

package concurrent;

import java.util.concurrent.ExecutorService;

import java.util.concurrent.Executors;

public class MyExecutor extends Thread {
	private int index;
	public MyExecutor(int i) {
		this.index = i;
	}
	public void run() {
		try {
			System.out.println("[" + this.index + "] start....");
			Thread.sleep((int) (Math.random() * 1000));
			System.out.println("[" + this.index + "] end.");
		}catch (Exception e) {
			e.printStackTrace();
		}
	}

	public static void main(String args[]) {
		//接口 Java.util.concurrent.ExecutorService 表述了异步执行的机制，并且可以让任务在后台执行。
		/*
		 * execute(Runnable):接收一个runnable 并且异步执行
		 * submit(Runnable) ：接收一个runnable ,返回Future 对象。这個 Future 对象可以用于判断 Runnable 是否结束执行
		 *                    Future future.get() 返回null 表示任务结束 
		 * 
		 */

		ExecutorService service1 = Executors.newFixedThreadPool(4);
		ExecutorService service2 = Executors.newSingleThreadExecutor();  
		ExecutorService service3 = Executors.newCachedThreadPool();  
		for (int i = 0; i < 10; i++) {
			service3.execute(new MyExecutor(i));
		}
		System.out.println("submit finish");
		service3.shutdown();
	}

}


```

从左到右,依次为 `newFixedThreadPool`,`newSingleThreadExecutor`,`newCachedThreadPool`

![outPut](http://wx1.sinaimg.cn/mw690/c584f169gy1fibeu3n1kqj209f0a4aa3.jpg)


## `Future`

上一节`Executors`中,接口 `Java.util.concurrent.ExecutorService`中的`submit(Runnable)`就会返回一个`Future`对象。

`Future` 表示异步计算的结果。它提供了检查计算是否完成的方法，以等待计算的完成，并检索计算的结果。

计算完成之后只能通过,`Future future.get()` 来获取结果。


## `2017/10/24 ` `update`

> `阿里编码规范` 告诉我们

```java

ExecutorService service2 = Executors.newSingleThreadExecutor();  

```

![outPut](http://wx4.sinaimg.cn/mw690/c584f169ly1fkt4ee84cyj20n20px0uq.jpg)


所以要改成[ThreadPoolExecutor](https://chenjy1225.github.io/2017/08/23/java-ThreadPoolExecutor/) 线程池这种形式。

```java

//如

ExecutorService service2 = Executors.newSingleThreadExecutor();  

//改为

ExecutorService service2 = new ThreadPoolExecutor(1, 1,0L, TimeUnit.MILLISECONDS,new LinkedBlockingQueue<Runnable>()));  

//或是

ThreadFactory factory = new ThreadFactoryBuilder().setNameFormat("thread-pool-%d").build();
ExecutorService service2 = new ThreadPoolExecutor(1,1,0L, TimeUnit.MILLISECONDS,new LinkedBlockingDeque<Runnable>(),factory);

```











