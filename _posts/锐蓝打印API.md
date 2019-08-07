#锐蓝打印API

![image-20190118074219245](/Users/lei/Library/Application Support/typora-user-images/image-20190118074219245.png)

##网络API



这是锐蓝打印机开放平台的API描述。在这个文档中描述了 平台中关于厂商，材料，设计，打印机的API，利用这些API可以实现打印机接入铭展网络的开放平台，并且可以对打印机实施远程控制。

###HTTP协议 

```GET /jobs ```

查询打印任务

```POST /jobs ```

查询打印任务

```GET /jobs/{jobID} ```

获得一个已经存在的打印任务的信息

```POST /jobs-in-list ```

根据打印任务的id列表查询设备

```GET /devices ```

查询打印机

```POST /devices-in-list ```

根据设备id查询设备

```GET /devices/{deviceId} ```

获得打印机信息

```POST /devices/{deviceId}/command/{command} ```

获得打印机信息

### JSON格式

####文件页面

Page

{

| totalEntries         | integer*example: 1,*                           |
| -------------------- | ---------------------------------------------- |
| entriesPerPage       | integer*example: 10,*每一页10条数据            |
| entriesInCurrentPage | integer*example: 8,*当前页数据的条数           |
| currentPage          | integer*example: 1*当前页的页号，第一页页号是1 |

}

####模型文件

Model{

​	name	string

​	extruderCount	integer 	*default: 1*

​	hasHeatedBed	integer	*default: 0*

​	invertZinteger	integer	*default: 0*

​	maxBedTemperature	integer	*default: 240*

​	maxNozzleTemperature	integer	*default: 100*

​	workQualitystring	*default: normal*

​	Enum:	Array [ 3 ]

}

####打印材料

Material{

​	name	string

​	filamentDiameter	integer	*example: 1*

​	temperature	integer	*example: 220*

​	bedTemperrature	integer	*example: 200*

}

####打印工作

SlimJob{

​	gcodeFileUrl	string	*example: 	http://op2ssjjrd.bkt.clouddn.com/snake.gcode*

​	deviceId	string	*example: 876366067151278080*

​	name	string	*example: 我的小小房子*

​	imageUrl	string	*example:http://pic15.nipic.com/20110616/5001675_165112690000_2.jpg*

​	filamentId	string*example: SP01*

****}