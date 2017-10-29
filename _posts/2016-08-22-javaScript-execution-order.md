---
layout: post
title:  "客户端 JavaScript 程序"
date:   2016-08-22 20:00:00 +0800
categories: JavaScript
tags: JavaScript 线程 编译
author: JiuYang Chen
---

* content
{:toc}

JavaScript犀牛书学习笔记



## 客户端JavaScript

### 客户端的JavaScript程序包括

* 内联脚本 

```js

    <script>  ...   </script>

```

* HTML 事件处理程序

```js

    <button  value="btn" onclick='Message()'></button>
    
    function Message(){
		}

```

* URL引用的外部JavaScript

```js

    <script type="text/javascript" src="js/Three/three.js"></script> 

```

他们拥有相同的document对象，可以共享全局变量和函数的集合。

tips：如果包含<iframe>元素，则该元素有单独的全局变量和函数。


### 执行的顺序

一般分为两步

* step1 载入文档内容，顺序加载出现的所有JavaScript程序。

* step2 在所有文档加载后执行，是异步的并且是事件驱动的

在此阶段它会监听所有的事件（如：鼠标单击或是键盘按键等）

在此阶段执行的第一个事件为`onload()`表示文档已经完全加载了，通常我们会把自定义的事件放在`onload()`中确保所有元素已经加载完成

在`js`中

```js

    window.onload = function(){
       
       //todo
       
    } 

```

在`jquery`中

jquery中使用方法`ready`代替`onload`方法

```js

    $(document).ready(function(){
    
       //todo
       
    }

```

此后一直处于事件驱动阶段，知道被用户或是网络任务中断。
但是同一时间只能执行一个事件或是脚本。

### 事件驱动

事件`冒泡`指的是当元素上注册的事件没有被处理，事件就会冒泡到嵌套该元素的容器元素。

我们可以使用方法`preventDefault`来阻止事件的冒泡。

```js

		function(event){
		  event.preventDefault();
		}

```

### 线程机制

JavaScript是单线程的，没有任何的线程机制。不会发生两个事件在同一时间发生，后续的认为需要排队，排在队列后面。它确保了程序的简单性，提高效率。
但是很多时候CPU是空闲的，因为它要等待IO设备返回结果（这个过程是很慢的）。
所以这时候完全可以不管IO设备挂起等待的任务，执行在队列后面的任务。当IO设备返回结果了继续执行挂起的任务。

所以将任务分为同步任务和异步任务。
所以整个过程可以分为三步。
* step1 主线程执行所有同步任务，形成一个`栈`
* step2 主线程之外还有一个`任务队列`，只要异步任务有结果就会在队列中放置一个`事件`
* step3 主线程的`栈`执行完以后就读取`任务队列`看有什么对应的事件

主线程不断的循环这三个步骤。

主线程从`任务队列`中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为`Event Loop（事件循环）`。

### 解析机制

JavaScript的解析过程也分成两个阶段。

一个阶段是编译阶段，另一个是执行阶段。

#### 编译阶段

编译阶段也是常说的预处理阶段，JavaScript解释器将JavaScript代码转成字节码。

* var , function声明的变量提升
首先会定义所有 关键词`var`声明的变量并将其赋值为`undefined`，`function`定义的函数也会被声明。

* 函数表达式
函数表达式不同于关键词`var``function`声明的变量和函数。
函数表达式用`var`声明，解析器会将它变量提升赋值为`undefined`然后执行`undefined = function(){}`会报错。

```js

   //b()会报错，a()不会
   
    a();
    function a(){};
    
    b();
    var b = function(){};
  

```

* 函数覆盖
在预解析阶段同名函数后面的函数会将前面的覆盖。

* 变量或函数解析到其运行时的范围中

* 预解析过程分段进行
按照`<script>`标签来分块

#### 执行阶段

执行阶段将字节码生成机械码然后顺序执行。



















