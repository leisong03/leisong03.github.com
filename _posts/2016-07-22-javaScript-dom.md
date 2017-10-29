---
layout: post
title:  "JavaScript dom"
date:   2016-07-22 20:00:00 +0800
categories: JavaScript
tags: JavaScript
author: JiuYang Chen
---

* content
{:toc}



## dom

根据 DOM，HTML 文档中的每个成分都是一个节点。

  DOM 是这样规定的：
  
*    整个文档是一个文档节点
*    每个 HTML 标签是一个元素节点
*    包含在 HTML 元素中的文本是文本节点
*    每一个 HTML 属性是一个属性节点
*    注释属于注释节点

```js
    //获取dom元素
    var Id = document.getElementById("id");
    var Class = document.getElementsByName("class");
    var Div = document.getElementsByTagName("div");

    //改变HTML内容
    document.getElementById("id").innerHTML = "text";
    //改变HTML样式
    document.getElementById("id").style.color = "blue";
    //添加HTML事件
    <div id="id" onclick = "this.innerHTML = 'text' "></div>

    document.getElementById("id").onclick=function(){alert("event")};
    //创建新的元素节点
    var element = document.createElement("p");
    //创建新的文本节点
    var node = document.createTextNode("node");

    element.appendChild(node);

    element.removeChild(node);

```


## node 接口方法

* nodeName String  节点的名字；根据节点的类型而定义

* nodeValue String 节点的值；根据节点的类型而定义

* nodeType Number 节点的类型：常量值之一

* ownerDocument Document 指向这个节点所属的文档

* firstChild Node 指向在childNodes列表中的第一个节点

* lastChild Node 指向在childNodes列表中的最后一个节点

* childNodes NodeList 所有子节点的列表

* previousSibling Node 指向前一个兄弟节点；如果这个节点就是第一个兄弟节点，那么该值为null

* nextSibling Node 指向后一个兄弟节点；如果这个节点就是最后一个兄弟节点，那么该值为null

* hasChildNodes() Boolean 当childNodes包含一个或多个节点时，返回真

* attributes NamedNodeMap 包含了代表一个元素的特性的Attr对象；仅用于Element节点

* appendChild(node) Node 将node添加到childNodes的末尾

* removeChild(node) Node 从childNodes中删除node

* replaceChild(newnode, oldnode) Node 将childNodes中的oldnode替换成newnode

* insertBefore(newnode, refnode) Node 在childNodes中的refnode之前插入newnode(newnode, refnode)
