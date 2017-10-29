---
layout: post
title:  "java Netty tcp通讯"
date:   2017-05-08 20:00:00 +0800
categories: Java 
tags: Java Netty tcp
author: JiuYang Chen
---



* content
{:toc}





本篇简单介绍`java`基于高性能网络框架`Netty`的`tcp`通讯。

## `Netty`

`Netty`的强大之处在于,它的高度抽象和封装。使用者无需关心内部实现。只需要修改相关`handler`类即可。

### 客户端



```java

package tcp;

import io.netty.bootstrap.Bootstrap;
import io.netty.channel.Channel;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelOption;
import io.netty.channel.ChannelPipeline;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.LengthFieldBasedFrameDecoder;
import io.netty.handler.codec.LengthFieldPrepender;
import io.netty.handler.codec.serialization.ClassResolvers;
import io.netty.handler.codec.serialization.ObjectDecoder;
import io.netty.handler.codec.serialization.ObjectEncoder;


public class TcpClient {

    /* Server Ip */
	public static String HOST = "127.0.0.1";
	/* Server Port */
	public static int PORT = 12340;

	public static Bootstrap bootstrap = getBootstrap();
	public static Channel channel = getChannel(HOST, PORT);

	// 初始化 `Bootstrap`
	public static final Bootstrap getBootstrap() {
		EventLoopGroup group = new NioEventLoopGroup();
		Bootstrap b = new Bootstrap();
		b.group(group).channel(NioSocketChannel.class);
		b.handler(new ChannelInitializer<Channel>() {
			@Override
			protected void initChannel(Channel ch) throws Exception {
			    
				// 每个 `Channel` 都关联一个 `ChannelPipeline`
				/* 发送和接收的 `object`通过`ObjectDecoder` `ObjectEncoder`进行加解密
				 * 注：对应`object`类,必须实现`Serializable`接口
				 *
				 * `netty`框架本身自带了很多`Encode`和`DeCode`
				 *  例如：字符串的 `StringDecoder` `StringEncoder`
				 */
				 
				ChannelPipeline pipeline = ch.pipeline();
				pipeline.addLast("frameDecoder", new LengthFieldBasedFrameDecoder(Integer.MAX_VALUE, 0, 4, 0, 4));
				pipeline.addLast("frameEncoder", new LengthFieldPrepender(4));
				pipeline.addLast(new ObjectEncoder());
				pipeline.addLast(new ObjectDecoder(ClassResolvers.cacheDisabled(null)));
				pipeline.addLast("handler", new TcpClientHandler());
			}
		});
		b.option(ChannelOption.SO_KEEPALIVE, true);
		return b;
	}

	// 建立连接
	public static final Channel getChannel(String host, int port) {
		Channel channel = null;
		try {
			channel = bootstrap.connect(host, port).sync().channel();
		} catch (Exception e) {
			System.out.println("连接Server(IP{},PORT{})失败");
			return null;
		}
		return channel;
	}

	// 向服务器发送消息
	public static void sendMsg(Object msg) throws Exception {
		if (channel != null) {
			channel.writeAndFlush(msg).sync();
		} else {
			System.out.println("消息发送失败,连接尚未建立!");
		}
	}

}


```

客户端对应的`handler`。

```java

package tcp;

import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;


public class TcpClientHandler extends SimpleChannelInboundHandler<Object> {

    // 从服务器接收到的信息 `Object`
	@Override
	protected void channelRead0(ChannelHandlerContext ctx, Object msg) throws Exception {
		
	}
}

```



### 服务端

```java

package tcp;


import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelPipeline;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.LengthFieldBasedFrameDecoder;
import io.netty.handler.codec.LengthFieldPrepender;
import io.netty.handler.codec.serialization.ClassResolvers;
import io.netty.handler.codec.serialization.ObjectDecoder;
import io.netty.handler.codec.serialization.ObjectEncoder;


public class TcpServer {
	private static final String IP = "192.168.1.154";
	private static final int PORT = 12340;
	/** 用于分配处理业务线程的线程组个数 */
	protected static final int BIZGROUPSIZE = Runtime.getRuntime().availableProcessors() * 2; // 默认
	/** 业务出现线程大小 */
	protected static final int BIZTHREADSIZE = 4;
	/*
	 * NioEventLoopGroup实际上就是个线程池,
	 * NioEventLoopGroup在后台启动了n个NioEventLoop来处理Channel事件,
	 * 每一个NioEventLoop负责处理m个Channel,
	 * NioEventLoopGroup从NioEventLoop数组里挨个取出NioEventLoop来处理Channel
	 */
	private static final EventLoopGroup bossGroup = new NioEventLoopGroup(BIZGROUPSIZE);
	private static final EventLoopGroup workerGroup = new NioEventLoopGroup(BIZTHREADSIZE);

	public static void run() throws Exception {
		ServerBootstrap b = new ServerBootstrap();
		b.group(bossGroup, workerGroup);
		b.channel(NioServerSocketChannel.class);
		b.childHandler(new ChannelInitializer<SocketChannel>() {
			@Override
			public void initChannel(SocketChannel ch) throws Exception {
				ChannelPipeline pipeline = ch.pipeline();
				pipeline.addLast("frameDecoder", new LengthFieldBasedFrameDecoder(Integer.MAX_VALUE, 0, 4, 0, 4));
				pipeline.addLast("frameEncoder", new LengthFieldPrepender(4));
				pipeline.addLast(new ObjectEncoder());
				pipeline.addLast(new ObjectDecoder(ClassResolvers.cacheDisabled(null)));

				pipeline.addLast(new TcpServerHandler());
			}
		});

		b.bind(IP, PORT).sync();

		System.out.println("TCP服务器已启动");
	}

	protected static void shutdown() {
		workerGroup.shutdownGracefully();
		bossGroup.shutdownGracefully();
	}

	public static void main(String[] args) throws Exception {
		System.out.println("启动TCP服务器...");
		TcpServer.run();
		// TcpServer.shutdown();
	}
}

```

服务器对应的`handler`。

```java

package tcp;

import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.channel.socket.SocketChannel;


public class TcpServerHandler extends SimpleChannelInboundHandler<Object> {

    // 从客户端接收到的消息
	/*  
	 *  服务器向指定客户端发送消息,只需要通过`map`将客户端的`id`和`channel`存起来
	 *  在需要的时候通过`writeAndFlush`方法发送即可
	 */
	@Override
	protected void channelRead0(ChannelHandlerContext ctx, Object msg) throws Exception {
		
	}

	@Override
	public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
		ctx.close();
	}


}

```

### `SimpleChannelInboundHandler`生命周期

如上开始所说我们只需要处理相应的`handler`即可`ChannelHandler`。
我们只需要根据业务需要在相应的方法里面做业务处理即可。

`ChannelHandler`的子接口`ChannelInboundHandler`处理进站数据,`ChannelOutboundHandler` 处理出站数据，允许拦截各种操作。

#### `ChannelInboundHandler`

`ChannelInboundHandler` 生命周期对应的方法

* channelRegistered           channel被注册到EventLoop并且可以处理io

* channelUnregistered         channel从EventLoop卸载，并且不能处理io

* channelActive               channel变为active模式，通道connected/boundb准备好了

* channelInactive             channel不活跃，不再连接远程的  

* channelReadComplete         channel上的读操作完成了

* channelRead                 数据从Channel中读出了

* channelWritabilityChanged   Channel的读写性改变时调用，

* userEventTriggered(...)     用户调用Channel.fireUserEventTriggered(...)，从ChannelPipeline传递特定的消息


#### `ChannelOutboundHandler`

`ChannelOutboundHandler`供了出站的方法，这些方法会被`Channel`, `ChannelPipeline`, 和 `ChannelHandlerContext`调用

* bind        请求绑定Channel到一个本地地址

* connect     请求连接Channel到远端

* disconnect  请求从远端断开Channel

* close       请求关闭Channel

* deregister  请求Channel从它的EventLoop上解除注册

* read        请求从Channel中读更多的数据
 
* write       请求通过Channel刷队列数据到远端

* flush       请求通过Channel写数据到远端




















