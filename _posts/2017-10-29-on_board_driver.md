---
layout: post
title: 4988 vs DRV8825
date: '2017-10-29 21:00:00 +0800'
categories: FDM
tags: FDM 3D打印
author: Song Lei
published: true
---

* content
{:toc}

# 4988 和DRV8825的性能对比
4988 是这个样子的 

![4988]({{site.baseurl}}/images/4988.png)

DRV8825是这个样子的

![8825]({{site.baseurl}}/images/8825.png)

DRV8729是这个样子的
![8729]({{site.baseurl}}/images/LV8729.png)

这两个驱动器的管脚分布和现在dual板子不兼容

![socket_on_dual]({{site.baseurl}}/images/dual_socket.png)

如果想要将公版的4988和8825以及8729插入dual中，我们需要对参考电压vref的输出进行改变，让他恒定输出3.3V

这个可以通过修改HEX来实现,下午需要做一下测试
