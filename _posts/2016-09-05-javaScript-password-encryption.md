---
layout: post
title:  "JavaScript 加密方法(Hash算法)"
date:   2016-09-05 22:00:00 +0800
categories: JavaScript
tags: JavaScript algorithm
author: JiuYang Chen
---

* content
{:toc}




## 原生的加密方式

### escape和unescape

如果是简单的加密我们可以使用JavaScript原生的方法`escape`和`unescape`。

`escape`方法不会对 ASCII 字母和数字进行编码，也不会对下面这些 ASCII 标点符号进行编码：` * @ - _ + . / `。其他所有的字符都会被转义序列替换。

```js

    console.log("output:" + escape("chenjy 1225! "));
    
    console.log("output:" + unescape(escape("chenjy 1225! ")));
	
	output:chenjy%201225%21%20
	
	output:chenjy 1225! 
	
```

### Hash算法

Hash算法特点：

* 很容易可以算出给的数值的散列数值
* 难以通过已知的散列数值推算原始信息
* 在不更改散列数值的基础上，无法更改消息内容
* 对于两条不同消息无法给出相同的散列数值

由于Hash算法的有以上特性所以常用在很多的重要应用例如数字签名，消息认证码。
	


### MD5算法

MD5是提供了一种`128bit``Hash值`的密码散列函数，

#### 算法原理

填充输入信息，使其字节长度对512求余数为448。信息的长度扩展为` N*512+448 bit N为整数`。
添加4个32位的链接变量

* A=0x01234567
* B=0x89abcdef
* C=0xfedcba98
* D=0x76543210

使其长度为`(N+1)*512 bit`，然后将每个`512bit`的组分为`16`个`32bit`子分组，最后经过一系列的算法生成`4`个`32bit`共`128bit`的散列值。

#### 算法使用

MD5js [MD5.js source code](http://pajhome.org.uk/crypt/md5/md5.html)

```js

    console.log(hex_md5("chenjy 1225!"));
    //basic-64编码
    console.log(b64_md5("chenjy 1225!"));
    
	output:6e065c650d8258f73bac5a3cd8f88f47
	
	output:bgZcZQ2CWPc7rFo82PiPRw

```

### SHA家族

SHA家族是一个密码散列函数家族，分别是`SHA-1`、`SHA-224`、`SHA-256`、`SHA-384`和`SHA-512`。
根据产生信息摘要的长度命名。

* SHA-1：160bit
* SHA-224：224bit
* SHA-256：256bit
* SHA-384：384bit
* SHA-512：512bit

#### 算法原理

原理和`MD5`类似，以SHA-1为例：

`SHA-1`能根据`2-64bit`的信息输入计算出`160bit`的散列值的单向散列函数。

填充数据的时候也是`512bit`为一组。在原始数据后先添加一个`1`然后添加`0`一直填满`448bit`。
然后再添加原始数据长度`64bit`,共为`512bit`。

同样分为为`16`个`32bit`子分组，最后经过一系列的算法生成`5`个`32bit`共`160bit`的散列值。

#### 算法使用

SHA-1.js [SHA-1.js source code](http://pajhome.org.uk/crypt/md5/sha1.html)

SHA-256.js [SHA-256.js source code](http://pajhome.org.uk/crypt/md5/sha256.html)

SHA-512.js [SHA-512.js source code](http://pajhome.org.uk/crypt/md5/sha512.html)

```js

    console.log(hex_sha1("chenjy 1225!"));
    
    console.log(hex_sha256("chenjy 1225!"));
    
    console.log(hex_sha512("chenjy 1225!"));
    
	output:a8a3b8b4263f7c12bbb400b6841aca472f53df0a
	
	output:1b4d37e8177634f191a7742c6d7e3d8aaf556aae583da0944e353001344fe3b0

    output:52622c1d8351011cb8ec1c4c891387d9a221c34e9e7289692070ef6656e98c4025328682cb86bd8577f6ed2b51c61559adb76685356632543db3e9020cc162f2
```

### Blizzard  `one way hash`


 ***
![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1f7jtnnkrp8j20dw0863zn.jpg)
 ***

据说是我大暴雪很经典的Hash算法。

Blizzard  one way hash [Blizzard](http://www.oschina.net/code/snippet_99767_1217)