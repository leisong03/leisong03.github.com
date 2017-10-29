---
layout: post
title:  "ZMQ"
date:   2017-03-26 11:00:00 +0800
categories: ZMQ 
tags: ZMQ
author: JiuYang Chen
---



* content
{:toc}

本篇简单介绍`ZMQ`的使用。





## `ZMQ`简介

ZMQ被称为史上最快的消息队列,它处于会话层之上,应用层之下,使用后台异步线程完成消息的接受和发送,完美的封装了Socket API,大大简化了编程人员的复杂度。

*  ZMQ发送和接受的是具有固定长度的二进制对象,ZMQ的消息包最大254个字节，前6个字节是协议，然后是数据包。

 如果超过255个字节（有一个字节表示包属性）,则ZMQ会自动分包传输;而对于TCP Socket，是面向字节流的连接。

*  传统的TCP Socket的连接是1对1的,ZMQ的Socket可以很轻松的实现1对N，N对1和N对N的连接模式。

*  ZMQ使用异步后台线程处理接受和发送请求，这意味着发送完消息，不可以立即释放资源，消息什么时候发送用户是无法控制的，同时，ZMQ自动重连，

 这意味着用户可以以任意顺序加入到网络中，服务器也可以随时加入或者退出网络；

## `ZMQ`几种模式

1) 请求-回复模式 

这种模式主要用于从客户端向一个或多个服务器发送请求,客户端首先使用zmq_send 发送消息,再用zmq_recv来接收消息。

服务端先用zmq_recv接收消息如果收到了客户端的消息,则使用zmq_send向客户端发送消息。如此循环。形成请求-回复模式。


![outPut](http://wx4.sinaimg.cn/mw690/c584f169ly1fe3iuv6omqj20ar0833ye.jpg)

2) 发布-订阅模式（PUB-SUB）

这种模式主要用于一个服务器对应一个或多个客户端,该模式相对来说是异步的。客户端在一个循环体中执行zmq_recv来接收消息。

如果尝试向SUB socket发送消息会导致错误。

TIPS: 你无法知道订阅者从什么时候开始获取消息。即使是启动订阅者，过一段时间启动发布者。订阅者总是会错过发布者的第一条信息。

因为订阅者连接到发布者需要一点时间（虽然可能很小）。


![outPut](http://wx2.sinaimg.cn/mw690/c584f169ly1fe3iuvha7nj20d70b7jrg.jpg)

3)管道模式

这种模式会将数据发布（PUSH）到由管道排列的节点上面，数据总是沿着管道流动。每个管道至少连接了一个节点（Worker），节点不会主动从管道中获取（PULL）数据，数据会负载均衡的分配在节点上。。

如果平均的批次时间为5S。

* 1个节点，总时长 5034 ms

* 2个节点，总时长 2421 ms

* 4个节点，总时长 1018 ms


![outPut](http://wx2.sinaimg.cn/mw690/c584f169ly1fe3iuw78tgj20d70e70sw.jpg)

## 请求-回复实例 （JAVA）

**Client:**

```java

import org.zeromq.ZMQ;

public class hwclient {

    public static void main(String[] args) {
        ZMQ.Context context = ZMQ.context(1);

        //  Socket to talk to server
        System.out.println("Connecting to hello world server…");

        ZMQ.Socket requester = context.socket(ZMQ.REQ);
        requester.connect("tcp://localhost:5555");

        for (int requestNbr = 0; requestNbr != 10; requestNbr++) {
            String request = "Hello";
            System.out.println("Sending Hello " + requestNbr);
            requester.send(request.getBytes(), 0);

            byte[] reply = requester.recv(0);
            System.out.println("Received " + new String(reply) + " " + requestNbr);
        }
        requester.close();
        context.term();
    }
}

```

**Server:**

```java

import org.zeromq.ZMQ;

public class hwserver {

    public static void main(String[] args) throws Exception {
        ZMQ.Context context = ZMQ.context(1);

        //  Socket to talk to clients
        ZMQ.Socket responder = context.socket(ZMQ.REP);
        responder.bind("tcp://*:5555");

        while (!Thread.currentThread().isInterrupted()) {
            // Wait for next request from the client
            byte[] request = responder.recv(0);
            System.out.println("Received Hello");

            // Do some 'work'
            Thread.sleep(1000);

            // Send reply back to client
            String reply = "World";
            responder.send(reply.getBytes(), 0);
        }
        responder.close();
        context.term();
    }
}

```

**Client:**

![outPut](http://wx2.sinaimg.cn/mw690/c584f169ly1fe3iuvv99cj208v05kt8i.jpg)

**Server:**

![outPut](http://wx3.sinaimg.cn/mw690/c584f169ly1fe3iuuozo8j20bi06emx6.jpg)


## 发布-订阅模式实例 （JAVA）

**Client:**

```java

import org.zeromq.ZMQ;

public class hwclient {

    public static void client() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                ZMQ.Context zContext = ZMQ.context(1);
                ZMQ.Socket socket = zContext.socket(ZMQ.SUB);
                socket.connect("tcp://" + "127.0.0.1" + ":5553");
                socket.subscribe("Hello World!".getBytes());
                while(true){
                    System.out.println("client:"+new String(socket.recv(0)));
                }
					
                //socket.close();
            }
        }).start();
    }


    public static void  main(String args[]){
        client();
    }
}

```

**Server:**

```java

import org.zeromq.ZMQ;

public class hwserver {

     private static void server() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                ZMQ.Context zContext = ZMQ.context(1);
                ZMQ.Socket publisher = zContext.socket(ZMQ.PUB);
                publisher.bind("tcp://*:5553");
					while (!Thread.currentThread().isInterrupted()) {
                        try {

                            Thread.currentThread().sleep(1000);
                            String reply = "Hello World!";
                            System.out.println("server:"+reply);
                            publisher.send(reply.getBytes(), 0);

                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }

					}
                /*publisher.close ();
                zContext.term ();*/

            }
        }).start();

    }

    public static void  main(String args[]){
        server();
    }
}

```

**Client:**

![outPut](http://wx3.sinaimg.cn/mw690/c584f169ly1ffxfzivjsgj20cj06jwel.jpg)



> more in [ZMQ](http://zguide.zeromq.org/)

> more in other blog [云风](http://blog.codingnow.com/2011/02/zeromq_message_patterns.html)
