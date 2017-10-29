---
layout: post
title:  "Android App 切换语言"
date:   2017-05-08 20:00:00 +0800
categories: Android  
tags: Android
author: JiuYang Chen
---



* content
{:toc}





本篇简单介绍将在`Android App`中进行语言的切换。

## 切换语言

首先需要在res 中创建个若干个不同的value文件夹（例如:`values`、`values-en`、`value-ja`）。然后将不同的`String.xml`文件。

* 这里为 中、英、日三语切换。（value文件夹命名可以参考 [http://www.cnblogs.com/loulijun/p/3164746.html](http://www.cnblogs.com/loulijun/p/3164746.html)）

![outPut](http://wx1.sinaimg.cn/large/c584f169ly1fi4f9gv7ruj205p05udfq.jpg)

### 核心代码

```java

	public void switchLanguage(Locale locale) {
        Resources resources = getBaseContext().getResources();
        Locale.setDefault(locale);
        Configuration config = resources.getConfiguration();
        DisplayMetrics dm = resources.getDisplayMetrics();
        config.locale = locale;
        resources.updateConfiguration(config, dm);
    }

```

![outPut](http://wx3.sinaimg.cn/large/c584f169ly1fi4ei6vz48g20f40qoqvi.gif)


* [完整源代码](https://github.com/Chenjy1225/ChenjyDemo/tree/gh-pages/Android/switchLanguage)





















