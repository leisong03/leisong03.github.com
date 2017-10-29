---
layout: post
title:  "Android 串口通信"
date:   2017-04-02 12:00:00 +0800
categories: Android 
tags: Android
author: JiuYang Chen
---



* content
{:toc}





本篇简单介绍`Android`的串口通信(基于chrome的一个开源串口包 `android_serialport_api`)。

## `Android`串口通信

1.1 Step1 导入 `android_serialport_api`

```xml

android_serialport_api/SerialPort

```

2.2 Step2 在`jniLibs/aemeabi` 文件夹下导入`android_serialport_api`的动态链接库 `libserial_port.so`

> 使用了`JNI`技术，使得`Java`可以调用c语言写成的库

3.3 Step3 use it

```java

package com.example.chenjy.serialport;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;

import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

import android_serialport_api.SerialPort;


public class MainBlogActivity extends AppCompatActivity {

	// 串口实例
    private SerialPort mSerialPort;
	// 输出流
    private OutputStream mOutputStream;
	// 输入流
    private InputStream mInputStream;
	// 读取串口线程
    private ReadThread mReadThread;
	// 串口 
    private String sPort = "/dev/ttyUSB0";
	// 波特率
    private int iBaudRate = 115200;
    private String receiveString;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setContentView(R.layout.activity_main);
        
        try {
            mSerialPort = new SerialPort(new File(sPort), iBaudRate, 0);	
            mOutputStream = mSerialPort.getOutputStream();
            mInputStream = mSerialPort.getInputStream();
            mReadThread = new ReadThread();
			// 启动读取串口线程
            mReadThread.start();
        } catch (IOException e) {
            e.printStackTrace();
        }
        

    }

    /**
     * 读串口数据
     */
    private class ReadThread extends Thread {
        @Override
        public void run() {
            super.run();
			// 如果线程没有被中断,不断从串口读取数据
            while (!isInterrupted()) {

                if (mInputStream != null) {
                    byte[] buffer = new byte[512];
                    int size = 0;
                    try {
					    // 如果没有读到数据,则阻塞 直到返回数据
                        size = mInputStream.read(buffer);
                    } catch (IOException e) {
                        e.printStackTrace();
                    }

                    if (size > 0) {
                        byte[] buffer2 = new byte[size];
                        for (int i = 0; i < size; i++) {
                            buffer2[i] = buffer[i];
                        }
                        receiveString = SerialDataUtils.ByteArrToHex(buffer2).trim();

                        System.out.println("---- receive ---- :"+receiveString);

                    }
                    try {
                        //延时50ms
                        Thread.sleep(50);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
            return;
        }
    }

    /**
     * 发串口数据
     */
    public void sendGetTemper() {
        try {
            
            // 测试用的3d打印机控制板 Gcode指令			
            String command = "M105\n";

            mOutputStream.write(command.getBytes());

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    protected void onDestroy() {
        //释放串口
        mSerialPort.close();
        super.onDestroy();
    }


}

```

Tips: 网络通信程序是基于阻塞式API的 - 当程序输入、输出操作以后，在这些操作返回之前会一直阻塞该进程。


![outPut](http://wx1.sinaimg.cn/mw690/c584f169ly1fery9i44b1j20pw01zwek.jpg)


[源代码](https://github.com/Chenjy1225/ChenjyDemo/tree/gh-pages/serialport)












