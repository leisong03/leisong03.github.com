---
layout: post
title:  "java 并发库之 Semaphore"
date:   2017-05-07 20:00:00 +0800
categories: Java 
tags: Java
author: JiuYang Chen
---



* content
{:toc}





本篇简单介绍将`java`并发库中的`Semaphore`使用。

## `Semaphore`

`java`并发库中的`Semaphore`主要完成对信号量的控制,从而实现某个资源同时可访问的用户数。


`Semaphore`维护了当前的访问数,提供同步机制,控制同时访问个数。在数据结构中链表可以保存“无限”的节点，用Semaphore可以实现有限大小的链表。


### 示例

```java

package test;

import java.util.LinkedList;
import java.util.Queue;
import java.util.UUID;
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

public class test extends Thread {

	public static void main(String[] args) {  
		
		// 创建10个访问线程,创建延时100ms
		
        int i = 0;  
        while (i < 10) {  
            i++;  
            new test().start();  
            try {  
                Thread.sleep(100);  
            } catch (InterruptedException e) {  
                e.printStackTrace();  
            }  
        }  
    }  
  
    //  `Semaphore`设置同时只能有5个访问
    static Semaphore semaphore = new Semaphore(5);  
  
    public void run() {  
        try {  
        	
            Object connec = getConnection();  
            System.out.println("---Connect---：" + connec);  
            Thread.sleep(1000);  
            releaseConnection(connec);  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
    }  
  
    public void releaseConnection(Object connec) {  
        /* `release()`释放许可 */  
        semaphore.release();  
        System.out.println("Release：" + connec);  
    }  
  
    public Object getConnection() {  
    	/* `acquire()`获取一个许可
    	 * `tryAcquire(long timeout, TimeUnit unit)`:在指定的时间内尝试地获取1个许可，如果获取不到就返回false
         */
        try {
            boolean getAccquire = semaphore.tryAcquire(500, TimeUnit.MILLISECONDS);  
            if (getAccquire) {  
                return UUID.randomUUID().toString();  
            }  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        throw new IllegalArgumentException("timeout");  
    }  

}

```

![outPut](http://wx3.sinaimg.cn/mw690/c584f169ly1fgdpubgxwzj20iu0anq3p.jpg)




















