---
layout: post
title:  "Android 局域网扫描"
date:   2017-03-29 16:00:00 +0800
categories: Android 
tags: Android UDP
author: JiuYang Chen
---



* content
{:toc}

本篇简单介绍通过`UDP`广播扫描局域网设备`IP`,并且通过`ZMQ`进行通信。





## `UDP`连接

主要流程：

1.1  Step1:主机发送广播信息,并指定接收端的端口`(9000)` 广播地址`(255.255.255.255)`
  
2.2  Step2:主机将数据报以固定报头并且和用户数据一起打包封装在`DatagramPacket`,为了防丢失一共发三次，每次发送以后监听一段时间

3.3  Step3:接收端监听端口`(9000)`,收到数据报以后解析数据如果和和约定的数据格式一样,则通过数据报获取主机的`ip`和端口

4.4  Step4:接收端设备通过主机的`ip`和端口给主机发送响应信息

5.5  Step5:主机收到响应信息,主机返回确认响应信息(防止信息丢失)

6.6  Step6:至少主机和接收端都有了对方的`IP`地址


### 主机

```java
package com.zjun.searcher;

import android.annotation.TargetApi;
import android.os.Build;

import java.io.IOException;
import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;
import java.net.SocketException;
import java.net.SocketTimeoutException;
import java.net.UnknownHostException;
import java.nio.charset.Charset;
import java.util.HashSet;
import java.util.Set;

public abstract class SearcherHost<T extends SearcherHost.DeviceBean> extends Thread {

    private int mUserDataMaxLen;
    private Class<T> mDeviceClazz;

    // UDP socket连接
    private DatagramSocket mHostSocket;
    // UDP socket对应的数据报
    private DatagramPacket mSendPack;

    // 搜索到的设备列表
    private Set<T> mDeviceSet;

    private byte mPackType;

    private String mDeviceIP;

    public SearcherHost() {
        this(0, DeviceBean.class);
    }

    public SearcherHost(int userDataMaxLen, Class clazz) {
        mDeviceClazz = clazz;
        mUserDataMaxLen = userDataMaxLen;
        mDeviceSet = new HashSet<>();

        try {
		
		    /***************** Step1 *****************/
		    
            // 实例udp socket ip默认为本机ip,端口为所有可用端口中随机端口
            mHostSocket = new DatagramSocket();
            // 设置接收超时时间
            mHostSocket.setSoTimeout(SearcherConst.RECEIVE_TIME_OUT);

            byte[] sendData = new byte[1024];
            InetAddress broadIP = InetAddress.getByName("255.255.255.255");

            // udp socket 数据报 端口号为 9000
            mSendPack = new DatagramPacket(sendData, sendData.length, broadIP, SearcherConst.DEVICE_FIND_PORT);
        } catch (SocketException | UnknownHostException e) {
            printLog(e.toString());
            if (mHostSocket != null) {
                mHostSocket.close();
            }
        }
    }

    /**
     * 开始搜索
     * @return true-正常启动，false-已经start()启动过，无法再启动。若要启动需重新new
     */
    public boolean search() {
        if (this.getState() != State.NEW) {
            return false;
        }

        this.start();
        return true;
    }

    @Override
    public void run() {
        if (mHostSocket == null || mHostSocket.isClosed() || mSendPack == null) {
            return;
        }

        try {
            onSearchStart();

		    /***************** Step2 *****************/
			
            // 开始搜索
            for (int i = 0; i < 3; i++) {


                // 打包搜索数据报,数据报类型为 `搜索请求`
                mPackType = SearcherConst.PACKET_TYPE_FIND_DEVICE_REQ_10;
                mSendPack.setData(packData(i + 1));
                // 发送搜索广播
                mHostSocket.send(mSendPack);

                // 监听来信
                byte[] receData = new byte[2 + mUserDataMaxLen];
                DatagramPacket recePack = new DatagramPacket(receData, receData.length);
                try {
				
					/***************** Step5 *****************/
                    // 最多接收250个，或超时跳出循环
                    int rspCount = SearcherConst.RESPONSE_DEVICE_MAX;
                    while (rspCount-- > 0) {
                        recePack.setData(receData);
                        mHostSocket.receive(recePack);
                        if (recePack.getLength() > 0) {
                            mDeviceIP = recePack.getAddress().getHostAddress();
                            if (parsePack(recePack)) {
                                printLog("a response from：" + mDeviceIP);
                                // 发送一对一的确认信息。使用接收报，因为接收报中有对方的实际IP，发送报时广播IP

                                // 打包搜索确认数据报,数据报类型为 `搜索确认`
                                mPackType = SearcherConst.PACKET_TYPE_FIND_DEVICE_CHK_12;
                                recePack.setData(packData(rspCount));

                                mHostSocket.send(recePack);
                            }
                        }
                    }
                } catch (SocketTimeoutException e) {
                }
                printLog(String.format("the %dth search finished", i));

            }
            // finish
            onSearchFinish(mDeviceSet);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (mHostSocket != null) {
                mHostSocket.close();
            }
        }

    }

    /**
     * 搜索开始时执行
     */
    public abstract void onSearchStart();

    /**
     * 打包搜索时的用户数据
     * packed the userData by caller when searching
     */
    protected byte[] packUserData_Search() {
        return new byte[0];
    }

    /**
     * 打包确认时的用户数据
     * packed userData by caller when checking，and override the method when pack
     */
    protected byte[] packUserData_Check() {
        return new byte[0];
    }


    /**
     * 解析数据
     * parse if have userData
     * @param type 数据类型
     * @param device 设备
     * @param userData 数据
     *
     * @return return the result of parse, true if parse success, else false
     */
    public boolean parseUserData(byte type, T device, byte[] userData) {
        return true;
    }

    /**
     * 搜索结束后执行
     * @param deviceSet 搜索到的设备集合
     */
    public abstract void onSearchFinish(Set deviceSet);

    /**
     * 打印日志
     * 由调用者打印，SE和Android不同
     */
    public abstract void printLog(String log);


    /**
     * 解析报文
     * 协议：$ + packType(1) + userData(n)
     *
     *  @param pack 数据报
     */
    @TargetApi(Build.VERSION_CODES.KITKAT)
    private boolean parsePack(DatagramPacket pack) {
        if (pack == null || pack.getAddress() == null) {
            return false;
        }

        String ip = pack.getAddress().getHostAddress();
        int port = pack.getPort();
        for (T d : mDeviceSet) {
            if (d.getIp().equals(ip)) {
                return false;
            }
        }

        // 解析头部数据
        byte[] data = pack.getData();
        int dataLen = pack.getLength();

        if (dataLen < 2 || data[0] != '$' || data[1] != SearcherConst.PACKET_TYPE_FIND_DEVICE_RSP_11) {
            return false;
        }

        T device = null;

        try {
            Constructor constructor = mDeviceClazz.getDeclaredConstructor(String.class, int.class);
            device = (T) constructor.newInstance(ip, port);
        } catch (NoSuchMethodException | InvocationTargetException | InstantiationException | IllegalAccessException e) {
            e.printStackTrace();
        }

        if (device == null) {
            return false;
        }

        if (mUserDataMaxLen == 0 && dataLen == 2) {
            return mDeviceSet.add(device);
        }

        // 解析用户数据
        int userDataLen = dataLen - 2;
        byte[] userData = new byte[userDataLen];
        System.arraycopy(data, 2, userData, 0, userDataLen);

        return parseUserData(data[1], device, userData) && mDeviceSet.add(device);
    }

    /**
     * 打包搜索报文
     * 协议：$ + packType(1) + sendSeq(4) [+ deviceIpLen(1) + deviceIp(n<=15)] [+ userData]
     *  packType - 报文类型
     *  sendSeq - 发送序列
     *  deviceIpLen - 设备IP长度
     *  deviceIp - 设备IP，仅在确认时携带
     *  userData - 用户数据
     *
     *  @param seq 发送序列号
     */
    private byte[] packData(int seq) {
        byte[] data = new byte[1024];
        int offset = 0;

        // 打包数据头部
        data[offset++] = '$';

        data[offset++] = mPackType;

        seq = seq == 3 ? 1 : ++seq; // can't use findSeq++
        data[offset++] = (byte) seq;
        data[offset++] = (byte) (seq >> 8 );
        data[offset++] = (byte) (seq >> 16);
        data[offset++] = (byte) (seq >> 24);

        switch (mPackType) {
            // packType为 `搜索请求`
            case SearcherConst.PACKET_TYPE_FIND_DEVICE_REQ_10: {

                // 打包userData
                byte[] userData = packUserData_Search();
                if (data.length < offset + userData.length) {
                    byte[] tmp = new byte[offset + userData.length];
                    System.arraycopy(data, 0, tmp, 0, offset);
                    data = tmp;
                }
                System.arraycopy(userData, 0, data, offset, userData.length);
                offset += userData.length;
                break;
            }
            // packType为 `搜索确认`
            case SearcherConst.PACKET_TYPE_FIND_DEVICE_CHK_12: {
                // deviceIp
                byte[] ips = mDeviceIP.getBytes(Charset.forName("UTF-8"));
                data[offset++] = (byte) ips.length;
                System.arraycopy(ips, 0, data, offset, ips.length);
                offset += ips.length;

                // userData
                byte[] userData = packUserData_Check();
                if (data.length < offset + userData.length) {
                    byte[] tmp = new byte[offset + userData.length];
                    System.arraycopy(data, 0, tmp, 0, offset);
                    data = tmp;
                }
                System.arraycopy(userData, 0, data, offset, userData.length);
                offset += userData.length;
                break;
            }
            default:
        }

        byte[] result = new byte[offset];
        System.arraycopy(data, 0, result, 0, offset);
        return result;
    }


    /**
     * 设备Bean
     * 只要IP一样，则认为是同一个设备
     */
    public static class DeviceBean{
        String ip;      // IP地址
        int port;       // 端口

        public DeviceBean(){}

        public DeviceBean(String ip, int port) {
            this.ip = ip;
            this.port = port;
        }

        @Override
        public int hashCode() {
            return ip.hashCode();
        }

        @Override
        public boolean equals(Object o) {
            if (o instanceof DeviceBean) {
                return this.ip.equals(((DeviceBean)o).getIp());
            }
            return super.equals(o);
        }

        public String getIp() {
            return ip;
        }

        public void setIp(String ip) {
            this.ip = ip;
        }

        public int getPort() {
            return port;
        }

        public void setPort(int port) {
            this.port = port;
        }

    }
}


```


### 接收端

```java

package com.zjun.searcher;

import java.io.IOException;
import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetSocketAddress;
import java.net.SocketException;
import java.net.SocketTimeoutException;
import java.nio.charset.Charset;


public abstract class SearcherDevice extends Thread {

    private int mUserDataMaxLen;

    private volatile boolean mOpenFlag;

    private DatagramSocket mSocket;

    /**
     * 构造函数
     * 不需要用户数据
     */
    public SearcherDevice() {
        this(0);
    }

    /**
     * 构造函数
     *
     * @param userDataMaxLen 搜索主机发送数据的最大长度
     */
    public SearcherDevice(int userDataMaxLen) {
       this.mUserDataMaxLen = userDataMaxLen;
    }

    /**
     * 打开
     * 即可以上线
     */
    public boolean open() {
        // 线程只能start()一次，重启必须重新new。因此这里也只能open()一次
        if (this.getState() != State.NEW) {
            return false;
        }

        mOpenFlag = true;
        this.start();
        return true;
    }

    /**
     * 关闭
     */
    public void close() {
        mOpenFlag = false;
    }

    @Override
    public void run() {
        printLog("设备开启");
        DatagramPacket recePack = null;
		
		
		/***************** Step3 *****************/
		
        try {
            // 实例接收socket 端口号 9000 和发送端一致
            mSocket = new DatagramSocket(SearcherConst.DEVICE_FIND_PORT);
            // 设置接收超时时间
            mSocket.setSoTimeout(SearcherConst.DEVICE_RECEIVE_DEFAULT_TIME_OUT);
            byte[] buf = new byte[32 + mUserDataMaxLen];
            recePack = new DatagramPacket(buf, buf.length);
        } catch (SocketException e) {
            e.printStackTrace();
        }

        if (mSocket == null || mSocket.isClosed() || recePack == null) {
            return;
        }

		
		/***************** Step4 *****************/
		
        while (mOpenFlag) {
            try {
                // 等待接收主机数据报
                mSocket.receive(recePack);

                // 校验接收的数据报
                if (verifySearchData(recePack)) {
                    byte[] sendData = packData();

                    // 给主机发送确认报文,等待主机回复
                    DatagramPacket sendPack = new DatagramPacket(sendData, sendData.length, recePack.getAddress(), recePack.getPort());
                    printLog("接收到请求，给主机回复信息");
                    mSocket.send(sendPack);
                    printLog("等待主机接收确认");
                    mSocket.setSoTimeout(SearcherConst.RECEIVE_TIME_OUT);
                    try {
                        mSocket.receive(recePack);
                        if (verifyCheckData(recePack)) {
                            printLog("确认成功");
                            onDeviceSearched((InetSocketAddress) recePack.getSocketAddress());
                            mOpenFlag = false;
                            break;
                        }
                    } catch (SocketTimeoutException e) {
                    }
                    mSocket.setSoTimeout(SearcherConst.DEVICE_RECEIVE_DEFAULT_TIME_OUT); // 还原连接超时
                }

            } catch (IOException e) {
            }
        }
        mSocket.close();
        printLog("设备关闭或已被找到");
    }

    /**
     * 打包响应报文
     * 协议：$ + packType(1) + userData(n)
     *
     */
    private byte[] packData() {
        byte[] data = new byte[1024];
        int offset = 0;
        data[offset++] = '$';
        data[offset++] = SearcherConst.PACKET_TYPE_FIND_DEVICE_RSP_11;

        // add userData
        byte[] userData = packUserData();
        if (userData.length + offset > data.length) {
            byte[] tmp = new byte[userData.length + offset];
            System.arraycopy(data, 0, tmp, 0, offset);
            data = tmp;
        }
        System.arraycopy(userData, 0, data, offset, userData.length);
        offset += userData.length;

        byte[] retVal = new byte[offset];
        System.arraycopy(data, 0, retVal, 0, offset);

        return retVal;
    }


    /**
     * 校验搜索数据
     * 协议：$ + packType(1) + sendSeq(4) [+ deviceIpLen(1) + deviceIp(n<=15)] [+ userData]
     *  packType - 报文类型
     *  sendSeq - 发送序列
     *  deviceIpLen - 设备IP长度
     *  deviceIp - 设备IP，仅在确认时携带
     *  userData - 用户数据
     */
    private boolean verifySearchData(DatagramPacket pack) {
        if (pack.getLength() < 6) {
            return false;
        }

        byte[] data = pack.getData();
        int offset = pack.getOffset();
        int sendSeq;
        if (data[offset++] != '$' || data[offset++] != SearcherConst.PACKET_TYPE_FIND_DEVICE_REQ_10) {
            return false;
        }
        sendSeq = data[offset++] & 0xFF;
        sendSeq |= (data[offset++] << 8 ) & 0xFF00;
        sendSeq |= (data[offset++] << 16) & 0xFF0000;
        sendSeq |= (data[offset++] << 24) & 0xFF000000;
        if (sendSeq < 1 || sendSeq > 3) {
            return false;
        }

        if (mUserDataMaxLen == 0 && offset == data.length) {
            return true;
        }

        // has userData
        byte[] userData = new byte[pack.getLength() - offset];
        System.arraycopy(data, offset, userData, 0, userData.length);
        return parseUserData(data[1], userData);
    }

    /**
     * 校验确认数据
     * 协议：$ + packType(1) + sendSeq(4) [+ deviceIpLen(1) + deviceIp(n<=15)] [+ userData]
     *  packType - 报文类型
     *  sendSeq - 发送序列
     *  deviceIpLen - 设备IP长度
     *  deviceIp - 设备IP，仅在确认时携带
     *  userData - 用户数据
     */
    private boolean verifyCheckData(DatagramPacket pack) {
        if (pack.getLength() < 6 + 1 +7) {
            return false;
        }

        byte[] data = pack.getData();
        int offset = pack.getOffset();
        int sendSeq;
        if (data[offset++] != '$' || data[offset++] != SearcherConst.PACKET_TYPE_FIND_DEVICE_CHK_12) {
            return false;
        }
        sendSeq = data[offset++] & 0xFF;
        sendSeq |= (data[offset++] << 8 ) & 0xFF;
        sendSeq |= (data[offset++] << 16) & 0xFF00;
        sendSeq |= (data[offset++] << 24) & 0xFF0000;
        if (sendSeq < 1 || sendSeq > SearcherConst.RESPONSE_DEVICE_MAX) {
            return false;
        }

        // ip
        int ipLen = data[offset++];
        if (data.length < offset + ipLen) {
            return false;
        }
        String ip = new String(data, offset, ipLen, Charset.forName("UTF-8"));
        offset += ipLen;
        printLog("Device's ip from host=" + ip);
        if (!isOwnIp(ip)) {
            return false;
        }

        if (mUserDataMaxLen == 0 && offset == data.length) {
            return true;
        }

        // has userData
        byte[] userData = new byte[pack.getLength() - offset];
        System.arraycopy(data, offset, userData, 0, userData.length);
        return parseUserData(data[1], userData);

    }

    /**
     * 打包用户数据
     * 如果调用者需要，则重写
     * @return
     */
    protected byte[] packUserData() {
        return new byte[0];
    }

    /**
     * 当设备被发现时执行
     */
    public abstract void onDeviceSearched(InetSocketAddress socketAddr);

    /**
     * 获取本机在Wifi中的IP
     * 默认都是返回true，如果需要真实验证，需调用自己重写本方法
     * @param ip 需要判断的ip地址
     * @return true-是本机地址
     */
    public boolean isOwnIp(String ip){
        return true;
    }

    /**
     * 解析用户数据
     * 默认返回true，如果调用者有自己的数据，需重写
     * @param type 类型，搜索请求or搜索确认
     * @param userData 用户数据
     * @return 解析结果
     */
    public boolean parseUserData(byte type, byte[] userData) {
        return true;
    }

    /**
     * 打印日志
     * 由调用者打印，SE和Android不同
     */
    public abstract void printLog(String log);

}



```

### 添加用户数据

如果想要添加用户自定义数据,只需要重写主机和接收端相应的打包和解析方法。


* 接收端打包用户数据

```java

		   /**
             * 响应时的打包数据格式
             * dataType(1) + len(4) + data(n)
             */
			 
            @Override
            protected byte[] packUserData() {
                String name = "LED灯";
                String room = "客厅";
                try {
				
				    // nameBytes:{76,69,68,-25,-127,-81}
                    byte[] nameBytes = name.getBytes("UTF-8");
					
					// nameBytes:{-27,-82,-94,-27,-114,-123}
                    byte[] roomBytes = room.getBytes("UTF-8");
					
					// 用户数据总大小： dataType(1) + len(4) + data(6) + dataType(1) + len(4) + data(6) = 22
					byte[] data = new byte[5 + nameBytes.length + 5 + roomBytes.length];
                    int offset = 0;

					// 用1位存数据类型 DEVICE_TYPE_NAME_21 = 0x21;
                    data[offset++] = DEVICE_TYPE_NAME_21;
					
                    // 用4位存用户数据大小
                    data[offset++] = (byte) nameBytes.length;
                    data[offset++] = (byte) (nameBytes.length >> 8);
                    data[offset++] = (byte) (nameBytes.length >> 16);
                    data[offset++] = (byte) (nameBytes.length >> 24);
					
                    System.arraycopy(nameBytes, 0 , data, offset, nameBytes.length);
                    offset += nameBytes.length;
					
					// 用1位存数据类型 DEVICE_TYPE_ROOM_22 = 0x22;
                    data[offset++] = DEVICE_TYPE_ROOM_22;
					
                    data[offset++] = (byte) roomBytes.length;
                    data[offset++] = (byte) (roomBytes.length >> 8);
                    data[offset++] = (byte) (roomBytes.length >> 16);
                    data[offset++] = (byte) (roomBytes.length >> 24);
                    System.arraycopy(roomBytes, 0 , data, offset, roomBytes.length);

                    return data;
                } catch (UnsupportedEncodingException e) {
                    e.printStackTrace();
                }
                return super.packUserData();
            }


```

* 主机解析用户数据

```java

			/**
             * 解析用户数据
             * dataType(1) + len(4) + data(n)
             * @param type 类型
             * @param device 设备
             * @param userData 数据
             * @return true-解析成功
             */
			 
            @Override
            public boolean parseUserData(byte type, MyDevice device, byte[] userData) {
			            
						// 用户数据如果小于5,数据错误
						if (userData.length < 5) {
                            return false;
                        }
                        int offset = 0;
                        
						// 解析用户数据部分
                        while (offset + 5 < userData.length) {
                            byte dataType = userData[offset++];
                            int len = (userData[offset++] & 0xFF)
                                    | ((userData[offset++] & 0xFF) << 8)
                                    | ((userData[offset++] & 0xFF) << 16)
                                    | ((userData[offset++] & 0xFF) << 24);
                            if (len + offset > userData.length) {
                                return false;
                            }
							
							switch (dataType) {
							    //解析`LED灯`
                                case DEVICE_TYPE_NAME_21:
                                    String name = null;
                                    try {
                                        name = new String(userData, offset, len, "UTF-8");
                                    } catch (UnsupportedEncodingException e) {
                                        e.printStackTrace();
                                    }
                                    if (name != null) {
                                        device.setName(name);
                                    }
                                    break;
								//解析`客厅`	
                                case DEVICE_TYPE_ROOM_22:
                                    String room = null;
                                    try {
                                        room = new String(userData, offset, len, "UTF-8");
                                    } catch (UnsupportedEncodingException e) {
                                        e.printStackTrace();
                                    }
                                    if (room != null) {
                                        device.setRoom(room);
                                    }
                                    break;
                                default:
                            }
            }

```

### 备注 

* `2017/06/26` 路由器 需要开启`SSID` 广播来支持，`UDP` 广播。

































