与普通的小车不同，杨总的小车主要是使用了usart1和usart2口作为调试口，调试命令从usart1口输入，从usart2输出，并且对电机发生作用。

## 注意

**为了表示usart1口的指令是正确的，从usart2口输出的数据会将关键字符 X Y F替换成A B C。**

## dual板图片

![3C198664-D907-4C72-93F4-150CBF516EBA](http://leisong03.github.io/images/3C198664-D907-4C72-93F4-150CBF516EBA.png)

## 接线方法

### 原始总体接线图

| 板上电机端口 | AGV功能链接 | 备注                                       |
| :----- | ------- | :--------------------------------------- |
| X电机端口  | 左轮驱动    | 1EN(EN)，2FR(MS1)，3PG(MS2)，4BK(ST)        |
| Y电机端口  | 右轮驱动    | 1EN(EN)，2FR(MS1)，3PG(MS2)，4BK(ST)        |
| Z电机端口  | 无       |                                          |
| E0电机端口 | 左右轮SV   | 1GND，2 SV_左，3 SV_右，4GND 直连板接E0DIR和E0EN   |
| E1电机端口 | 寻磁      | 1GND，2TX，3RX，4GND。(E1DIR和usart3_RX复用，E1STEP和usart3_tx复用) |
| usart1 | rfid    | 1GND，2TX，3RX，4GND                        |
| usart2 | 超声      | 1GND，2TX，3RX，4GND                        |
| usb口   | 摄像头     | VLC免驱摄像头                                 |
| 音频输入   | 单mic    |                                          |
| 音频输出   | 运放模块    |                                          |

### 杨总版本接线图

| 板上电机端口 | AGV功能链接             | 备注                                       |
| :----- | ------------------- | :--------------------------------------- |
| X电机端口  | 左轮驱动                | ~~1EN(EN)，2FR(MS1)，3PG(MS2)，4BK(ST)~~ ***1GND 2 EN 3, STEP 4 DIR 5 SV*** |
| Y电机端口  | 右轮驱动                | ~~1EN(EN)，2FR(MS1)，3PG(MS2)，4BK(ST)~~ **1GND 2 EN 3, STEP 4 DIR 5 SV** |
| Z_MIN  | 左轮PG                | **1 PG, 2 GND, 3 5V**                    |
| Z_MAX  | 右轮PG                | **1 PG, 2 GND, 3 5V**                    |
| Z电机端口  | 无                   |                                          |
| E0电机端口 | 左右轮SV               | ~~1GND，2 SV_左，3 SV_右，4GND 直连板接E0DIR和E0EN~~ |
| E1电机端口 | 寻磁                  | 1GND，2TX，3RX，4GND。(E1DIR和usart3_RX复用，E1STEP和usart3_tx复用) |
| usart1 | ~~rfid~~ **控制命令输入** | 1GND，2TX，3RX，4GND                        |
| usart2 | ~~超声~~ **控制指令输出**   | 1GND，2TX，3RX，4GND                        |
| usb口   | 摄像头                 | VLC免驱摄像头                                 |
| 音频输入   | 单mic                |                                          |
| 音频输出   | 运放模块                |                                          |

## 命令解析程序

对于串口收到数据的解析用如下程序完成：

```c++
if(USART_GetITStatus(USART1,USART_IT_RXNE)!=RESET){
	uint8_t ch = USART_ReceiveData(USART1);
	if(!usart1Ready){
	    if(ch!='X'){
		usart1Idx = 0;
		return;
	    }
	    else
		usart1Ready = 1;
	} 
	rx1Data[usart1Idx++] = ch;

	if(ch == '\n'){
	    usart1Ready = 0;
	    uint8_t strLen = usartIdx;
	    usart1Idx = 0;
	    const char keyword[] = {'X','Y','F'};
	    for(int i =0;i<3;i++){
		int j = 0;
		while(j<strLen && rx1Data[j]!=keyword[i])
		    j++;
		if(j<strLen){
		    sscanf((const char*)(rx1Data+j),"%d",&(param[i]));
		}
		toReplayOverUsart2 = 1;
	    }
	    for(int i=0;i<2;i++){
		setAxisDir(i,param[i]);
	    }
	}
	USART_ClearITPendingBit(USART1,USART_IT_RXNE);
    }
}
```

从usart2 输出的包的输出程序

 uint8_t strBuf[30];

```c++
    sprintf((char*)strBuf,"A%d B%d C%d\n",param[0],param[1],param[2]);
    for(int i = 0;i<strlen((const char*)strBuf);i++){
	USART_SendData(EVAL_COM2, (uint8_t) strBuf[i]);
	while (USART_GetFlagStatus(EVAL_COM2, USART_FLAG_TC) == RESET){};
    }
```




## 串口通信参数

波特率：9600  8n1

## 控制指令格式

底盘车通信格式

| 字段1  | 字段2  | 字段3  | 字段4  | 字段5  | 字段6  | 字段7  | 字段8  |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| X    | 数字1  | 空格   | Y    | 数字2  | F    | 数字3  | 换行符  |

 数字1左轮正负分别代表前进后退 大小代表转N个10分之1圈

数字2右轮正负分别代表前进后退大小代表转N个10分之1圈

数字3代表运行速度 范围为1-30

## 命令例子

X10 Y10 F20↵

车前进1圈，运行速度为20