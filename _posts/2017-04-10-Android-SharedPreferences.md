---
layout: post
title:  "Android SharedPreferences"
date:   2017-04-10 14:00:00 +0800
categories: Android 
tags: Android
author: JiuYang Chen
---



* content
{:toc}





本篇简单SharedPreferences的应用。

## `SharedPreferences`简介

`SharedPreferences`是用来存储一些简单的配置文件的机制,`SharedPreferences`使用`Map`数据结构来存储数据(`key-value`)。采用`XML`格式将数据存储到设备中。

```xml

<?xml version='1.0' encoding='utf-8' standalone='yes' ?>

<map>

   <string name="name">chenjy</string>

   <int name="age" value="18" />

</map>


```

## `SharedPreferences`使用

获取`SharedPreferences`有两种方式:

1.1 调用`Context`对象的`getSharedPreferences()`方法,可以被用一个应用程序下的其他组件共享。

2.2 调用`Activity`对象的`getPreferences()`方法,该对象只能在`Activity`中使用。

`SharedPreferences`的四种操作模式：

`Context.MODE_PRIVATE`:默认的操作模式,代表改文件为私有数据,只能被应用本身访问。该模式下,写入的文件会覆盖原文件。

`Context.MODE_APPEND`:改模式会检查文件是否存在,如果存在就往文件追加内容否则就创建新文件。

`Context.MODE_WORLD_READABLE`：表示当前文件可以被其他应用读取。

`Context.MODE_WORLD_WRITEABLE`：表示当前文件可以被其他应用写入。

## `SharedPreferences`源码

`SharedPreferences`通过`SharedPreferences.Editor`来存储数据

* `SharedPreferences.Editor.putInt`、`SharedPreferences.Editor.putString`存`key` `value`值。

* `SharedPreferences.Editor.remove` 清除指定`key`的字段。

* `SharedPreferences.Editor.clear` 清空所有数据。

```java

import android.content.Context;
import android.content.SharedPreferences;
import android.preference.PreferenceManager;

public class EasySharePreference {

    private static SharedPreferences sharedPreferences = null;
    private static SharedPreferences.Editor sp_editor = null;

    public static void init(Context c) {
        if (sharedPreferences == null) {
            sharedPreferences = PreferenceManager.getDefaultSharedPreferences(c);
        }
        if (sp_editor == null) {
            sp_editor = sharedPreferences.edit();
        }
    }

    public static SharedPreferences getPrefInstance() {
        return sharedPreferences;
    }

    public static SharedPreferences.Editor getEditorInstance() {
        return sp_editor;
    }

	// 获取名为token的`SharedPreferences`
    public static String getToken() {
        String token = sharedPreferences.getString("token", "").trim().replace("\n", "");
        return token.replace("\"", "");
    }

	// 存储名为token的`SharedPreferences`
    public static void saveToken(String token) {
        sp_editor.putString("token", token.trim().replace("\n", "")).commit();
    }

}


```

MainActivity:

```java

package com.example.chenjy.chenjy;

import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;


import utils.EasySharePreference;

public class MainActivity extends Activity {

    private TextView tv_token;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        EasySharePreference.init(getApplicationContext());

        initView();

    }

    private void initView(){

        tv_token = (TextView)findViewById(R.id.tv_token);

        EasySharePreference.saveToken("chenjy");

        tv_token.setText(EasySharePreference.getToken());

    }

}

```

![outPut](http://wx2.sinaimg.cn/mw690/c584f169ly1fexqek5atrj20af08yq2y.jpg)













