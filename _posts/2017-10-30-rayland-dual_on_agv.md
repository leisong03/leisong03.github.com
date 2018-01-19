---
layout: post
title:  "用锐蓝dual主板控制AGV小车"
date:   2017-10-30 14:00:00 +0800
categories: AGV 
tags: AGV 
author: JiuYang Chen
typora-copy-images-to: ../images
---



* content
{:toc}



本文档介绍了用rayland-dual主板控制小车的控制协议。协议共分两种，1，用内置的安卓系统。2，用外置的ROS系统。





![1851AC83-793D-4179-870E-669D8F8F48C7]({{site.baseurl}}/images/1851AC83-793D-4179-870E-669D8F8F48C7.png)



## AGV小车接线方法

上图所示AGV小车的外设默认配置

| 设备      | 个数         |
| ------- | ---------- |
| 7寸高清触摸屏 | 1          |
| 超声测距    | 8          |
| RFID    | 1          |
| 16通道寻磁  | 1          |
| 麦克风     | 1（可选4通道）   |
| 2w扬声器   | 1          |
| UVC摄像头  | 1（可选扫码摄像头） |

Dual板配置

| 项目   | 配置             |
| ---- | -------------- |
| 处理器  | 全志A33 4核心      |
| RAM  | 1GB            |
| ROM  | 4GB            |
| WiFi | RTL8189 2.4G单频 |

总体接线图

| 板上电机端口 | AGV功能链接 | 备注                                       |
| ------ | ------- | ---------------------------------------- |
| X电机端口  | 左轮驱动    | 1EN(EN)，2FR(MS1)，3PG(MS2)，4BK(ST)        |
| Y电机端口  | 右轮驱动    | 1EN(EN)，2FR(MS1)，3PG(MS2)，4BK(ST)        |
| Z电机端口  | 无       |                                          |
| E0电机端口 | 左右轮SV   | 1GND，2 SV_左，3 SV_右，4GND 直连板接E0DIR和E0EN   |
| E1电机端口 | 寻磁      | 1GND，2TX，3RX，4GND。(E1DIR和usart3_RX复用，E1STEP和usart3_tx复用) |
| usart1 | rfid    | 1GND，2TX，3RX，4GND                        |
| usart2 | 超声      | 1GND，2TX，3RX，4GND                        |
| usb口   | 摄像头     | VLC免驱摄像头                                 |
| 音频输入   | 单mic    |                                          |
| 音频输出   | 运放模块    |                                          |

## 安卓系统下的AGV 小车控制协议

##  AGV

`Android -> Stm32`

1.1 **控制指令** `cmd_type,pkg_length,seq,v_x,v_y,s_x,s_y`


| Param      | Describe |  Type  |   Range    |             Unit |
| ---------- | :------: | :----: | :--------: | ---------------: |
| cmd_type   |   指令类型   | uint8  |     0      |                  |
| pkg_length |   包长度    | uint8  | `12bytes`  |                  |
| seq        |   指令序号   | uint8  |            |                  |
| is_reset   |   是否抢占   | uint8  |            | `0:false 1:true` |
| v_x        |   x轮速度   | int16  | -500 ~ 500 |    `(1/10)r/min` |
| v_y        |   y轮速度   | int16  | -500 ~ 500 |    `(1/10)r/min` |
| s_x        |   x轮里程   | uint16 |            |    `1 = (1/10)r` |
| s_y        |   y轮里程   | uint16 |            |    `1 = (1/10)r` |



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

| Param         | Describe | Type  |                   Range | Unit |
| ------------- | :------: | :---: | ----------------------: | ---: |
| cmd_type      |   指令类型   | uint8 |                       2 |      |
| pkg_length    |   包长度    | uint8 |                `4bytes` |      |
| seq           |   指令序号   | uint8 |                         |      |
| controll_type |   控制模式   | uint8 | `0：板控` `1：遥控` `2：ROS控制` |      |

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





## ros系统下的AGV 小车控制协议

板载的A33处理器并不能运行ROS操作系统，需要通过外接mini PC来完成。MiniPC直接和板子上的STM32进行通讯，在ros模式下，我们需要禁用板载的寻磁传感器功能，将这个口用来做miniPC和STM32的通讯。

MiniPC -> Stm32

1.1 **控制指令** `cmd_type,pkg_length,seq,v_x,v_y,s_x,s_y`

| Param      |  Describe   |  Type   |   Range    |             Unit |
| ---------- | :---------: | :-----: | :--------: | ---------------: |
| prefix     |     包头      |  uint8  |    0x68    |                  |
| cmd_type   |    指令类型     |  uint8  |     0      |                  |
| pkg_length |     包长度     |  uint8  | `12bytes`  |                  |
| seq        |    指令序号     |  uint8  |            |                  |
| is_reset   |    是否抢占     |  uint8  |            | `0:false 1:true` |
| v_x        |    x轮速度     |  int16  | -500 ~ 500 |    `(1/10)r/min` |
| v_y        |    y轮速度     |  int16  | -500 ~ 500 |    `(1/10)r/min` |
| s_x        |    x轮里程     | uint16  |            |    `1 = (1/10)r` |
| s_y        |    y轮里程     | uint16  |            |    `1 = (1/10)r` |
| checksum   | 校验和（不含包头包尾） | uint8_t |   0~255    |                  |
| subfix     |     包尾巴     | uint8_t |    0xff    |                  |



Stm32-> MiniPC 

| Param      |  Describe   |     Type     |                          Range |                              Unit |
| ---------- | :---------: | :----------: | -----------------------------: | --------------------------------: |
| prefix     |     包头      |    uint8     |                           0x68 |                                   |
| cmd_type   |    指令类型     |    uint8     |                              0 |                                   |
| pkg_length |     包长度     |    uint8     |                      `39bytes` |                                   |
| seq        |    指令序号     |    uint8     |                                |                                   |
| v_x        |    x轴速度     |    int16     |                     -500 ~ 500 |                     `(1/10)r/min` |
| v_y        |    y轴速度     |    int16     |                     -500 ~ 500 |                     `(1/10)r/min` |
| axis_x     |    x轴坐标     |    int16     |                                |                     `1 = (1/10)r` |
| axis_y     |    y轴坐标     |    int16     |                                |                     `1 = (1/10)r` |
| ori        |     朝向      |    int16     |                                | `1 = 1/10°` `-1 = -1/10°` (逆时针为正) |
| rfid       |   rfid序列号   |    uint32    |                                |                                   |
| magn       |    磁条序列号    |    uint16    |              `0:missing 1:hit` |                                   |
| infra      |     红外      |    uint8     |               `0:false 1:true` |                                   |
| ut_f       |    前侧超声     | uint16 * `4` |                                |                              `mm` |
| ut_b       |    后侧超声     | uint16 * `4` |                                |                              `mm` |
| moto_state |    电机状态     |    uint8     | `0:stop 1:running` 左侧第0位 右侧第一位 |                                   |
| mile       |     总里程     |    uint16    |                                |                              `mm` |
| checksum   | 校验和（不含包头包尾） |   uint8_t    |                          0~255 |                                   |
| subfix     |     包尾巴     |   uint8_t    |                           0xff |                                   |









