---
layout: post
title:  "百度地图逆地址解析"
date:   2016-12-08 19:00:00 +0800
categories: Java JavaScript
tags: Java JavaScript fastjson httpclient
author: JiuYang Chen
---

* content
{:toc}

本篇使用`Geocoding API`逆地址解析经纬度，并且通过`fastjson`获取地址。



## `Geocoding API`

Geocoding API 是一类接口，用于提供从地址到经纬度坐标或者从经纬度坐标到地址的转换服务，用户可以使用C# 、C++、Java等开发语言发送请求且接收JSON、XML的返回数据。

*http://lbsyun.baidu.com/index.php?title=webapi/guide/webservice-geocoding*

简单的说就是通过向`http://api.map.baidu.com/geocoder/v2/`发送一个`HTTP/HTTPS`请求然后获取返回的数据。

请求示例：

`http://api.map.baidu.com/geocoder/v2/?callback=renderReverse&location=39.915,116.404&output=json&pois=1&ak=你的ak密钥`  

Tips:ak密钥需要登录百度账号申请。

### `JavaScipt`调用

由于`ajax`跨域问题这里使用了`dataType: 'JSONP'`。

```js

<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title></title>
	</head>
	<body>
		<button>提交</button>
	</body>
	<script type="text/javascript" src="js/jquery-1.9.1.js"></script>
	<script>
		$(document).ready(function() {
			$("button").click(function() {
				$.ajax({
					type: "GET",
					url: "http://api.map.baidu.com/geocoder/v2/?callback=renderReverse&location=39.915,116.404&output=json&pois=1&ak=你的ak密钥",					
					dataType: 'JSONP',
					success: function(data) {
						var list = data.result.addressComponent;
						console.log(data);
						console.log(list.country+""+list.province+""+list.district+""+list.street);
					}
				})
			});

		});
	</script>
</html>

```

output:

![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1faktepn1u8j20do0bgmxj.jpg)

### `Java`调用

这里使用`fastjson`来解析。

```java

package HttpRequest;

// 导入`fastjson`
import com.alibaba.fastjson.JSONObject;
// 封装的`HttpRequest`类
import HttpRequest.HttpRequest;

public class BaiduMapRequset {

	public static void main(String[] args) {

		String responseData = HttpRequest.sendGet("http://api.map.baidu.com/geocoder/v2/",
				"callback=renderReverse&location=39.915,116.404&output=json&pois=1&ak=你的ak密钥");
		
		//解析返回的`Json`
		int start = responseData.indexOf("(") + 1;
		responseData = responseData.substring(start, responseData.lastIndexOf(")"));
		
		//获取返回的`city`值
		JSONObject jsonobject = new JSONObject(responseData);  
		System.out.println(jsonobject.parseObject(responseData).getJSONObject("result").getJSONObject("addressComponent").get("city"));
		

	}

}

```


output:

```java
北京市
```

由于返回的值为`Gson`:renderReverse&&renderReverse({"status":0, ...... "cityCode":131}}),所以需要简单的解析一下。

[源代码](https://github.com/Chenjy1225/ChenjyDemo/tree/gh-pages/Chenjy1225)

### 使用`HttpClient`封装请求

`HttpClient`是`Apache Jakarta Common`下的子项目，用来提供高效的、最新的、功能丰富的支持`HTTP`协议的客户端编程工具包。

Maven地址：

```xml

<!-- https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient -->
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
    <version>4.5.2</version>
</dependency>

```

```java

package HttpClient;

import java.io.IOException;
import java.net.URI;
import java.net.URISyntaxException;

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

import com.alibaba.fastjson.JSONObject;

public class HttpClient {

	public static void main(String[] args) throws IOException {

		try {
			
		CloseableHttpClient httpclient = HttpClients.createDefault();
			URI uri;
			uri = new URIBuilder()
					    .setScheme("http")
					    .setHost("api.map.baidu.com")
					    .setPath("/geocoder/v2/")
					    .setParameter("callback", "renderOption")
					    .setParameter("output", "json")
					    .setParameter("address", "惠山区")
					    .setParameter("ak", "ONYb7QVTnolEPPGQhBsX2jG2kX3ki9yq")
					    .build();
		

        // 创建httpget.  
		HttpGet httpget = new HttpGet(uri);
		    
		CloseableHttpResponse response = httpclient.execute(httpget);

		System.out.println(response);
		 try {
			 HttpEntity entity = response.getEntity(); 
			 
			 System.out.println(entity);
			 
			 if (entity != null) {   
                 // 打印响应内容长度    
                 System.out.println("Response content length: " + entity.getContentLength());  
                 // 打印响应内容    
                 System.out.println("Response content: " + EntityUtils.toString(entity));  
                 
             }  
			 
		    } finally {
		        response.close();
		    }
		

		 
		} catch (URISyntaxException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

	}
}


```



















