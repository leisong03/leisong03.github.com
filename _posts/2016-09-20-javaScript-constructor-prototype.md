---
layout: post
title:  "javaScript constructor和prototype"
date:   2016-09-20 20:00:00 +0800
categories: JavaScript
tags: JavaScript
author: JiuYang Chen
---

* content
{:toc}


本篇介绍JavaScript对象中的constructor和prototype。




## constructor和prototype


JS中函数定义：

```js
    
    function Person(name)   
    {   
       this.name=name;  
    };   
    
    var chen = new Person("chenjy")

```

### `类`

因为有关键字`new`可以实例对象所以函数`Person`可以当做是一个`类`。
定义的函数都有一个`prototype`的属性，但是使用`new`来创建的对象没有。

每个`prototype`的属性又指向一个`prototype`对象(`__ proto __`)，每个`prototype`对象(`__ proto __`)包含一个`constructor`的属性，
而这个属性又指向一个`constructor`对象，该对象就是这个函数。



### `原型继承`

当我们使用`new`实例对象`chen`,它指向`Person`的`prototype`对象(`__ proto __`)，然后`prototype`对象( `__ proto __`)中所有的属性和函数都可以调用。



### `原型链`



这样就可以形成一个`原型链` 

* chen.__ proto __  === Person.prototype

* person.prototype.__ proto __ === Object.prototype

* Object.prototype.__ proto __ === null (由于比较特殊)

![output](http://ww3.sinaimg.cn/mw690/c584f169jw1f8g543gko3j20v60m1dh6.jpg)


>  **Update In 2017/08/07**

先盗图一张

![output](http://wx4.sinaimg.cn/mw690/c584f169gy1fibebikbjzj20eg0gaabg.jpg)

## 原型链

### `_proto_`

在`JS`里，万物皆对象。方法`Function`是对象，方法的原型`Function.prototype`是对象。
对象具有属性 `_proto_ `[隐式原型] ,一个对象的隐式原型指向构造该对象的构造函数的原型。
保证了实例可以访问在构造函数原型中定义的属性和方法

`f1`、`f2`的`_proto_`指向构造该函数的构造函数`function Foo()`的原型`Foo.prototype`[显式原型]

`function Foo()`的`_proto_`执行构造该函数的构造函数`function Function()`的原型`Function.prototype`

`Foo.prototype`的`_proto_`执行构造该函数的构造函数`function Object()`的原型`object.prototype`

* `object.prototype` 的 `_proto_` 指向 `null`

* (object creat by Function) (Foo creat by Function)

### `prototype` & `constructor`

方法这个特殊的对象，除了和其他对象一样有上述`_proto_`属性之外，还有自己特有的属性——原型属性`prototype`

指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法（我们把这个对象叫做原型对象）。
原型对象也有一个属性，叫做`constructor`，这个属性包含了一个指针，指回原构造函数。

### `prototype` 使用

`prototype`主要用于继承。

```js

var Foo = function(name){

    this.name = name;
 
}

Foo.prototype.getName = function(){
    
	return this.name;
	
}

var foo = new Foo("chenjy");

foo.getName(); // chenjy

```


















