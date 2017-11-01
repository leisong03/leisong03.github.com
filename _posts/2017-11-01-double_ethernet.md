---
layout: post
title:  "Solo主板如何接两个网卡"
date:   2017-10-29 07:00:00 +0800
categories: SLA
tags: SLA DLP SOLO 
author: Song Lei
---

* content
{:toc}

SOLO 是我们针对DLP应用出的一款主板，有三个USB口，所以肯定可以接3个USB网卡，目前可以确定的是一定要
接两个的。一个是接H3，另一个是接大屏幕。

接好之后，在cmd里面运行
```sh
#adb shell
```
能看到如下的输出
```sh
Z:\下载\adb>adb shell
root@astar-y3:/ # netcfg
netcfg
ip6tnl0  DOWN                                   0.0.0.0/0   0x00000080 00:00:00:
00:00:00
tunl0    DOWN                                   0.0.0.0/0   0x00000080 00:00:00:
00:00:00
sit0     DOWN                                   0.0.0.0/0   0x00000080 00:00:00:
00:00:00
gre0     DOWN                                   0.0.0.0/0   0x00000080 00:00:00:
00:00:00
eth0     UP                               192.168.199.3/24  0x00001043 00:e0:4c:
36:24:cf
eth1     UP                                192.168.99.4/24  0x00001043 00:e0:4c:
36:06:20
lo       UP                                   127.0.0.1/8   0x00000049 00:00:00:
00:00:00
root@astar-y3:/ #
```


SPI负责高速高实时的部分

IIC因为有半双工特性，可以用来保证传输的一致性

## BUG

直接上代码：

```js

    var objArray = ["1","2","3","4"];
    
    for(var i = 0; i < objArray.length; i++) {
        
        var mtlLoader = new THREE.MTLLoader();
			mtlLoader.setPath("obj/");
			mtlLoader.load("" + objArray[i] + ".mtl", function(materials) {

			materials.preload();

				var objLoader = new THREE.OBJLoader();
					objLoader.setMaterials(materials);
					objLoader.setPath("obj/");
					objLoader.load("" + objArray[i] + ".obj", function(object) {

				    scene.add(object);

					}, onProgress, onError);

				});
         
				}
```

最新版本的`three.js`中加载obj模型可以分为两步，第一步先加载mtl文件，提取mtl文件作为对象的材质。第二部加载obj文件，提取obj文件作为
对象的三维网格，然后创建对象。

当我们使用以上方式循环加载一组模型的时候。它会报错。

```
   
    obj/undefined.obj  NOT_FOUND   X4
   
```

*but i dont't know what happend!*

然后在加载obj文件的时候打印出i会发现此事的 `i=5` 数组越界了所以找不到文件。但是为什么mtl都被加载了呢？

then 我稍微学习了一下发现，由于文件的加载时异步的

第一次加载的时候`i=0`， mtl加载的时候是正常的，但是加载obj的时候由于是单线程的obj的加载被阻塞了,所以它只好等mtl加载完成将一个函数`objLoader.load(i)`放到线程池中。
但是由于是异步的，所以在加载第一个mtl加载完成时候循环早已结束。
所以`mtlLoader.load(i)` （此处`i=0`）结束的时候，要执行`objLoader.load(i)`的时候`i`早已为`5`，所以会出现`NOT_FOUND`。

## 解决方案

### 解决方案1

我发现只要在循环中加入这么一句：

```js
   
    for(var i = 0; i < objArray.length; i++) {
        
        var arr = array[i];
   
```

这句话在使用ajax的时候很常见，我一直天真的以为它仅仅是用来简化代码的。

### 解决方案2

可以将需要循环的封装成一个函数，然后外部循环调用。

```js
   
    for(var i = 0; i < objArray.length; i++) {
    
        loadObj(objArray[i]);
         
    }
    
    function loadObj(objName){
        
        //todo
       
    }
   
```