---
layout: post
title:  "Google-chart 生成二维码（1）"
date:   2016-12-30 19:00:00 +0800
categories: Java
tags: Java 
author: JiuYang Chen
---

* content
{:toc}

本篇介绍利用`Google-chart`生成二维码。





## `Google-chart`

### `Google-chart`简介

`Google chart API`是Google提供的一个工具，方便人们轻松的将一些图标嵌入到浏览器中。

你只需要在浏览器中键入地址即可不需要其他多余软件。

### `Google-chart`格式

`https://chart.googleapis.com/chart?cht=qr&chs=200x200&choe=UTF-8&chld=L|4&chl=chenjy1225`

结构：

1.1 `cht`:图表种类，这里是`qr`表示为二维码  必填

2.2 `chs`:图表尺寸，这里的大小为`200x200`  `中间的乘号是小写的x`  必填

3.3 `chl`:图表数据，需要编码的数据  必填

4.4 `choe`:图表编码格式，这里的格式为`UTF-8` 可供选择的值`UTF-8`、`SHIFT_JIS`、`ISO-8859-1`  可选

5.5 `chd`:图表的纠错能力，QR码支持4个层次的纠错。 L[default]:允许7%、M:允许15% 、Q:允许25% 、H:允许30% 

### `Google-chart`使用

访问上述地址，你就可以看到如下二维码：

![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1fbeqatnyvnj206705s3yd.jpg)

`Google-chart`还有很多很强大的功能。


[see more in Google](https://developers.google.com/chart/)