---
layout: post
title:  "rayland-DLP主板使用说明"
date:   2018-01-09 07:00:00 +0800
categories: DLP 
tags: DLP 3D打印 
author: Song Lei

typora-copy-images-to: ../images
---

* content
{:toc}

Rayland-DLP是锐蓝科技自主研发的新产品，通过HDMI输出视频到LCD或者DLP显示端，完成树脂材料的光固化，是目前低成本高精度的打印方案。



Rayland-DLP无法单独使用，需要与电机驱动板协同工作。目前支持的电机驱动板包括Rayland-dual，Rayland-solo，Rayland-pro。

## 软件系统

Rayland-DLP是基于著名的开源操作系统Armbian开发的。工作原理很简单，从usb口接收来自电机驱动版的图像，将图像用pygame-python进行渲染之后从hdmi口进行输出。以下是我们依赖的这两个项目的链接。

[pygame 主页](http://www.pygame.org/news)

[armbian 主页](https://www.armbian.com)

### 源码和镜像下载

基于GPL协议，我们也开放了我们的img文件和程序源码，请大家到百度网盘下载。

[img和源码下载链接](https://pan.baidu.com/s/1c2TXdUK)

## 电路连接

目前的Rayland-DLP一共有两个规格，使用方法完全一样，唯一的区别是内存大小导致的支持最大分辨率的差异。

![99F8292C-D5C8-467D-9F9A-7566435DCB46]({{site.baseurl}}/images/99F8292C-D5C8-467D-9F9A-7566435DCB46.png)

Rayland-DLP主板上共有两个客户用接口，分别为：

1. HDMI接口

2. usb接口（接dual/solo/pro板）

这两个接口如下图所示

 ![dlp_interface]({{site.baseurl}}/images/dlp_interface.jpg)

Rayland-DLP无法单独使用，需要与电机驱动板协同工作。目前支持的电机驱动板包括Rayland-dual，Rayland-solo，Rayland-pro。在本章实例中，使用的电机驱动板为Rayland-dual。

本系统由Rayland-DLP+曝光屏，Rayland-dual+控制屏，以及电机系统组成。电路链接方法如下图所示

![dlp_in_circuit]({{site.baseurl}}/images/dlp_in_circuit.jpg)



*注意*如果通过USB口单独给Rayland-DLP供电时，请使用供电电流超过1A的电源，普通的USB2.0电源可能导致Rayland-DLP的主CPU无法启动

## 烧录镜像

镜像是IMG格式的文件，在linux/macOS上用dd进行读写，在windows平台上用win32diskimager烧写

### 在Rayland-DLP上安装镜像
#### 用win32DiskImager烧录镜像

win32diskImager是windows平台上的镜像读取和烧录软件。使用是需要指定三个选项

1. 镜像文件，在Image File选项框里面
2. TF卡设备在Device下来菜单里面
3. 操作动作，是read（从tf拷贝出.img）文件还是 write（将.img文件烧入TF卡）

![win32disk]({{site.baseurl}}/images/win32disk.jpg)

注意自检镜像和打印镜像都使用这个工具进行烧录

烧录时的进度如下图所示。

![1E3EF4C2-BE6E-48DA-9899-400BBE58E3E6]({{site.baseurl}}/images/1E3EF4C2-BE6E-48DA-9899-400BBE58E3E6.png)

#### 烧录自检镜像

自检镜像是用来获取曝光屏的基本参数的，基本参数包括hdmi_timing等参数。

使用步骤如下：

1. 烧录selftest.img文件,这个tf卡以下简称亮屏卡

   ![selftest_tf]({{site.baseurl}}/images/selftest_tf.png)

2. 把亮屏卡插入tf卡槽

   ![to_tf_slot]({{site.baseurl}}/images/to_tf_slot.png)

3. 把hdmi插入hdmi口 HDMI线另一端 接屏幕

4. 供电插入中间的microUSB插口、观察屏幕是否有显示。

   ![cable_on]({{site.baseurl}}/images/cable_on.png)

5. 屏幕出现这样字样、说明调试成功

   ![FD6337D6-6052-450D-AF8C-FA4009E95941]({{site.baseurl}}/images/FD6337D6-6052-450D-AF8C-FA4009E95941.png)

如需定制分辨率，请将这个屏幕截取发送到客服QQ上，打了红框的这几行是关键参数，请务必拍摄清楚。

#### 烧录打印镜像

用上面的方法将print.img烧录到tf卡中，以下简称打印卡。打印镜像是用来进行DLP打印的，安装上面的电路进行链接时，在开机状态下曝光屏将出现如下的启动画面。

![A210B3C5-E56A-40C2-AA9A-7AD6A1273B20]({{site.baseurl}}/images/A210B3C5-E56A-40C2-AA9A-7AD6A1273B20.png)


### 在Rayland-solo/dual/pro上安装APK
Rayland-solo/dual/pro上需要专门的APK来处理电机运动和投影图像。打印时UI屏的输入如下图所示。

![dlp_on_screen]({{site.baseurl}}/images/dlp_on_screen.png)

#### 在APP中调整曝光屏分辨率

在这个页面里可以看到曝光屏分辨率和屏幕接口种类

![E1DF85AB-F16D-4EF4-9262-AFF589768F0F]({{site.baseurl}}/images/E1DF85AB-F16D-4EF4-9262-AFF589768F0F.png)

这个页面是当前支持的所有分辨率种类，通过点击可以选择

![7AB26883-A95F-48AB-9E0E-CE3ADF5EB59C]({{site.baseurl}}/images/7AB26883-A95F-48AB-9E0E-CE3ADF5EB59C.png)



在选择了分辨率之后，系统会自动让你选择接口类型，是HDMI接口屏还是DVI接口屏。

![AF52A490-5969-4ACE-80AA-5CAED13D3FF1]({{site.baseurl}}/images/AF52A490-5969-4ACE-80AA-5CAED13D3FF1.png)



选择曝光屏接口类型后，曝光屏系统会自动重启。

### 故障排除

#### 无法获取分辨率

app 里无法显示分辨率，一直显示“正在设置”

![E9FEFAB4-4F63-4A92-BC81-6B23ECA56CA7]({{site.baseurl}}/images/E9FEFAB4-4F63-4A92-BC81-6B23ECA56CA7.png)

将烧录好的打印卡插入H3板将打印卡插入TF卡槽，然后用mircroUSB连接到电脑上。链接方法如下图所示

![contoPC]({{site.baseurl}}/images/contoPC.jpeg)

电脑上会多出一个有线网卡，请将这个网卡的IP地址如下设置为固定值

![IPof8152]({{site.baseurl}}/images/IPof8152.png)

如果您使用的是Windows操作系统，那么需要安装RTL8152网卡的驱动，在不安装驱动的情况下你可以在设备管理器里面看到如下的图

![4CE930FB-35C5-445B-A522-9BB9AB3DC293]({{site.baseurl}}/images/4CE930FB-35C5-445B-A522-9BB9AB3DC293.png)

之后用ping命令查看192.168.199.3 这一IP是否正常链接，如果不能链接则打印卡没有烧录成功，请重新烧录。

```sh
$ ping 192.168.199.3
PING 192.168.199.3 (192.168.199.3): 56 data bytes
64 bytes from 192.168.199.3: icmp_seq=0 ttl=64 time=0.633 ms
64 bytes from 192.168.199.3: icmp_seq=1 ttl=64 time=0.409 ms
64 bytes from 192.168.199.3: icmp_seq=2 ttl=64 time=0.486 ms
64 bytes from 192.168.199.3: icmp_seq=3 ttl=64 time=0.480 ms
64 bytes from 192.168.199.3: icmp_seq=4 ttl=64 time=0.482 ms
64 bytes from 192.168.199.3: icmp_seq=5 ttl=64 time=0.481 ms
64 bytes from 192.168.199.3: icmp_seq=6 ttl=64 time=0.516 ms
64 bytes from 192.168.199.3: icmp_seq=7 ttl=64 time=0.437 ms
```

当链接修复正常后应该显示如下的信息

![ABEBC8FC-ED24-410B-9D5E-523627262EA7]({{site.baseurl}}/images/ABEBC8FC-ED24-410B-9D5E-523627262EA7.png)

#### 色域不正常

启动后色域显示不正常，整个屏幕发红，但是可以显示出打印图像。请参考“在APP中调整曝光屏分辨率”这一章节，切换屏幕接口类型。

![0B726BBC-1E25-4DE4-9A0B-8EC84F5B5CC2]({{site.baseurl}}/images/0B726BBC-1E25-4DE4-9A0B-8EC84F5B5CC2.png) 

## 客户打印样品

艾菲尔铁塔

![838AB13E-5CC9-42B6-B58E-90DF634E9CB1]({{site.baseurl}}/images/838AB13E-5CC9-42B6-B58E-90DF634E9CB1.png)

戒指

![6D4F6B36-2CB5-45FE-A514-346ED341A81E]({{site.baseurl}}/images/6D4F6B36-2CB5-45FE-A514-346ED341A81E.png)

鞋底

![8AFE6623-5B7B-45B2-9CEC-1B897D1FDA62]({{site.baseurl}}/images/8AFE6623-5B7B-45B2-9CEC-1B897D1FDA62.png)

一堆鞋底

![E6F78316-65D9-4CA5-AEB6-914DF7DC394A]({{site.baseurl}}/images/E6F78316-65D9-4CA5-AEB6-914DF7DC394A.png)