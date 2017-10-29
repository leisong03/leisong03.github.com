---
layout: post
title:  "Android tips"
date:   2016-06-01 20:00:00 +0800
categories: Android
tags: Android
author: JiuYang Chen
---

* content
{:toc}




## `Android`

###  `setHeight` no use

当设置的高度比原来默认的高度要小时,调整setHeight是不生效的。

```java

editText=(EditText)findViewById(R.id.myEditText);

// editText.setHeight(10); //不生效

editText.getLayoutParams().height = 100; 

```

### `Installation error:INSTALL_PARSE_FAILED_MANIFEST_MALFORMED`

1.1 包名大写了

2.2 缺少`AndroidManifest.xml`文件


### `Error:Error converting bytecode to dex`

1.1 包重复

2.2 `build`本身问题, 只需要`clean and rebuild` 一下

### 自动售票

```java

public class AutoSaleTicket  implements Runnable {

    private int ticket = 20;

    public void run() {

        while (true) {// 循环是指线程不停的去卖票
            // 当操作的是共享数据时,用同步代码块进行包围起来,这样在执行时,只能有一个线程执行同步代码块里面的内容
            synchronized (this) {
                if (ticket > 0) {

                    // 不要在同步代码块里面sleep,作用只是自已不执行,也不让线程执行
                    System.out.println(Thread.currentThread().getName()
                            + " 卖出 第 " + (20 - ticket + 1) + " 张票");
                    ticket--;

                } else {
                    break;
                }
            }
            // 所以把sleep放到同步代码块的外面,这样卖完一张票就休息一会,让其他线程再卖,这样所有的线程都可以卖票
            try {
                Thread.sleep(200);
            } catch (Exception ex) {
            }
        }
    }
    public static void main(String[] args){
        AutoSaleTicket ticket = new AutoSaleTicket();
        Thread t1 = new Thread(ticket, "1");
        Thread t2 = new Thread(ticket, "2");
        Thread t3 = new Thread(ticket, "3");
        Thread t4 = new Thread(ticket, "4");
        t1.start();
        t2.start();
        t3.start();
        t4.start();
    }

    }
	
```	

### `EditText`光标颜色
	
`EditText` 有一个属性 `android:textCursorDrawable` 用来控制光标的颜色。`android:textCursorDrawable="@null"`,`"@null"`作用是让光标颜色和`text color`一样
	
### 发现了以元素`'d:skin'`开头的无效内容	

把有问题的`devices.xml`删除,在`Android SDK` 里面的`tool\lib` 下找到`devices.xml`拷贝到那个文件夹。
	
### `finished with non-zero exit value 2`

重复的`jar`包,删除引用的包，同时删除`module`的`build.gradle`文件的引用。

### `border`

```java

<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <solid android:color="#00000000"/>
    <stroke android:width="1dp" android:color="#000000"/>
    <padding android:left="1dp" android:top="1dp" android:right="1dp" android:bottom="1dp" />
</shape>

```



















	