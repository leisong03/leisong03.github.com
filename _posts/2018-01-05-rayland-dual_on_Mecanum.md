---
layout: post
title:  "用rayland-dual主板控制麦克纳姆车 控制协议"
date:   2018-01-05 10:00:00 +0800
categories: AGV 
tags: AGV 
author: JiuYang Chen
typora-copy-images-to: ../_images
---



* content
{:toc}


本文简单介绍了用rayland-dual主板控制麦克纳姆车的接线方法和控制协议。





## 概述

![CBF15C15-F3E4-407B-939B-F6D3D3DA6776]({{site.baseurl}}/images/CBF15C15-F3E4-407B-939B-F6D3D3DA6776.png)

麦克纳姆车是一种很奇特的小车，它搭载了一种全向轮子。由于轮子构造的独特性，这种车可以做出各种奇怪的动作，比如左右平移，斜向运动。以下是这种车能进行的10个运动方向和每个轮子的转动方向之间的关系。

![F32A5FE1-6698-4134-96F0-BB5C4BAC61A1]({{site.baseurl}}/images/F32A5FE1-6698-4134-96F0-BB5C4BAC61A1.png)

与控制AGV类似，控制马克纳姆车也有两种控制方式。一个是板载的安卓控制，另一个是用ROS系统控制，利用安卓控制的控制协议如下。

## 用板载安卓系统控制麦克纳姆车

`Android -> Stm32`

1.1 **控制指令** `cmd_type,pkg_length,seq,is_reset,X,Y,Z,A,B,s,v`


| Param      | Describe |  Type  |   Range    |                                Unit |
| ---------- | :------: | :----: | :--------: | ----------------------------------: |
| cmd_type   |   指令类型   | uint8  |     0      |                                     |
| pkg_length |   包长度    | uint8  |  `9bytes`  |                                     |
| seq        |   指令序号   | uint8  |            |                                     |
| is_reset   |   是否抢占   | uint8  |            |                    `0:false 1:true` |
| moving_idx |    前后    | uint8  |            | 0: 前后   1: 旋转 2:平移 3:左上和右下 4: 右上和左下 |
| s          |   行驶里程   | uint16 |            |                       `1 = (1/10)r` |
| v          |   行驶速度   | int16  | -500 ~ 500 |                       `(1/10)r/min` |



2.2 **定义当前坐标** `cmd_type,pkg_length,seq,axis_x,axis_y,ori`

| Param      | Describe | Type  |    Range |                              Unit |
| ---------- | :------: | :---: | -------: | --------------------------------: |
| cmd_type   |   指令类型   | uint8 |        1 |                                   |
| pkg_length |   包长度    | uint8 | `9bytes` |                                   |
| seq        |   指令序号   | uint8 |          |                                   |
| axis_x     |   x轴坐标   | int16 |          |                     `1 = (1/10)r` |
| axis_y     |   y轴坐标   | int16 |          |                     `1 = (1/10)r` |
| ori        |    朝向    | int16 |          | `1 = 1/10°` `-1 = -1/10°` (逆时针为正) |

3.3 **切换控制模式** `cmd_type,pkg_length,seq,controll_type`

| Param         | Describe | Type  |         Range | Unit |
| ------------- | :------: | :---: | ------------: | ---: |
| cmd_type      |   指令类型   | uint8 |             2 |      |
| pkg_length    |   包长度    | uint8 |      `4bytes` |      |
| seq           |   指令序号   | uint8 |               |      |
| controll_type |   控制模式   | uint8 | `0：板控` `1：遥控` |      |

`Stm32 -> Android`

1.1 **状态采集** 

`cmd_type,pkg_length,seq,v_x,v_y,axis_x,axis_y,ori,rfid,magn,infra,ut_f,ut_b,moto_state,mile`

| Param      | Describe |     Type     |                          Range |                              Unit |
| ---------- | :------: | :----------: | -----------------------------: | --------------------------------: |
| cmd_type   |   指令类型   |    uint8     |                              0 |                                   |
| pkg_length |   包长度    |    uint8     |                      `39bytes` |                                   |
| seq        |   指令序号   |    uint8     |                                |                                   |
| v_x        |   x轴速度   |    int16     |                     -500 ~ 500 |                     `(1/10)r/min` |
| v_y        |   y轴速度   |    int16     |                     -500 ~ 500 |                     `(1/10)r/min` |
| axis_x     |   x轴坐标   |    int16     |                                |                     `1 = (1/10)r` |
| axis_y     |   y轴坐标   |    int16     |                                |                     `1 = (1/10)r` |
| ori        |    朝向    |    int16     |                                | `1 = 1/10°` `-1 = -1/10°` (逆时针为正) |
| rfid       | rfid序列号  |    uint32    |                                |                                   |
| magn       |  磁条序列号   |    uint16    |              `0:missing 1:hit` |                                   |
| infra      |    红外    |    uint8     |               `0:false 1:true` |                                   |
| ut_f       |   前侧超声   | uint16 * `4` |                                |                              `mm` |
| ut_b       |   后侧超声   | uint16 * `4` |                                |                              `mm` |
| moto_state |   电机状态   |    uint8     | `0:stop 1:running` 左侧第0位 右侧第一位 |                                   |
| mile       |   总里程    |    uint16    |                                |                              `mm` |



## 用ROS系统控制麦克纳姆车

板载的A33处理器并不能运行ROS操作系统，需要通过外接mini PC来完成。MiniPC直接和板子上的STM32进行通讯，在ros模式下，我们需要禁用板载的寻磁传感器功能，将这个口用来做miniPC和STM32的通讯。这里为了调试方便，我们暂时使用了ascii码的方式来做。

MiniPC->STM32

命令是变长的，用字母作为命令头开始，紧接着数字，最后用\n结束，例如向前运动一整圈就是

```
X10 \n
```

格式如下

| 命令头  | 数字           | 意义      |
| ---- | ------------ | ------- |
| X    | -32768～32768 | 前后运动    |
| Y    | 同上           | 左右平移    |
| Z    | 同上           | 原地旋转    |
| A    | 同上           | 左前-右后方向 |
| B    | 同上           | 右前-左后方向 |

STM32->MiniPC

命令是变长的，每行含有两个字母S和V，各自接一个数字，最后用\n结束。S后面的数字是当前里程，V后面的数字是当前速度。例如，当前已经以4 centRound/second的速度运行了500 centRound，则输出是

```
V4 S500 \n
```



## 麦克纳姆车硬件接线

| 板上电机端口 | 麦克纳姆功能链接 |                备注                |
| :----: | :------: | :------------------------------: |
| X电机端口  |    左前    | 1FR(EN)2BK(MS1)3PG(MS2)4GND(GND) |
| Y电机端口  |    右前    | 1FR(EN)2BK(MS1)3PG(MS2)4GND(GND) |
| E0电机端口 |    左后    | 1FR(EN)2BK(MS1)3PG(MS2)4GND(GND) |
| E1电机端口 |    右后    | 1FR(EN)2BK(MS1)3PG(MS2)4GND(GND) |
|        |          |                                  |

电机改成XYZE0

超声接usart2

寻磁接usart3

sv接x_max















