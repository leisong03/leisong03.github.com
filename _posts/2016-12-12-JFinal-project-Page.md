---
layout: post
title:  "Jfinal 列表分页"
date:   2016-12-12 19:00:00 +0800
categories: Java
tags: Java JFinal
author: JiuYang Chen
---

* content
{:toc}

本篇介绍`JFinal`项目的列表分页的功能实现。



## `JFinal`列表分页

### 添加`Page`类

在前篇JFinal项目的基础上。新建一个`Page`类,提供一些`Page`的操作。

```java

/// <summary>
///  Page类
/// </summary>

public class Page {

/// <param name="pageSize">页面大小</param>
/// <param name="pageIndex">页面索引</param>
/// <param name="pageCount">页面数量</param>
/// <param name="count">List数量</param>

    public static int pageSize = 9;
    private int pageIndex;
    private int pageCount;
    private int count;

    public Page(int pageIndex,int count){

       if(count%pageSize==0){
           this.pageCount = count/pageSize;
       }else{
           this.pageCount = count/pageSize+1;
       }
       if(pageIndex>pageCount){
           pageIndex = pageCount;
       }
       if(pageIndex<1){
           pageIndex = 1;
       }
       this.pageIndex = pageIndex;
       this.count = count;

    }

    public Page(int pageSize,int pageIndex,int count){

       this.pageSize = pageSize;
       if(count%pageSize==0){
           this.pageCount = count/pageSize;
       }else{
           this.pageCount = count/pageSize+1;
       }
       if(pageIndex>pageCount){
           pageIndex = pageCount;
       }
       if(pageIndex<1){
           pageIndex = 1;
       }
       this.pageIndex = pageIndex;
       this.count = count;

    }

    public int getPageSize() {
       return pageSize;
    }

    public void setPageSize(int pageSize) {
       this.pageSize = pageSize;
    }

    public int getPageIndex() {
       return pageIndex;
    }

    public void setPageIndex(int pageIndex) {
       this.pageIndex = pageIndex;
    }

    public int getPageCount() {
       return pageCount;
    }

    public void setPageCount(int pageCount) {
       this.pageCount = pageCount;
    }

    public int getCount() {
       return count;
    }

    public void setCount(int count) {
       this.count = count;
    }

}

```

### 添加接口,获取`List`

在原`HelloController`基础上添加方法`IofoList`.

`HelloService.java`:

```java

 static int count = 0;
 
    
     /// <summary>
	 ///  获取总数量
	 /// </summary>

	public static int getCount(){
		
		String sql="select count(*) as count from dbName";
		
	    count = Integer.parseInt(Db.findFirst(sql).getLong("count").toString());
	    
	    return count;
	}
	
    /// <summary>
	///  获取当前页面列表
	/// </summary>	  
	public static List<Record> get(Page page) {

	       List<Record> list = null;
	       
	       int start = (page.getPageIndex() - 1) * page.getPageSize();
	       
	       int pageSize = page.getPageSize();

	       String sql = "select * from dbName limit "+start+","+pageSize+" ";
	       
	       list = Db.find(sql);
	       return list;
	    }

```

`HelloController.java`:

```java

	public void IofoList(){

		// 获取页面参数  < pageIndex >页面索引值
		int pageIndex = getParaToInt("pageIndex");

		// 获取总数量
		int count = HelloService.getCount();

		Page page = new Page(Page.pageSize, pageIndex, count);
		
		List<Record> list = HelloService.get(page);
		Record pageInfo = new Record();

		pageInfo.set("count", page.getCount()).set("pageIndex", page.getPageIndex()).set("pageCount", page.getPageCount());

		// 返回 JSON  <list>当前页面的list;
		// <pageInfo>页面信息: <count>list总数,<pageCount>页面总数,<pageIndex>当前页面索引 
		renderJson(new Record().set("list", list).set("pageInfo", pageInfo));

		}

```


接口传来的数据：

![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1fazx08xxd1j208x08njrd.jpg)

### 调用接口

`page.html`:

```html

<body>
	<div class="table-inner">
		<div class="table-head">
			<table>
				<thead>
					<tr>
						<th>List1</th>
						<th>List2</th>
						<th>List3</th>
					</tr>
				</thead>
			</table>
		</div>
		<div class="table-body">
			<table>
				<tbody id="tbody">
				<tr>
					<td>1</td>
					<td>2</td>
					<td>3</td>
				</tr>
				</tbody>
			</table>
		</div>
		<div class="page-wrap">
			<div class="page-info">
				<span>共有:</span><span id="listCount">1</span><span>条</span> 
				<span>共有:</span><span id="pageCount">1</span><span>页</span> 
				<span>页面显示:</span><span id="currentCount">1</span> <span>条</span> 
				<span class="current " id="firstPage"><<</span> 
				<span class="current " id="PreviousPage"><</span> 
				<span class="current " id="pageIndex">1</span> 
				<span class="current " id="nextPage">></span>
				<span class="current " id="lastPage">>></span> 
				<input type="text" id="go_index" />
				<span class="current"  id="go">GO</span>
			</div>
		</div>
	</div>
</body>

```

`page.js`:


```js

<script type="text/javascript">

// 存页面索引 及总页数
var pageIndex = 1,pageCount;

$(document).ready(function(){
	
	Page();

	//首页
	$("#firstPage").click(function(){
		
		pageIndex = 1;

		Page();
		
	});
	
	//上一页
    $("#PreviousPage").click(function(){
    	
    	pageIndex = pageIndex - 1;
		if(pageIndex == 0){
			pageIndex = 1;
		}

		Page();
		
	});

    //下一页
    $("#nextPage").click(function(){
    	
    	pageIndex = pageIndex + 1;
		if(pageIndex >= pageCount){
			pageIndex = pageCount;
		}

		Page();
    });
	
    //尾页
    $("#lastPage").click(function(){
		
    	pageIndex = pageCount;

    	Page();
		
		
	});
    
    // Go
    $("#go").click(function(){
    	
    	var _index = $("#go_index").val().trim();
    	
        if( _index > 0 && _index <= pageCount){

        	pageIndex = _index;
        	
        }else {
        	
        	alert("请输入正确页码");
        	
        }
        Page();
		$("#go_index").val("");
    	
    });
	
});

function Page(){
	
	$.post("hello/IofoList",{pageIndex:pageIndex},function(data){
		console.log(data);
		var _html = "";
		var _list = data.list;
		
		if( _list.length != 0 && _list.length != null){
			
		for(var _i =0 ;_i<_list.length;_i++){
			
			var _list_ = _list[_i];
			
			_html += "<tr><td>"+(_list_.id == null?"":_list_.id)+"</td>"+
					 "<td>"+(_list_.caseId == null?"":_list_.caseId)+"</td>"+
					 "<td>"+(_list_.imgId == null?"":_list_.imgId)+"</td></tr>";
			
		}
		
		$("#tbody").empty();
		$("#tbody").append(_html);
		
		var _page = data.pageInfo;

		pageCount = _page.pageCount;
		
		$("#listCount").html(_page.count);
		$("#pageCount").html(pageCount);
		$("#currentCount").html(_list.length);
		$("#pageIndex").html(pageIndex);
		
		}else{
			alert("暂无数据");
		}
		
	});
	
}

</script>

```


![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1fazx072sfej20se0mnglz.jpg)

点击尾页:

![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1fazx07o67aj20sl0mrjrn.jpg)


[源代码](https://github.com/Chenjy1225/ChenjyDemo/tree/gh-pages/JFinal_demo_page)