

* content
{:toc}

本文档介绍了用`Rayland-DUAL`主板控制鸵鸟机器人的控制协议。



## 鸵鸟机器人控制协议


`Android -> 下位机`

1.1 **控制指令**  

`cmd_type,pkg_length,seq,cmd_io,s_x,s_y,v_x,v_y`


| Param      |  Describe  |  Type  |   Range    |          Unit |
| ---------- | :--------: | :----: | :--------: | ------------: |
| cmd_type   |    指令类型    | uint8  |     0      |               |
| pkg_length |    包长度     | uint8  | `12bytes`  |               |
| seq        |    指令序号    | uint8  |            |               |
| cmd_io     | io口控制指令（8） | uint8  |   `0;1`    |               |
| s_x        |    左轮里程    | uint16 |            | `1 = (1/10)r` |
| s_y        |    右轮里程    | uint16 |            | `1 = (1/10)r` |
| v_x        |    左轮速度    | int16  | -500 ~ 500 | `(1/10)r/min` |
| v_y        |    右轮速度    | int16  | -500 ~ 500 | `(1/10)r/min` |


`下位机 -> Android`

1.1 **状态回传** 

`cmd_type,pkg_length,seq,lim,pro_change,pulse_count`

| Param       |  Describe  |    Type    |     Range | Unit |
| ----------- | :--------: | :--------: | --------: | ---: |
| cmd_type    |    指令类型    |   uint8    |         0 |      |
| pkg_length  |    包长度     |   uint8    | `22bytes` |      |
| seq         |    指令序号    |   uint8    |           |      |
| lim         | 限位开关信息（15） |   uint16   |     `0;1` |      |
| pro         |   优先级控制    |   uint8    |           |      |
| pulse_count |  脉冲计数（4）   | uint32 * 4 |           |      |



`云台 -> Android`

1.1 **摄像头控制指令**  

`cmd_type,pkg_length,seq,angle_hor,angle_ver`

| Param      | Describe | Type  |   Range    | Unit |
| ---------- | :------: | :---: | :--------: | ---: |
| cmd_type   |   指令类型   | uint8 |     1      |      |
| pkg_length |   包长度    | uint8 |  `5bytes`  |      |
| seq        |   指令序号   | uint8 |            |      |
| angle_hor  | 水平方向转动角度 | int8  | `-180~180` |    ° |
| angle_ver  | 垂直方向转动角度 | int8  | `-180~180` |    ° |

2.2 **腰、升降控制指令**  

`cmd_type,pkg_length,seq,s_w,s_u`

| Param      | Describe | Type  |    Range | Unit |
| ---------- | :------: | :---: | -------: | ---: |
| cmd_type   |   指令类型   | uint8 |        2 |      |
| pkg_length |   包长度    | uint8 | `7bytes` |      |
| seq        |   指令序号   | uint8 |          |      |
| s_w        |   转腰里程   | int16 |          |      |
| s_u        |   升降里程   | int16 |          |      |



