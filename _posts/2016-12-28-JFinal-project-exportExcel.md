---
layout: post
title:  "Jfinal 导出Excel（1）"
date:   2016-12-18 19:00:00 +0800
categories: Java
tags: Java JFinal
author: JiuYang Chen
---

* content
{:toc}

本篇介绍基于`JFinal`将列表导出成`Excel`。



## `JFinal`导出`Excel`

### `Apache POI`

`Apache POI`是`Apache`软件基金会的开放源码函式库，POI提供API给Java程序对`Microsoft Office`格式档案读和写的功能。

结构：

* HSSF － 提供读写Microsoft Excel XLS格式档案的功能。

* XSSF － 提供读写Microsoft Excel OOXML XLSX格式档案的功能。

* HWPF － 提供读写Microsoft Word DOC97格式档案的功能。

* XWPF － 提供读写Microsoft Word DOC2003格式档案的功能。

* HSLF － 提供读写Microsoft PowerPoint格式档案的功能。

* HDGF － 提供读Microsoft Visio格式档案的功能。

* HPBF － 提供读Microsoft Publisher格式档案的功能。

* HSMF － 提供读Microsoft Outlook格式档案的功能。

`Maven`地址：

```xml

<!-- https://mvnrepository.com/artifact/org.apache.poi/poi -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>3.9</version>
</dependency>

```

### 导出`Excel`

添加`Excel`操作类,`XLSFileKit`。

```java

package common.util;

import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.util.List;

import org.apache.poi.hssf.usermodel.HSSFCell;
import org.apache.poi.hssf.usermodel.HSSFRow;
import org.apache.poi.hssf.usermodel.HSSFSheet;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;

public class XLSFileKit {

    // 创建一个`excel`文件
	private HSSFWorkbook workBook;
	
    // `excel`文件保存路径
	private String filePath;
	public XLSFileKit(String filePath){
		this.filePath=filePath;
		this.workBook=new HSSFWorkbook();
	}
	
	/**
	 * 添加sheet
	 * @param content 数据
	 * @param sheetName sheet名称
	 * @param title 标题
	 */
	public <T> void addSheet(List<List<T>> content,String sheetName,List<String> title){
		HSSFSheet sheet=this.workBook.createSheet(sheetName);
		
		// `excel`中的一行
    	HSSFRow row=null;
		
		// `excel`中的一个单元格
    	HSSFCell cell=null;
    	int i=0,j=0;
		
		// 创建第一行，添加`title`
    	row=sheet.createRow(0);
    	for(;j<title.size();j++){//添加标题
    		cell=row.createCell(j);
			cell.setCellValue(title.get(j));
    	}
		
		// 创建余下所有行
    	i=1;
    	for(List<T> rowContent:content){
    		row=sheet.createRow(i);
    		j=0;
    		for(Object cellContent:rowContent){
    			cell=row.createCell(j);
    			cell.setCellValue(cellContent.toString());
    			j++;
    		}
    		i++;
    	}
	}
	
	/**
	 * 保存
	 * @return
	 */
	public boolean save(){
		try {
			FileOutputStream fos=new FileOutputStream(this.filePath);
			this.workBook.write(fos);
			fos.close();
			return true;
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
    	return false;
	}
}


```

添加`ExportExcelController`:

```java

package controller;

import java.io.File;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;
import java.util.UUID;

import common.util.XLSFileKit;
import service.ExportExcelService;

import com.jfinal.core.Controller;
import com.jfinal.plugin.activerecord.Record;


public class ExportExcelController extends Controller{

    // 和`ExportExcelController`对应的`Service`类
	ExportExcelService exportExcelService = new ExportExcelService();
	
	/**
	 * 导出表格
	 */
	public void exportOutExcel(){
		String sheetName = getPara("sheetName");		
		
		// 导出`Excel`名称
		String fileName = new Date().getTime() + "_" + UUID.randomUUID().toString() + ".xls";
		
		// excel`保存路径
		String filePath = getRequest().getRealPath("/") + "/file/export/";
		File file = new File(filePath);
		if(!file.exists()){
			file.mkdirs();
		}
		String relativePath = "/file/export/" + fileName;
		filePath += fileName;
		XLSFileKit xlsFileKit = new XLSFileKit(filePath);
		List<List<Object>> content = new ArrayList<List<Object>>();
		List<String> title = new ArrayList<String>();
		
		List<Record> datas = exportExcelService.getList();
		
		// 添加`title`,对应的从数据库检索出`datas`的`title`
		
		title.add("序号");
		title.add("id");
		title.add("caseId");
		title.add("imgId");
		int i = 0;
		OK:
		while(true){
			if(datas.size() < (i + 1)){
				break OK;
			}
			// 判断单元格是否为空，不为空添加数据 
			int index = i + 1;
			List<Object> row = new ArrayList<Object>(); 
			row.add(index + "");
			row.add(null==datas.get(i).get("id")?"":datas.get(i).get("id"));
			row.add(null==datas.get(i).get("caseId")?"":datas.get(i).get("caseId"));
			row.add(null==datas.get(i).get("imgId")?"":datas.get(i).get("imgId"));
			content.add(row);
			i ++;
		}
		xlsFileKit.addSheet(content, sheetName, title);
		xlsFileKit.save();
		renderJson(new Record().set("relativePath", relativePath));
	}
	
}



```



对应的`Service`类，`ExportExcelService`


```java

package service;

import java.util.List;

import com.jfinal.plugin.activerecord.Db;
import com.jfinal.plugin.activerecord.Record;

public class ExportExcelService {

	public List<Record> getList(){
		
		String sql = "select * from zt_case_img";
		
		return Db.find(sql);
	}
	
}


```

然后再页面上添加一个按钮调用这个方法即可，另还要在`config`中配置路由。

```java

		me.add("/excel", ExportExcelController.class);

```

调用方法：


```js

$("#export").click(function(){
    	
    	$.post("excel/exportOutExcel",{sheetName:"报表"},function(data){
    		
    		var relativePath = data.relativePath;
			window.location.href = ".." + relativePath;
    		
    	});
    });

```

调用成功后会自动在`webRoot/file/export/`下生成一个你导出的`excel`.


		
项目目录结构：

![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1fbegblsvzmj20di07ggln.jpg)

导出成功：

![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1fbegbmq04ej209l07iq34.jpg)



[部分源代码](https://github.com/Chenjy1225/ChenjyDemo/tree/gh-pages/JFinal_demo_ExportExcel)