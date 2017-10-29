---
layout: post
title:  "大数组分时加载算法 timedChunk"
date:   2016-09-28 20:00:00 +0800
categories: JavaScript
tags: JavaScript
author: JiuYang Chen
---

* content
{:toc}


本篇介绍`高性能JavaScript`一书作者`NicholasC.Zakas`提出的大数组的分时加载算法`timedChunk`。





## 分时加载

分时加载的意义在于分批加载数据确保在数据加载完成后及时的更新UI界面确保用户体验的流畅性。

### chunk()


```js

function chunk(array, process, context){
    
    //克隆数组
    var items = array.concat();   
    
    //延时100ms执行
    setTimeout(function(){
        
        var item = items.shift();
        
        //执行`process`方法
        process.call(context, item);
  
        //如果`items`数组不为空，延迟100ms(提供100ms来更新UI界面)然后回调上方的`setTimeout`函数
        if (items.length > 0){
            setTimeout(arguments.callee, 100);
        }
    }, 100);
}




```

如果该方法的数组大小为100，执行延时为100ms则总耗时长约为10000ms也就是10s。(忽略`process`执行的时长)
100ms的延时意味着，你花了大部分时间来进行页面UI的更新而且执行`JavaScript`的时间甚至可能只是几ms或是不到`1ms`，而且事实上我们并不需要那么长时间。

经过研究发现延时可以降低至25ms,25ms是避免浏览器`setTimeout`出现问题(IE`setTimeout`最小延时15ms左右,也就是说你延时设为0也会在15ms左右的时候执行)。
但是如果你提供了25ms的延时，你要处理上面的数据仅仅大小为100的数组也需要耗时2500ms 2.5s也是很长的一段时间。


### timedChunk()

所以`NicholasC.Zakas`改进了 `chunk()`

`Jakob Nielsen`指出`JavaScript`代码执行时间如果超过100ms持续执行则不能实时的更新页面UI。


`NicholasC.Zakas`在`Jakob Nielsen`的基础上认为`JavaScript`代码执行时间不应该连续的超过50ms(可以保证流畅的更新UI界面)。


改进算法

```js

//Copyright 2009 Nicholas C. Zakas. All rights reserved.
//MIT Licensed
function timedChunk(items, process, context, callback){
    
    //克隆数组
    var todo = items.concat(); 

    //延时25ms执行
    setTimeout(function(){

        var start = +new Date();
        
        //执行50ms的`process`方法(前提数组足够大)
        do {
             process.call(context, todo.shift());
        } while (todo.length > 0 && (+new Date() - start < 50));
        
        
        //延迟25ms(提供25ms来更新UI界面)然后回调上方的`setTimeout`方法
        if (todo.length > 0){
            setTimeout(arguments.callee, 25);
        } else {
            callback(items);
        }
    }, 25);
}

```

所以保证了`JavaScript`的执行时间的是50ms。
每执行50ms`JavaScript`就为UI界面的更新提供了25ms的时间渲染。


参照：

*https://threejs.org/examples/?q=sand#raytracing_sandbox*

空口白牙

[demo](https://chenjy1225.github.io/source/JavaScript-timedChunk.html)






