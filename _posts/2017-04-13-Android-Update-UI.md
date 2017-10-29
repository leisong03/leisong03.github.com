---
layout: post
title:  "Android 更新UI"
date:   2017-04-10 14:00:00 +0800
categories: Android 
tags: Android
author: JiuYang Chen
---



* content
{:toc}





本篇简单`Android`里面更新`UI`的四种方法。

## 更新`UI`简介

`Android` 更新`UI`主要是主线程进行更新,即`UI`线程更新。如果在主线程外更新则会报错。

> `android.view.ViewRoot$CalledFromWrongThreadException: Only the original thread that created a view hierarchy can touch its views.`

* 只有创建这个视图层次的线程才能修改它的视图

### `Handler`消息传递

```java

package com.example.chenjy.chenjy;

import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.os.Message;
import android.widget.TextView;


public class MainActivity extends Activity {

    private TextView tv_token;

    Handler handler = new Handler()
    {
        public void handleMessage(Message msg) {
            if(msg.what==0x12)
            {
                tv_token.setText("update UI");
            }
        };
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initView();

        new MyThread().start();
    }

    public void initView(){

        tv_token = (TextView)findViewById(R.id.tv_token);

    }

    class MyThread extends Thread
    {
        @Override
        public void run() {
            //延迟两秒更新
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
            handler.sendEmptyMessage(0x12);
        }
    }
}

```


### 使用`runOnUiThread`


```java

package com.example.chenjy.chenjy;

import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;

public class MainActivity extends Activity {

    private TextView tv_token;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initView();

        new MyThread().start();
    }

    public void initView(){

        tv_token = (TextView)findViewById(R.id.tv_token);

    }

    class MyThread extends Thread
    {
        @Override
        public void run() {
            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    // TODO Auto-generated method stub
                    try {
                        //延迟两秒更新
                        Thread.sleep(2000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    tv_token.setText("update UI");
                }
            });
        }
    }
}


```

### `Handler` 的`post(Runnable runnable)`

```java

package com.example.chenjy.chenjy;

import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.widget.TextView;

public class MainActivity extends Activity {

    private TextView tv_token;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        tv_token = (TextView) findViewById(R.id.tv_token);

        Handler handler = new Handler();
        handler.post(new Runnable() {
            @Override
            public void run() {
                try {
                    //延迟两秒更新
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                tv_token.setText("update UI");
            }

        });

    }

}


```

### `AsyncTask`异步任务

```java

package com.example.chenjy.chenjy;

import android.app.Activity;
import android.os.AsyncTask;
import android.os.Bundle;
import android.widget.TextView;

public class MainActivity extends Activity {

    private TextView tv_token;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        tv_token = (TextView) findViewById(R.id.tv_token);

        new UpdateUI().execute();
    }

    class UpdateUI extends AsyncTask<String, String, String>
    {

        @Override
        protected String doInBackground(String... params) {
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                // TODO Auto-generated catch block
                e.printStackTrace();
            }
            return null;
        }
        @Override
        protected void onPostExecute(String result) {
            // TODO Auto-generated method stub
            tv_token.setText("update UI");
        }

    }
}


```


![outPut](http://wx2.sinaimg.cn/mw690/c584f169ly1fext689puij20ai08lt8r.jpg)













