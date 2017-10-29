---
layout: post
title:  "JDBC 基础知识"
date:   2017-03-25 11:00:00 +0800
categories: JDBC 
tags: JDBC
author: JiuYang Chen
---



* content
{:toc}




本篇简单介绍`JDBC`


## `JDBC`基本步骤：

1.1 加载`JDBC`驱动

2.2 定义连接的`URL`

3.3 建立数据库连接

4.4 创建`Statement`对象

5.5 执行数据库操作

6.6 处理结束

7.7 关闭连接


* 在同一时间下，每个`Statement`对象只能打开一个`ResultSet`对象。所以，假如有两个同样结果的结果集在交叉访问，那么这两个结果集必定为两个不同的`Statement`对象所创建。如果在打开一个新的结果集的时候存在一个已经打开的结果集，则这个已经存在的结果集会被隐式的关闭。    

* 默认情况下`ResultSet`(结果集)是不可更新的，且只能向前移动。

* `ResultSet`的生命周期和`JDBC`操作相关,如果`Statement`和`Connection`要得到释放,`ResultSet`首先得释放。


## `JDBC` 常用 `API`

### `Connection`

    1.createStatement()：创建数据库连接
	
    2.prepareStatement(String sql):创建预处理语句
	
    3.prepareCall(String sql):创建可调用语句

    4.getAutoCommit():获取自动提交的模式
	
    5.setAutoCommit():设置自动提交的模式
    
    6.commit():提交所执行的SQL语句
	
    7.rollback():回滚所执行的SQL语句

    8.getMetaData():获取一个DatabaseMetaData对象，该对象包含了有关数据库的基本信息

    9.close():关闭数据库连接
	
    10.isClose()：判断数据库连接是否超时或被显示关闭

### `Statement`

    1.execute(String sql):执行SQL语句，如果返回值是结果集则为true,否则为false
	
    2.executeQuery(String sql):执行SQL语句，返回值为ResultSet
	
    3.executeUpdate(String sql):执行SQL语句，返回值为所影响的行数
    
    4.addBatch(String sql):向当前Statement对象的命令列表中添加新的批处理SQL语句
	
    5.clearBatch():清空当前Statement对象的命令列表
	
    6.executeBatch()：执行当前Statement对象的批处理语句，返回值为每个语句所影响的函数数组

    7.getConnection():返回创建了该Statement对象的Connection对象

    8.getQueryTimeout():获取等待处理结果的时间
	
    9.setQueryTimeout():设置等待处理结果的时间
          
### `ResultSet`   
 
    1.first()/beforeFirst():将游标移动到ResultSet中第一条记录(的前面)
	
    2.last()/afterLast():将游标移动到ResultSet中最后一条记录(的后面)

    3.absolute(int column):将游标移动到相对于第一行的指定行，负数则为相对于最后一条记录
	
    4.relative(int rows):将游标移动到相对于当前行的第几行,正为向下，负为向上

    5.next():将游标下移一行
	
    6.previous():将游标上移一行

    7.insertRow():向当前ResultSet和数据库中被插入行处插入一条记录
	
    8.deleteRow():将当前ResultSet中的当前行和数据库中对应的记录删除
	
    9.updateRow():用当前ResultSet中已更新的记录更新数据库中对应的记录
	
    10.cancelUpdate():取消当前对ResultSet和数据库中所做的操作

    11.findColumn(String columnName):返回当前ResultSet中与指定列名对应的索引

    12.getRow():返回ResultSet中的当前行号

    13.refreshRow():更新当前ResultSet中的所有记录

    14.getMetaData():返回描述ResultSet的ResultSetMetaData对象

    15.isAfterLast(): 是否到了结尾
	
    16.isBeforeFirst(): 是否到了开头
	
    17.isFirst():是否第一条记录   
	
    18.isLast(): 是否最后一条记录

    19.wasNull():检查列值是否为NULL值，如果列的类型为基本类型，且数据库中的值为0，那么这项检查就很重要。由于数据库NULL也返回0，所以0值和数据库的NULL不能区分。如果列的类型为对象，可以简单地将返回值与null比较
    
    20.close():关闭当前ResultSet


## 数据库连接池

数据库连接池的基本思想就是为数据库连接建立一个“缓冲池”。预先在缓冲池中放入一定数量的连接，当需要建立数据库连接时，只需从“缓冲池”中取出一个，使用完毕之后再放回去。

我们可以通过设定连接池最大连接数来防止系统无尽的与数据库连接。更为重要的是我们可以通过连接池的管理机制监视数据库的连接的数量、使用情况，为系统开发、测试及性能调整提供依据。 
















