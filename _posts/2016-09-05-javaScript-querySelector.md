---
layout: post
title:  "JavaScript 原生DOM选择器querySelector"
date:   2016-09-05 20:00:00 +0800
categories: JavaScript
tags: JavaScript
author: JiuYang Chen
---

* content
{:toc}




## querySelector

### 概述

return the first matching Element node within the node’s subtrees. If there is no such node, the method must return null .

返回指定元素节点的子树中匹配`selector`的集合中的第一个，如果没有匹配，返回`null`。

## querySelectorAll

### 概述

return a NodeList containing all of the matching Element nodes within the node’s subtrees, in document order. If there are no such nodes, the method must return an empty NodeList. 

返回指定元素节点的子树中匹配`selector`的节点集合，采用的是深度优先预查找；如果没有匹配的，这个方法返回空集合。

## 用法


```html

    <div class="test">
			<ul class="first-ul">
				<li>li1</li>
				<li>li2</li>
			</ul>
			<ul class="first-ul">
				<li>li1</li>
				<li>li2</li>
			</ul>
		</div>
   

```

```js
    
    // 选取`class`类名为`test`的子元素中第一个为`p`的元素
    document.querySelector("div.test>ul:first-child");
    
    document.querySelectorAll("div.test>ul:first-child")[0];


```

output:

```html

            <ul class="first-ul">
				<li>li1</li>
				<li>li2</li>
			</ul>
			
```