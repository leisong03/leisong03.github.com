---
layout: post
title:  "Google-zxing 扫描二维码（2）"
date:   2016-12-18 19:00:00 +0800
categories: Java Android
tags: Java Android 
author: JiuYang Chen
---

* content
{:toc}

上一篇我们用`Google-chart`扫描了二维码，紧接着我们就用`Google-zxing`在我们的`Android`小手机上扫描读取二维码中的信息。





## `Google-zxing`

### `Google-zxing`简介

`Google-zxing`是`Google`的一个开源项目可以用来生成或是读取二维码。

这里我们用来用来扫一扫读取我们生成的二维码。

`秉持的不要重复制造轮子的原则`。



原博客地址：[原博客地址](http://blog.csdn.net/xiaanming/article/details/10163203)

源码地址：[http://download.csdn.net/detail/xiaanming/5990219](http://download.csdn.net/detail/xiaanming/5990219)


### `Google-zxing`存在的小`Bug`修复

1.1 `Google-zxing`原生的取景框太小

`mining.app.zxing.camera` 包里面的`CameraManager.java`,`getFramingRect`方法获取宽高的部分替换为

```java

 DisplayMetrics dm = context.getResources().getDisplayMetrics();
      int width = (int)(dm.widthPixels * 0.6);
      int height = (int)(width * 0.9);

      int leftOffset = (screenResolution.x - width) / 2;
      int topOffset = (screenResolution.y - height) / 2;

```

2.2 扫描的时候二维码变形拉伸（zxing默认针对横屏扫描）

`mining.app.zxing.camera` 包里面的`CameraConfigurationManager.java`,`findBestPreviewSizeValue`方法的`newDiff`取值 


```java

// 修改newDiff 取值
int newDiff=Math.abs(newY - screenResolution.x) + Math.abs(newX - screenResolution.y);



```

3.3 距离太近扫不出二维码

`mining.app.zxing.camera`

包里面的`CameraConfigurationManager.java`,`setDesiredCameraParameters`方法替换为

```java

Camera.Parameters parameters = camera.getParameters();
List<Camera.Size> supportedPreviewSizes = parameters.getSupportedPreviewSizes();
int position =0;
if(supportedPreviewSizes.size()>2){
  position=supportedPreviewSizes.size()/2+1;//supportedPreviewSizes.get();
}else {
  position=supportedPreviewSizes.size()/2;
}

int width = supportedPreviewSizes.get(position).width;
int height = supportedPreviewSizes.get(position).height;
Log.d(TAG, "Setting preview size: " + cameraResolution);
camera.setDisplayOrientation(90);
cameraResolution.x=width;
cameraResolution.y=height;
parameters.setPreviewSize(width,height);
setFlash(parameters);
setZoom(parameters);
camera.setParameters(parameters);


```

[修改后的Zxing源码](https://github.com/Chenjy1225/ChenjyDemo/tree/gh-pages/google.zxing)

*爱谷歌真是太好了!*

然后我们项目`run`起来，扫描前面创建的二维码


![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1fbgw0j63ogj20920fg0sm.jpg)


![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1fbgwdyj7g5j20920fcdga.jpg)

