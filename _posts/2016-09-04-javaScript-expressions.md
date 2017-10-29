---
layout: post
title:  "JavaScript 正则表达式"
date:   2016-09-04 20:00:00 +0800
categories: JavaScript
tags: JavaScript
author: JiuYang Chen
---

* content
{:toc}




## 正则表达式

### 方括号

方括号用于查找某个范围内的字符：

* [abc]	查找方括号之间的任何字符。
* [^abc] 查找任何不在方括号之间的字符。
* [0-9]	查找任何从 0 至 9 的数字。
* [a-z]	查找任何从小写 a 到小写 z 的字符。
* [A-Z]	查找任何从大写 A 到大写 Z 的字符。
* [A-z]	查找任何从大写 A 到小写 z 的字符。
* [adgk] 查找给定集合内的任何字符。
* [^adgk]= 查找给定集合外的任何字符。
* `(red|blue|green)` 查找任何指定的选项。

### 元字符

* .  查找单个字符，除了换行和行结束符。
* \w 查找单词字符。
* \W 查找非单词字符。
* \d 查找数字。
* \D 查找非数字字符。
* \s 查找空白字符。
* \S 查找非空白字符。
* \b 匹配单词边界。
* \B 匹配非单词边界。
* \0 查找 NUL 字符。
* \n 查找换行符。
* \f 查找换页符。
* \r 查找回车符。
* \t 查找制表符。
* \v 查找垂直制表符。
* \xxx 查找以八进制数 xxx 规定的字符。
* \xdd 查找以十六进制数 dd 规定的字符。
* \uxxxx 查找以十六进制数 xxxx 规定的 Unicode 字符。

### 量词

* n+	匹配任何包含至少一个 n 的字符串。 等价于 {1,}
* n*	匹配任何包含零个或多个 n 的字符串。 等价于 {0,}
* n?	匹配任何包含零个或一个 n 的字符串。 等价于 {0,1}
* n{X}	匹配包含 X 个 n 的序列的字符串。
* n{X,Y}	匹配包含 X 或 Y 个 n 的序列的字符串。
* n{X,}	匹配包含至少 X 个 n 的序列的字符串。
* n$	匹配任何结尾为 n 的字符串。
* ^n	匹配任何开头为 n 的字符串。
* ?=n	匹配任何其后紧接指定字符串 n 的字符串。
* ?!n	匹配任何其后没有紧接指定字符串 n 的字符串。

### Constructor

#### RegExp的构造函数

```js

    var regExp = new RegExp(pattern, attributes); 
    
    //匹配所有"a"和"A"
    var regExp = new RegExp("a","i");

```

#### Main Properties

* `pattern`：字符串，指定了一个正则表达式模式或是其他正则表达式。

* `attributes`：可选字符串。

1. i ：大小写匹配
2. g : 全文搜索
3. m : 多行匹配

#### 正则表达式字面量的声明方式

使用`/` `/`将正则表达式包起来，`i``g``m`属性放在`/`后面

```js
    
    //匹配所有"a"和"A"
    var regExp = /a/i;

```

#### RegExp的双重转义

因为`\`同时是JavaScript和正则表达式的转义符，所以在`RegExp`对象中需要注意双重转义。
所以一般使用字面量的声明方式。

```js
    
    //1.使用RegExp的构造函数
    var regExp = new RegExp("\\s","i");
    
     //1.使用字面量的声明方式
    var regExp = \s\i;
    

```

### 正则表达式相关方法

* `match`找到一个或多个正则表达式的匹配。 

```js

     var str = "HELLO World";
     
     var regExp = /o/gi;
     
      //检索全文中所有"o","O"
     // outPut: O,o
     alert(str.match(regExp));
 
```

* `replace`替换与正则表达式匹配的子串。 

```js
 
    var str = "HELLO World";
       
     var regExp = /l/gi;
     
     //用"*"全文中所有"l","L"
     // outPut: HE**O Wor*d
     alert(str.replace(regExp,"*"));
 
```

* `search`检索与正则表达式相匹配的值。 

```js

     var str = "HELLO World";
       
     var regExp = /l/i;
     
     //检索出第一个和"l"或是"L"匹配的位置。起始位置为'0'
     // outPut: 2
     alert(str.search(regExp));
 
```

* `split`把字符串分割为字符串数组。 

```js

    var str = "HELLO World";
       
    var regExp = /l/i;
     
     //以"l"或是"L"为界限分割字符串
     // outPut: "HE","","O","wor","d"
     alert(str.split(regExp));
 
```

### 常用的正则表达式

#### 去除字符串首尾空格

```js
    
    var regExp = /(^\s*)|(\s*$)/g;
    
    str.replace(regExp, ""); 

```

* 正则表达式 `(^\s*)|(\s*$)` 全文搜索`g`
* `\s`查找空白字符
* `\s*`查找0个或多个空白字符
* `^\s*`查找开头0个或多个空白字符
* `(^\s*)` 匹配任何开头为一个或是多个空格的字符串
* `(\s*$)` 匹配任何结尾为一个或是多个空格的字符串
* 使用`replace()`替换为`""`即为删除。

`more...`
