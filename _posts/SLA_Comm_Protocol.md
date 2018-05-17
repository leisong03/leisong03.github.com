---
typora-copy-images-to: ../images
---

# SLA控制协议

SLA主板在使用TTL激光器控制的时候遇到一个问题就是激光器无法精准控制延迟。目前的激光控制是伴随振镜步进进行的。

也就是说每进行一次脉冲步进才能进行一次激光的开关。按照目前TIM5的频率是40khz计算。激光延迟的最小单位是插点时间间距。
$$
\Delta = \frac{1}{f} = \frac{1}{40k} = 25\mu s
$$
所以需要在这个延迟的基础上将将激光的延迟开关周期增加到1us这个单位，也就是说需要将现在的控制精度提高100倍左右，目前的激光延迟是用一个`uint16_t`的变量存储的，我们需要将这个变量的长度修改为`uint32_t`。然后在TIM5的中断响应函数里面对额外加入的这些精度进行利用。

![0A02105D-9CE3-4C1A-B35D-DD67206748C6](../images/0A02105D-9CE3-4C1A-B35D-DD67206748C6.png)

T1:开激光延迟

T2:关激光延迟

## A33->STM32的配置包

总包长 20bytes

| 编号           | 类型     | 名称       | 备注                   |
| -------------- | -------- | ---------- | ---------------------- |
| Byte0          | uint8_t  | head       | 0x68                   |
| Byte1          | uint8_t  | seq        | 自增序列号             |
| Byte2          | uint8_t  | pktType    | 包类型(2)              |
| Byte3          | uint8_t  | 开激光电平 | 0:低电压开，1:高电压开 |
| Byte4~Byte5    | uint16_t | onDelay    | 开激光延迟（单位us）   |
| Byte6~Byte7    | uint16_t | offDelay   | 关激光延迟（单位us）   |
| Byte8~Byte9    | uint16_t | beforeJump | 快跳延迟（单位us）     |
| Byte10～Byte17 | uint8_t  | reserve[8] | 备用                   |
| Byte18         | uint8_t  | tail       | 0xFF                   |
| Byte19         | uint8_t  | checksum   | 校验和                 |

## A33->STM32的数据包

总包长 20bytes

| 编号          | 类型     | 名称       | 备注         |
| ------------- | -------- | ---------- | ------------ |
| Byte0         | uint8_t  | head       | 0x68         |
| Byte1         | uint8_t  | seq        | 自增序列号   |
| Byte2         | uint8_t  | pktType    | 包类型(1)    |
| Byte3         | uint8_t  | ctrl       | 控制位       |
| Byte4~Byte7   | uint16_t | posXY[2]   | 目标位置     |
| Byte8~Byte11  | uint16_t | stepCnt[2] | 插点数量     |
| Byte12~Byte15 | int16_t  | stepXY[2]  | 插点空间间距 |
| Byte16        | uint8_t  | stepPeriod | 插点时间间距 |
| Byte17        | uint8_t  | reserve    | 备用         |
| Byte18        | uint8_t  | tail       | 0xFF         |
| Byte19        | uint8_t  | checksum   | 校验和       |

ctrl内容

| 7        | 6        | 5        | 4        | 3        | 2       | 1     | 0       |
| -------- | -------- | -------- | -------- | -------- | ------- | ----- | ------- |
| reserved | reserved | reserved | reserved | reserved | AutoOff | onOff | isReset |

## STM32程序解析

从A33来的SPI协议是用DMA功能接收下来的，在中断中的入口是：

`DMA1_Channel4_IRQHandler`

进来之后先检查校验和

```c
for(int j = 0;j<SLA_PKT_SIZE-1;j++){
    sum+= spiRxBuf[j];
}
```
校验和不对就直接丢包

```c
if(sum != spiRxBuf[SLA_PKT_SIZE-1]){
    DMA_SetCurrDataCounter(IN_SPI_RX_DMA_Channel,SLA_PKT_SIZE);
    return;
}
```
这里面`DMA_SetCurrDataCounter(IN_SPI_RX_DMA_Channel,SLA_PKT_SIZE);`是清空DMA寄存器的意思

如果是运动包

```C
    CRITICAL_SECTION_START;
    memcpy(sla_buffer[in],spiRxBuf,SLA_PKT_SIZE);
    //memcpy(rxSPI.a,spiRxBuf,SLA_PKT_SIZE);
    in = (in+1) % SLA_BUFFER_SIZE;
    spiTxBuf[0]--;
    SLABufferisEmpty = 0;
    pkt_cnt++;
    CRITICAL_SECTION_END;
```
则如循环队列，减接收容量。

如果是配置包

​	    `memcpy(thisConfPkt.a,spiRxBuf,SLA_PKT_SIZE);`

直接拷贝进配置文件

主要的working horse再tim5当中

`void TIM5_IRQHandler(void)`

首先出队列

```C
	CRITICAL_SECTION_START;
	memcpy(thisMovePkt.a,sla_buffer[out],SLA_PKT_SIZE);
	slaIsWorking = 1;
	spiTxBuf[1] = 1;
	out = (out+1)%SLA_BUFFER_SIZE;
	spiTxBuf[0]++;
	CRITICAL_SECTION_END;
```
这一段是对激光的处理

```C
	toTurnOff = 1;
	if(spiTxBuf[0]<SLA_BUFFER_SIZE){ 
	    if((sla_buffer[out][3]&(1<<1)) != 0){ 
		toTurnOff = 0;
	    }
	}else{
	    if((thisMovePkt.b.ctrl&(1<<2)) == 0){ 
		toTurnOff = 0;
	    }
	}
	if(((thisMovePkt.b.ctrl&(1<<1)) == 0)){
	    SET_LASER_PWR(0);
	    toTurnOff = 1;
	}
```
默认就关激光

如果当前运动指令不是最后一条，

那么看下一条是不是要关激光，如果下一条是开激光，那么当前这一条就不自动关

但如果本条指令就是关继光指令，那么立刻关，毫不犹豫。

