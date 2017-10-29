---
layout: post
title:  "Jfinal 导入Excel（2）"
date:   2016-12-31 19:00:00 +0800
categories: Java
tags: Java JFinal
author: JiuYang Chen
---

* content
{:toc}

前面我们在JFianl项目中把Excel导出去了，所以我们要想办法把它导回来。



## `JFinal`导入`Excel`

### 导入依赖`jar`包

`poi-ooxml-3.8-20120326.jar` 和 `xmlbeans-2.3.0.jar`

### 导入`Excel`

我们导入前端插件还是我们之前使用过的`uploadify`。

当我们接收了`Excel`文件。我们首先要判断一下是否为`Excel`文件以及`Excel`的版本。

添加`Excel`文件检测类，`CEVUtil`。


```java

package util;

import java.io.BufferedInputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;

import org.apache.poi.POIXMLDocument;
import org.apache.poi.poifs.filesystem.POIFSFileSystem;

public class CEVUtil {
	/**
	* 依据后缀名判断读取的是否为Excel文件
	* @param filePath
	* @return
	*/
	public static boolean isExcel(String filePath){
	     if(filePath.matches("^.+\\.(?i)(xls)$")||filePath.matches("^.+\\.(?i)(xlsx)$")){
	    	 return true;
	     }
	     return false;
	} 
	     
     /** 
      * 检查文件是否存在 
      */  
     public static boolean fileExist(String filePath){
	     if(filePath == null || filePath.trim().equals("")) 
	    	 return false;
         File file = new File(filePath);  
         if (file == null || !file.exists()){  
             return false;  
         } 
	     return true;
     }
     /**
      * 依据内容判断是否为excel2003及以下
     */
     public static boolean isExcel2003(String filePath){
         try {
	         BufferedInputStream bis = new BufferedInputStream(new FileInputStream(filePath));
	         if(POIFSFileSystem.hasPOIFSHeader(bis)) {
	        	 System.out.println("Excel版本为excel2003及以下");
	        	 return true;
	         }
         } catch (IOException e) {
        	 e.printStackTrace();
        	 return false;
         }
         return false;
     }
     /**
      * 依据内容判断是否为excel2007及以上
     */
     public static boolean isExcel2007(String filePath){
         try {
	         BufferedInputStream bis = new BufferedInputStream(new FileInputStream(filePath));
	         if(POIXMLDocument.hasOOXMLHeader(bis)) {
	        	 System.out.println("Excel版本为excel2007及以上");
	        	 return true;
	         }
         } catch (IOException e) {
        	 e.printStackTrace();
        	 return false;
         }
         return false;
     }
}


```

添加`Excel`读取类:

```java

package util;

import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.InputStream;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

import org.apache.poi.hssf.usermodel.HSSFCell;
import org.apache.poi.hssf.usermodel.HSSFDateUtil;
import org.apache.poi.hssf.usermodel.HSSFWorkbook;
import org.apache.poi.ss.usermodel.Cell;
import org.apache.poi.ss.usermodel.Row;
import org.apache.poi.ss.usermodel.Sheet;
import org.apache.poi.ss.usermodel.Workbook;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
/**
 * 有判断性的读取
 * @author Administrator
 *
 */
public class ReadExcel {
	 /** 错误信息 */  
    private String errorInfo;
    
    /**
     * 验证EXCEL文件是否合法 
     */
    public boolean validateExcel(String filePath){ 
    
    	/**判断文件名是否为空或者文件是否存在 */
    	if(!CEVUtil.fileExist(filePath)){
    		errorInfo = "文件不存在";
    		return false; 
    	}
    
        /**检查文件是否是Excel格式的文件 */  
        if (!CEVUtil.isExcel(filePath))  {  
            errorInfo = "文件名不是excel格式";  
            return false;  
        } 
        return true;  
    }
    
    /** 
     * @描述：根据文件名读取excel文件 
     */  
    public Map<String,Object> read(String filePath){
    	Map<String,Object> map = new HashMap<String,Object>(); 
        InputStream is = null;  
        try{
            /** 验证文件是否合法 */  
            if (!validateExcel(filePath)){ 
                map.put("error", errorInfo);
                return map;
            }  
            /** 判断文件的类型，是2003还是2007 */  
            boolean isExcel2003 = true; 
            if (CEVUtil.isExcel2007(filePath)){ 
                isExcel2003 = false;  
            }  
            /** 调用本类提供的根据流读取的方法 */  
            is = new FileInputStream(new File(filePath));
            Workbook wb = null;  
            if (isExcel2003){  
                wb = new HSSFWorkbook(is);  
            }else{  
                wb = new XSSFWorkbook(is);  
            }
            map.put("wb", wb);
            is.close();
        }catch (IOException e){  
            e.printStackTrace(); 
        }catch (Exception ex){  
            ex.printStackTrace();  
        }finally{  
            if (is != null){  
                try{  
                    is.close();  
                }catch (IOException e){  
                    is = null;  
                    e.printStackTrace();  
                }  
            }  
        }  
        return map;  
    }
    
    /** 
     * @描述：读取数据 
     */  
 
    public List<List<Object>> readExcel(Workbook workbook) 
			throws  FileNotFoundException, IOException, org.apache.poi.openxml4j.exceptions.InvalidFormatException{
		//Workbook workbook = WorkbookFactory.create(new FileInputStream(new File(excelPath)));
    	//String values="";
    	Sheet sheet = workbook.getSheetAt(0);
		int startRowNum = sheet.getFirstRowNum();
		int endRowNum = sheet.getLastRowNum();
		List<List<Object>> dataLst = new ArrayList<List<Object>>();
		
		for(int rowNum = startRowNum;rowNum<=endRowNum;rowNum++){
			List<Object> rowLst = new ArrayList<Object>();  
			//values += "(";
			Row row = sheet.getRow(rowNum);
			if(row == null) continue;
			int startCellNum = row.getFirstCellNum();
			int endCellNum = row.getLastCellNum();
			for(int cellNum = startCellNum;cellNum<endCellNum;cellNum++){
				Cell cell = row.getCell(cellNum);
				if(cell == null) continue;
				int type = cell.getCellType(); 
				switch (type) {
					case Cell.CELL_TYPE_NUMERIC://数值、日期类型
						double d = cell.getNumericCellValue();
						if (HSSFDateUtil.isCellDateFormatted(cell)) {//日期类型
							// Date date = cell.getDateCellValue();
							Date date = HSSFDateUtil.getJavaDate(d);
							rowLst.add(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(date));
							//values +="'"+new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(date)+"',";
						}else{//数值类型          
							System.out.print(" "+d+" ");
							rowLst.add(d);
						}
						break;
					case Cell.CELL_TYPE_BLANK://空白单元格
						System.out.print(" null ");
						rowLst.add("");
						break;
					case Cell.CELL_TYPE_STRING://字符类型
						System.out.print(" "+cell.getStringCellValue()+" ");
						rowLst.add(cell.getStringCellValue());
						break;
					case Cell.CELL_TYPE_BOOLEAN://布尔类型
						System.out.println(cell.getBooleanCellValue());
						rowLst.add(cell.getBooleanCellValue());
						break;
		            case HSSFCell.CELL_TYPE_ERROR: // 故障  
		                System.err.println("非法字符");//非法字符;  
		                rowLst.add("非法字符");
		                break;
		            default:      
		            	System.err.println("error");//未知类型
		            	 rowLst.add("error");
		            	break;
				}
			}
			dataLst.add(rowLst);
			System.out.println();
		}
		return dataLst;
	}
    
   
}



```



对应的`Controller`类，`ExportExcelController`

为了防止用户上传的`Excel`格式不争取，额外添加了一个导出`Excel`模板方法。

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

	ExportExcelService exportExcelService = new ExportExcelService();
	/**
	 * 导出表格
	 */
	public void exportOutExcel(){
		String sheetName = getPara("sheetName");		
		
		String fileName = new Date().getTime() + "_" + UUID.randomUUID().toString() + ".xls";
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
	
	
	/**
	 * 导出表格模板
	 */
	public void exportOutExcelStandard(){
		String sheetName = getPara("sheetName");		
		
		String fileName = new Date().getTime() + "_" + UUID.randomUUID().toString() + ".xls";
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
		
		title.add("序号");
		title.add("id");
		title.add("caseId");
		title.add("imgId");
		
		xlsFileKit.addSheet(content, sheetName, title);
		xlsFileKit.save();
		renderJson(new Record().set("relativePath", relativePath));
	}
}

```

对应的`Services`类，`InportExcelService`


```java

package service;

import com.jfinal.plugin.activerecord.Db;

public class InportExcelService {

	public static void delAll(){
		
		Db.update("delete from zt_case_img");
	}
	
	public static void addSql(String sqlValues){
		
		String sql = "insert into zt_case_img"
				  + " (caseId,imgId)"
				  + " values " + sqlValues;
		Db.update(sql);
	}
}


```

然后前端相应的方法调用。


```js

//导出`Excel`模板
		$("#exportS").click(function() {
			$.post("excel/exportOutExcelStandard", {
				sheetName: "报表"
			}, function(data) {
				var relativePath = data.relativePath;
				console.log(relativePath);
				window.location.href = "." + relativePath;

			});

		});

		$('#inport').uploadify({
		'swf': 'js/uploadify/uploadify.swf',
		'buttonText': '导入Excel',
		'auto': false,
		'buttonClass': 'crud-btn import-btn',
		'width': '78',
		'height': '26',
		'fileTypeExts': '*.xls;*.xlsx',
		'uploader': 'inexcel/exportInExcel',
		'removeCompleted': true,
		'onSelect': function(file) {
			layer.confirm('请选择清空数据库插入，还是追加插入？', {
				btn: ['清空插入', '追加插入']
					//按钮
			}, function() {
				$("#inport").uploadify("settings", "uploader", "../../inexcel/exportInExcel?flag=1");
				$('#inport').uploadify('upload', '*');
			}, function() {
				$("#inport").uploadify("settings", "uploader", "../../inexcel/exportInExcel?flag=0");
				$('#inport').uploadify('upload', '*');
			});
		},
		'onUploadStart': function(file) {

		},
		'onUploadSuccess': function(file,
			data, respone) {
			var img = $.parseJSON(data);
			if(img.flag != "1") {
				alert(img.flag);
			} else {
				alert("导入成功！", function() {
					window.location
						.reload();
				});
			}
			$('#pic_upload').uploadify(
				'stop');
		}
		});

		});       

```

导入`Excel`:

![outPut](http://wx4.sinaimg.cn/mw690/c584f169ly1fbpdvetd4nj208a04aa9z.jpg)

导入`Excel`成功：

![outPut](http://wx3.sinaimg.cn/mw690/c584f169ly1fbpdvf9mlrj20cg04ugli.jpg)

导出`Excel`模板：

![outPut](http://wx2.sinaimg.cn/mw690/c584f169ly1fbpdve9yv9j209k058748.jpg)

[完整源代码](https://github.com/Chenjy1225/ChenjyDemo/tree/gh-pages/2016-JFinal_demo)