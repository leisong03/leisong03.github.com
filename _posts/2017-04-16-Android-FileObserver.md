---
layout: post
title:  "Android 监听文件夹"
date:   2017-04-16 14:00:00 +0800
categories: Android 
tags: Android
author: JiuYang Chen
---



* content
{:toc}





本篇简单`Android`监听文件夹的方式`FileObserver`。

## `FileObserver`简介

`Android.os`包下的`FileObserver`类是一个用于监听文件访问、创建、修改、删除、移动等操作的监听器，基于`Linux`的`INotify`。

`FileObserver`是个抽象类，必须继承它才能使用。每个`FileObserver`对象监听一个单独的文件或者文件夹，如果监视的是一个文件夹，那么文件夹下所有的文件和级联子目录的改变都会触发监听的事件。

### `FileObserver`使用


```java

package com.example.chenjy.chenjy;

import android.os.FileObserver;

import utils.LogUtil;

/**
 * Created by chenjy on 2017/5/2.
 */

public class FileListener extends FileObserver {

    private EventCallback callback;

    public FileListener(String path) {
        super(path);
    }

    public void setEventCallback(EventCallback callback){
        this.callback = callback;
    }

    @Override
    public void onEvent(int event, String path) {
        LogUtil.i("FileListener", "path="+path);
        switch (event){
            // 文件被访问
            case FileObserver.ACCESS:
                LogUtil.i("FileListener", "ACCESS");
                break;
            // 文件被修改
            case FileObserver.MODIFY:
                LogUtil.i("FileListener", "MODIFY");
                break;
            // 文件属性被修改
            case FileObserver.ATTRIB:
                LogUtil.i("FileListener", "ATTRIB");
                break;
            // 可写文件被close
            case FileObserver.CLOSE_WRITE:
                LogUtil.i("FileListener", "CLOSE_WRITE");
                if(callback != null){
                    callback.onEvent(path);
                }
                break;
            // 不可写文件被close
            case FileObserver.CLOSE_NOWRITE:
                LogUtil.i("FileListener", "CLOSE_NOWRITE");
                break;
            // 文件被打开
            case FileObserver.OPEN:
                LogUtil.i("FileListener", "OPEN");
                break;
            // 文件被移走
            case FileObserver.MOVED_FROM:
                LogUtil.i("FileListener", "MOVED_FROM");
                break;
            // 文件被移进来
            case FileObserver.MOVED_TO:
                LogUtil.i("FileListener", "MOVED_TO");
                break;
            // 文件被删除
            case FileObserver.DELETE:
                LogUtil.i("FileListener", "DELETE");
                break;
            // 创建新文件
            case FileObserver.CREATE:
                LogUtil.i("FileListener", "CREATE");
                break;
            // 自删除
            case FileObserver.DELETE_SELF:
                LogUtil.i("FileListener", "DELETE_SELF");
                break;
            // 自移动
            case FileObserver.MOVE_SELF:
                LogUtil.i("FileListener", "MOVE_SELF");
                break;
        }
    }

    public interface EventCallback{
        void onEvent(String path);
    }
}


```

`FileObserver`调用

```java 

package com.example.chenjy.chenjy;

import android.app.Activity;
import android.os.Bundle;
import android.os.Environment;

public class MainActivity extends Activity {

    public static final String FILE_PATH = Environment.getExternalStorageDirectory().getAbsolutePath()+"/Pictures";


    private FileListener fileListener = new FileListener(FILE_PATH);

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        fileListener.startWatching();

    }

}


```

运行`app`。

![outPut](http://wx1.sinaimg.cn/mw690/c584f169ly1ff78r6f45nj20go08hweb.jpg)

然后通过`adb push` 向`android` 设备`push`图片。

![outPut](http://wx2.sinaimg.cn/mw690/c584f169ly1ff78r622a3j20df04gdfq.jpg)

![outPut](http://wx4.sinaimg.cn/mw690/c584f169ly1ff78r6qx2wj209i05gweh.jpg)

![outPut](http://wx3.sinaimg.cn/mw690/c584f169ly1ff78r71pw4j20gw089gll.jpg)

