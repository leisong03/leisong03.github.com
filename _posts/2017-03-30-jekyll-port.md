---
layout: post
title:  "blog 运行端口占用"
date:   2017-03-30 11:00:00 +0800
categories: jekyll 
tags: jekyll
author: JiuYang Chen
---



* content
{:toc}





jekyll 错误：Permission denied - bind for 127.0.0.1:4000

## Permission denied

本地的`4000`端口被占用。

错误：

![outPut](http://wx2.sinaimg.cn/mw690/c584f169gy1fem01b5mohj20gy02rjra.jpg)

1.1 查看端口占用情况

```xml

$ netstat -ano

```

![outPut](http://wx4.sinaimg.cn/mw690/c584f169gy1fem01cbyy6j20gx00i0sh.jpg)

参数：

* `-a`：显示所有链接和侦听端口

* `-n`：以数字形式显示地址和端口号而不会去尝试进行外部地址解析，能够显著的提高执行速度

* `-o`：显示拥有与每一个链接关联的 `PID`


2.2 查看当前占用端口服务

```xml

$ tasklist /svc /FI "PID eq 2852"

```

![outPut](http://wx4.sinaimg.cn/mw690/c584f169gy1fem01cnxtmj20ik04gt8k.jpg)

为福昕阅读器,默认端口为`4000`。

参数：

* `/svc`：如果这个进程是一个 `Windows` 服务的话同时显示这个服务的名称

* `/FI`：使用筛选器对结果进行筛选


3.3 关闭`4000`端口服务

可以在任务管理器,找到对应PID关闭服务。

![outPut](http://wx1.sinaimg.cn/mw690/c584f169gy1fem08jifucj20ar03lq2t.jpg)
















