---
layout: post
title:  "JavaScript原生css类操作方法"
date:   2016-09-04 23:00:00 +0800
categories: JavaScript
tags: JavaScript
author: JiuYang Chen
---

* content
{:toc}

[You Don't Need jQuery!](http://blog.garstasio.com/you-dont-need-jquery/)

使用原生的`JavaScript`实现`has`、`add`、`remove`和`toggleClass`




## hasClass

判断元素是否包含类名

因为出现的情况可能有`className`，`className otherName`，`otherName className`，`other-className`，`className-other`和`other-className-Name`

所以起始位置要`匹配空格`或是直接`匹配你需要的classname`

```js

    \s|^classname

```

同理结束位置`匹配空格`或是直接`匹配你需要的classname`即可


```js

    classname\s|$

```


> hasClass(element, classname) 传入一个`元素`和要匹配的`class类名`

```js

    function hasClass(element, classname) {

		 if(element.className.match(new RegExp('(\\s|^)' + classname + '(\\s|$)')) != null) {

			return true;
		 } else {
			return false;
		 }

	}

```

## addClass

给元素添加class类名

> hasClass(element, classname) 传入一个`元素`和要添加的`class类名`


```js

    function addClass(element, classname) {
         
         element.className += " " + classname;
         
	}

```

## removeClass

移出元素的类名

> removeClass(element, classname) 传入一个`元素`和要移出的`class类名`

```js

    function removeClass(element, classname) {

		element.className = element.className.replace(new RegExp('(\\s|^)' + classname + '(\\s|$)'), ' ');
         
	}

```

## toggleClass

判断是否有类名如果有则移出如果没有则添加

> removeClass(element, classname) 传入一个`元素`和要判断的`class类名`


```js

    function toggleClass(element, classname) {

		if(hasClass(element, classname)){
		
		   removeClass(element, classname);
		   
		}else{
		
		  addClass(element, classname);
		    
		}
         
	}

```

## View source


*[source code](https://github.com/Chenjy1225/ChenjyDemo/blob/gh-pages/js/noneed_jquery.js)*