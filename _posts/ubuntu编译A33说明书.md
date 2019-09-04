---
typora-copy-images-to: ../images
---

# VR屏幕分辨率调试

## 显示屏

直接在fex里面改时序

![image-20190102194213543](/Users/lei/Documents/workshop/githubleisong03/images/image-20190102194213543-6429333.png)

这是用LPS HPW HPS描述的时序关系，在全志平台上，要转换成用HSPW HFP，HBP描述的关系。

![image-20190102171102672](/Users/lei/Documents/workshop/githubleisong03/images/image-20190102171102672-6420262.png)

![image-20190102171317452](/Users/lei/Documents/workshop/githubleisong03/images/image-20190102171317452-6420397.png)

###3.97的timing图纸



![image-20190102164635935](/Users/lei/Documents/workshop/githubleisong03/images/image-20190102164635935-6418796.png)

根据上面两幅图的对照

hbp=HPS-LPS

vbp=VPS-FPS

## 触摸屏

### 增加linux源码

1. 为避免麻烦首先需要进行文件夹的替换：将固件/A33/gsl1680中的gslnew文件夹替换a33源码中的gslx680new文件夹：a33文件夹具体目录为：

   `/home/bean/Workspace/A33/lichee/linux-3.4/drivers/input/touchscreen`

###增加fex配置项

1. Sys_config.fex文件中:

```
sys_config.fex.smallscreen_gsl1680_new      为4.3寸屏gsl1680配置
sys_config.fex.bigscreen_gsl1680_new         为7寸屏gsl1680配置
```

1. 同时需要设置  android/ device/softwinner/astar-y3/init.sun8i.rc中加入insmod gslX680new.ko：

![ image-20190102080431022](/Users/lei/Documents/workshop/githubleisong03/images/image-20190102080431022-6387471.png)

​                                                  ![image-20190102080441128](/Users/lei/Documents/workshop/githubleisong03/images/image-20190102080441128-6387481.png)

**需注意的是**：

1. 红框都代表一个固件数组，对应于一种触摸屏的固件参数。

1. 触摸屏都有一个固件参数，有时新的硬件可能不能匹配原有的固件参数，此时最好的办法就是找售后，联系售后的技术，直接让他们去调参数，避免浪费不必要的时间。

## WiFi

sys_config.fex：[wifi_para]

驱动目录：/home/bean/Workspace/A33/lichee/linux-3.4/drivers/net/wireless

详细配置请见A33配置文档：

**必须注意的一个问题就是：**在修改**BoardConfig.mk**文件后，需**make clean**重新make才能完全生效（即如果想修改使用的Wifi/Bluetooth芯片型号需进行修改）。

因为AP6212版本较新，关于6212的开发请见博客：

<http://blog.csdn.net/zjngogo/article/details/46739683> ，

需注意相应的固件文件在 

​            /home/bean/Workspace/A33/android/hardware/broadcom/wlan/firmware

## STM32

目的：在device目录下生成一个虚拟设备（stm32）通过操作stm32完成在安卓端同真正stm32进行通信。

​            代码放置位置：

### 添加linux驱动

/home/bean/Workspace/A33/lichee/linux-3.4/drivers/i2c/busses

stm32.c

1修改Makefile

![image-20190102080756154](/Users/lei/Documents/workshop/githubleisong03/images/image-20190102080756154-6387676.png)

将其直接编译进内核。

### 修改运行时文件

2 改变运行时文件

android/ device/softwinner/astar-y3/

 在init.sun8i.rc文件中为stm32  mkdir并chmod

3 在ueventd.sun8i.rc 里面 

/dev/stm32 0777 root root

## 屏幕方向

**屏幕翻转问题**：调节屏幕方向(**不到万不得已不要修改**，因为修改就需要将安卓源码重新make clean一遍，非常浪费时间，现在已有版本（0 90 180）应该已够用)

android/device/softwinner/polaris-common/polaris-common.mk

ro.sf.rotation

## 不关机，不锁屏



## 默认开启Debug

home/bean/Workspace/A33/android/out/target/product/astar-y3/system/build.prop

![image-20190102080918390](/Users/lei/Documents/workshop/githubleisong03/images/image-20190102080918390-6387758.png)

修改persist.sys.usb.config=mtp,adb，修改ro.adb.secure=0。



在这里修改是否暗屏幕

android/frameworks/base/packages/SettingsProvider/res/values

timeout若是-1，则永不锁屏

## 开机LOGO

用DragonFace直接替换即可

## 安卓apk：Launcher设置

1. 将可自启动(system 级)的apk放入目录：system/app/

A33目录为：android/out/target/product/astar-y3/system/app/

然后进行编译，烧写

2. 开机后，打开开发者选项--usb调试，进入adb

3. 使system有读写权限：   mount -o remount,rw /system

然后将系统自带的luncher删掉， rm -f /system/priv-app/Launcher2.apk

4. 再次开机便可直接进入自启动app