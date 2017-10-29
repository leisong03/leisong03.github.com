---
layout: post
title:  "Jquery Plugin Tips"
date:   2016-06-01 20:00:00 +0800
categories: jquery JavaScript
tags: jquery JavaScript
author: JiuYang Chen
---

* content
{:toc}





## layer.js

### 父页面和子页面传值

1.1 访问父页面值

```js  

// "id" 父页面元素
var parentId=parent.$("#id").val();

```

2.2 访问父页面方法

```js  

var parentMethod=parent.getMethod();

```

3.3 给父页面传值

```js  

    // "id" 父页面元素
    parent.$('#id').text('');
	

```

4.4 关闭弹出的子页面窗口

```js  

//获取窗口索引  

var index = parent.layer.getFrameIndex(window.name); 

//关闭弹出的子页面窗口  

parent.layer.close(index);

```

5.5 子页面调用父页面刷新

```js  

parent.location.reload();

```

## uploadify.js

### `error` `placeholder element`

这是因为input 元素必须有id，并且用id初始化uploadify函数。否则就报错。