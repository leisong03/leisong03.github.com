---
typora-copy-images-to: ../images
---

# SLA控制协议

SLA主板在使用TTL激光器控制的时候遇到一个问题就是激光器无法精准控制延迟。目前的激光控制是伴随振镜步进进行的。

也就是说每进行一次脉冲步进才能进行一次激光的开关。按照目前TIM5的频率是40khz计算。激光延迟的最小单位是插点时间间距。
$$
\Delta = \frac{1}{f} = \frac{1}{40k} = 25\mu s
$$
所以需要在这个延迟的基础上将将激光的延迟开关周期增加到1us这个单位，也就是说需要将现在的控制精度提高100倍左右，目前的激光延迟是用一个`uint16_t`的变量存储的，我们需要将这个变量的长度修改为`uint32_t`。然后在TIM5的中断响应函数里面对额外加入的这些精度进行利用。在之前的协议里T1和T2的最小单位是振镜的插点时间间距 ，现在要改成插点时间间距的1/1000。大概是0.25us。所以需要4Mhz的时钟，STM32应该刚刚够。

![0A02105D-9CE3-4C1A-B35D-DD67206748C6](../images/0A02105D-9CE3-4C1A-B35D-DD67206748C6.png)

## A33->STM32的数据包

总包长22

| 编号            | 类型       | 名称         | 备注               |
| ------------- | -------- | ---------- | ---------------- |
| Byte0         | uint8_t  | head       | 0x68             |
| Byte1         | uint8_t  | seq        | 自增序列号            |
| Byte2         | uint8_t  | ctrl       | 控制位              |
| Byte3~Byte4   | uint16_t | headDelay  | 最小单位是插点时间间距的百分之一 |
| Byte5~Byte6   | Uint16_t | tailDelay  | 最小单位是插点时间间距的百分之一 |
| Byte7~Byte10  | uint16_t | posXY[2]   | 目标位置             |
| Byte11~Byte14 | uint16_t | stepCnt[2] | 插点数量             |
| Byte15~Byte18 | int16_t  | stepXY[2]  | 插点空间间距           |
| Byte19        | uint8_t  | stepPeriod | 插点时间间距           |
| Byte20        | uint8_t  | tail       | 0xFF             |
| Byte21        | uint8_t  | checksum   | 校验和              |
|               |          |            |                  |

ctrl内容

| 7        | 6        | 5        | 4        | 3        | 2              | 1              | 0       |
| -------- | -------- | -------- | -------- | -------- | -------------- | -------------- | ------- |
| reserved | reserved | reserved | reserved | reserved | TailLaserLevel | headLaserLevel | isReset |

