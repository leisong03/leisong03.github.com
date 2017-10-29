---
layout: post
title:  "Jquery Tips"
date:   2016-06-01 20:00:00 +0800
categories: jquery JavaScript
tags: jquery JavaScript
author: JiuYang Chen
---

* content
{:toc}





## Tips About Jquery.

### `Jquery`动态绑定事件


```js

//Jquery绑定事件

$('.div').click(function(){});

//Jquery动态绑定事件

$('.div').on('click',function(){});

<-- 当页面动态刷新时，新加载的元素依然可以绑定事件 -->

$(document).on('click','.div',function(){});

```

### `Jquery` `on`绑定`hover`事件

不能用`on`处理`hover`事件，因为`Jquery`的`hover`事件是一个封装的事件，不是真正的事件。

所以使用`mouseenter`和`mouseleave`来代替鼠标悬浮和离开事件。

```js

$(document).on('mouseenter', '.div', function() {
});

$(document).on('mouseleave', '.div', function() {
});


```

### `Jquery`获取时间并且格式化

```js

Date.prototype.format = function(format) {
    /*
     * eg:format="YYYY-MM-dd hh:mm:ss";

     */
    var o = {
        "M+" :this.getMonth() + 1, // month
        "d+" :this.getDate(), // day
        "h+" :this.getHours(), // hour
        "m+" :this.getMinutes(), // minute
        "s+" :this.getSeconds(), // second
        "q+" :Math.floor((this.getMonth() + 3) / 3), // quarter
        "S" :this.getMilliseconds()
    // millisecond
    }
    if (/(y+)/.test(format)) {
        format = format.replace(RegExp.$1, (this.getFullYear() + "")
                .substr(4 - RegExp.$1.length));
    }
    for ( var k in o) {
        if (new RegExp("(" + k + ")").test(format)) {
            format = format.replace(RegExp.$1, RegExp.$1.length == 1 ? o[k]
                    : ("00" + o[k]).substr(("" + o[k]).length));
        }
    }
    return format;
}

var startTime = new Date().format("yyyy-MM-dd 00:00:00");
var endTime = new Date().format("yyyy-MM-dd hh:mm:ss");

```

### 滚动条滚动底部

* `scrollTop([val])` 获取匹配元素相对滚动条顶部的偏移。

* `scrollLeft([val])` 获取匹配元素相对滚动条左侧的偏移。

* `scrollHeight` 滚动条高度

```js

$(".div").scrollTop( $(".div")[0].scrollHeight);


```

### `Jquery` `size( )`和`length`

`size()`是`jQuery` 提供的函数，而 `length`是属性。两者的取值是一样的。

### 页面之间传值

```js

   //页面一
   location.href = "href2.html?id=3";

```

```js   
   //页面二
   var _url = document.URL;
   var _urlParam = _url.split('?')[1];
   var _value = _urlParam.split('=')[1];

```
 
 也可以使用`jquery.params.js` `$.query.get("id");`
 
 ### 灵活运用三目运算符
 
```js   
 
 (_list.equipStatus ==1?"运行中":(_list.repairStatus ==2?"维修中":"待确认"))
 
```

### 正确引用jQuery

1.	尽量在`body`结束前才引入`jQuery`，而不是在`head`中。
2.	借助第三方提供的CDN来引入jQuery，同时注意当使用第三方CDN出现问题时，要引入本地的`jQuery`文件。
3.	<u>如果在`</body>`前引入`script`文件的话，就不用写`document.ready`了</u>，因为这时执行`js`代码时`DOM`已经加载完毕了。

```js   

<body>  
    <script src="http://lib.sinaapp.com/js/jquery11/1.8/jquery.min.js"></script>  
    <script>window.jQuery  document.write('<script src="jquery1.8.min.js">\x3C/script>')</script>  
</body> 

```

### 优化jQuery选择器

```js   

<div id="nav" class="nav">  
    <a class="home" href="http://www.jquery001.com">jQuery学习网</a>  
    <a class="articles" href="http://www.jquery001.com/articles/">jQuery教程</a>  
</div>

```

如果我们选择class为home的a元素时，可以使用下边代码：

$('.home');  //1  

$('#nav a.home');  //2  

$('#nav').find('a.home');  //3 

1.	方法1，会使jQuery在整个DOM中查找class为home的a元素，性能可想而知。
2.	方法2，为要查找的元素添加了上下文，在这里变为查找id为nav的子元素，查找性能得到了很大提升。
3.	方法3，使用了find方法，它的速度更快，所以方法三最好。

关于jQuery选择器的性能优先级，ID选择器快于元素选择器，元素选择器快于class选择器。因为ID选择器和元素选择器是原生的`JavaScript`操作，而类选择器不是。


### 缓存jQuery对象

缓存jQuery对象可以减少不必要的DOM查找，关于这点大家可以参考下缓存jQuery对象来提高性能。

```js  

// 糟糕
h = $('#element').height();
$('#element').css('height',h-20);

// 建议
$element = $('#element');
h = $element.height();
$element.css('height',h-20);

```

####  使用子查询缓存的父元素
正如前面所提到的，DOM遍历是一项昂贵的操作。典型做法是缓存父元素并在选择子元素时重用这些缓存元素。

```js  

// 糟糕
var
    $container = $('#container'),
    $containerLi = $('#container li'),
    $containerLiSpan = $('#container li span');
// 建议 (高效)
var
    $container = $('#container '),
    $containerLi = $container.find('li'),
    $containerLiSpan= $containerLi.find('span');

```

### 精简jQuery代码

一般来说,最好尽可能合并函数。

```js  

// 糟糕
$first.click(function(){
    $first.css('border','1px solid red');
    $first.css('color','blue');
});
// 建议
$first.on('click',function(){
    $first.css({
        'border':'1px solid red',
        'color':'blue'
    });
});

```

### 减少DOM操作

#### 最小化DOM更新

重布局和重绘是WEB页面中最常见的也是最昂贵的两种操作。
当改变样式，而不改变页面几何布局时，将会发生重绘。隐藏一个元素或者改变一个元素的背景色时都将导致一次重绘。
当对页面结构进行更新时，将导致页面重布局。

```js  

	//糟糕
	for(var i=0; i<10000; i++){
	$("#main table").append("<tr><td>aaaa</td></tr>");
	}
	//建议
	var tablerow = "";
	for(var i=0; i<10000; i++){
	tablerow  += "<tr><td>aaaa</td></tr>";
	}
$("#main table").append(tablerow);

```

### prop和attr方法

对于元素自身的固有属性 使用 prop 对于自定义的属性使用attr方法

例：获取选中的checkbox的value值

```js  

	$("input[type=checkbox]").each(function() {

					if(true == $(this).prop("checked")) {
					
						alert($(this).attr("value"));
						
					}
				});

```

### each遍历

用`each`实现全选或是取消全选。

```js  

	$("#selectAll").click(function() {
	
				if($("#selectAll").prop("checked")) {
					$("#selectAll input[type=checkbox]").each(function() {
						$(this).prop("checked", "true");
					});
					
				} else {
				
					$("#selectAll input[type=checkbox]").each(function() {
						$(this).removeAttr("checked");
					});
				}
			});


```






