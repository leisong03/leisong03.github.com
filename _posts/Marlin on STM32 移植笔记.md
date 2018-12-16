---
typora-copy-images-to: ../images
---

# Marlin on STM32 移植笔记

[TOC]

## Marlin的移植

marlin是基于徐通提交的爱思特的现成版本进行移植的。marlin是基于ardiuno开发的，所以程序有两个入口

Marlin_main.cpp文件里面。入口1是

```java
void setup() {
```

入口2是

```java
void loop() {
```



### STM32程序的烧录

安装J-Flash软件后，运行J-Flash

在windows电脑上安装了J-Link软件之后，我们可以对STM32的固件进行更新。更新是通过串口进行的，当使用串口连接电脑和STM32之后，我们运行J-Flash可以看到如下界面。

![image-20180912190735279](/Users/lei/Documents/workshop/githubleisong03/images/image-20180912190735279.png)

在该界面中选择预存好的stm32Marlin.hex文件

 ![image-20180912190552791](/Users/lei/Documents/workshop/githubleisong03/images/image-20180912190552791.png)

![image-20180912190747385](/Users/lei/Documents/workshop/githubleisong03/images/image-20180912190747385.png)

 

3、之后点击options下的project settings完成相应设置，首先完成General下选择相应的串口连接，操作步骤如下

![image-20180912190801704](/Users/lei/Documents/workshop/githubleisong03/images/image-20180912190801704.png)

4、然后切换Target Interface标签，选择SWD选项，其余设置为默认状态或参考如图所示数值，具体步骤如下。

 ![image-20180912190814527](/Users/lei/Documents/workshop/githubleisong03/images/image-20180912190814527.png)

5、然后切换至CPU标签，选择Device选项栏，点击打开select device。

![image-20180912190828697](/Users/lei/Documents/workshop/githubleisong03/images/image-20180912190828697.png)

6、在Select device界面中，对Manufacturer选择ST栏，并在设备Device内选择STM32F103VC，单击OK确认选择退出。

 ![image-20180912190933561](/Users/lei/Documents/workshop/githubleisong03/images/image-20180912190933561.png)

7、最后在Production标签中勾选Secure chip选项，点击应用，确定退出设置，操作步骤如下图所示。

 ![image-20180912190941811](/Users/lei/Documents/workshop/githubleisong03/images/image-20180912190941811.png)



### STM32程序的编译



## Android程序的修改

### MagicFirmSlave.java的修改

这里主要是修改了gcode输出的端口，从usb端口修改到了内置的ttyS端口

修改前

```java
@Override

    public synchronized boolean connect() {

        try {

            serialPort = new SerialPort(new File("/dev/ttyUSB0"), 115200, 0);

            is = serialPort.getInputStream();

            os = serialPort.getOutputStream();

            if(is != null && os != null){
```

修改后
```java
@Override

    public synchronized boolean connect() {

        try {

            serialPort = new SerialPort(new File("/dev/ttyS0"), 115200, 0);

            is = serialPort.getInputStream();

            os = serialPort.getOutputStream();

            if(is != null && os != null){
```
### 其余文件的修改
所有的ttyUSB1修改为ttyUSB0.

## 板子的连接图

![image-20180915132040790](../images/image-20180915132040790.png)

### 两个USART口的互联

![image-20180915132354269](/Users/lei/Documents/workshop/githubleisong03/images/image-20180915132354269.png)

### 热床的连接

因为板子尺寸较小发热量较大，热床需要用外置接法，需要如下小模块

![image-20180915134202791](/Users/lei/Documents/workshop/githubleisong03/images/image-20180915134202791.png)

![image-20180915133540452](/Users/lei/Documents/workshop/githubleisong03/images/image-20180915133540452.png)

###风扇的连接

2号针接+

1号针接-

### 加热棒的连接

2号针接+

1号针接-

### RFID的链接方法

同之前，插入三个USB口中的一个


