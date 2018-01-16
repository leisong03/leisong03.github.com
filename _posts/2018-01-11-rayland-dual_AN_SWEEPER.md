---
layout: post
title:  "rayland-dual主板控制舵机"
date:   2018-01-16 07:00:00 +0800
categories: FDM 
tags:  3D打印 
author: Song Lei
typora-copy-images-to: ../images
---

* content
{:toc}
 本文介绍了如何使用RAYLAND-DUAL主板的电机输出接口来控制舵机



## 舵机基本参数

RAYLAND-DUAL可以用来控制舵机，目前暂时只支持脉冲宽度调制的舵机。脉冲宽度调制的舵机能接受1ms<2ms脉宽的。其中1ms和2ms分别对应最左和最右位置，1.5ms对应中间位置。

![F384E714-1E01-4CA5-832F-39D6FA36AAA2]({{site.baseurl}}/images/F384E714-1E01-4CA5-832F-39D6FA36AAA2.png)

这是一款典型舵机的工作参数

![DB3D289D-2ABF-4844-9981-C469354DF5E4]({{site.baseurl}}/images/DB3D289D-2ABF-4844-9981-C469354DF5E4.png)

综合上面两个图，舵机的控制典型值有下面三个

| 名称     | 典型值     |
| ------ | ------- |
| 脉冲宽度范围 | 1ms～2ms |
| 脉冲周期   | 20ms    |
| 电压     | 4.8v～6V |
| 死区     | 5us     |

脉冲宽度范围处以死去就是舵机的角度分辨率
$$
LSB = \frac{\Delta_{width}}{T}=1ms/5us = 200
$$
所以控制一个舵机的需要一个byte的数据量。



## 基本参数

| 尺寸        | 115×93 mm    |
| --------- | ------------ |
| 重量        |              |
| 工作温度      | -20～65℃      |
| 主处理器主频    | 1.4GHz       |
| 主处理器类型    | 4核 Cortex A7 |
| 协处理器      | 72MHz        |
| 内存容量      | 1Gbytes      |
| Flash     | 4Gbytes      |
| 最大外接Flash | 32Gbytes     |



