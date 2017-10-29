---
layout: post
title:  "three.js-shadow"
date:   2016-08-09 22:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇介绍一下阴影的生成。



## 渲染器设置

要生成阴影需要将渲染器的`shadowMap.enabled`开启。
设置`shadowMap`的样式

```js

    render.shadowMapEnabled = true;
    
    // 0: THREE.BasicShadowMap, 1: THREE.PCFShadowMap, 2: THREE.PCFSoftShadowMap
    render.shadowMap = 0;  

```


## 添加光源

这里的光源需要是能生成阴影的光源。比如说是`SpotLight`、`DirectinalLight`或是其他一下有`castShadow` 属性的高级光源。
`AmbientLight`和`PointLight`则不能产生阴影。这里以`SpotLight`为例。

```js

    var spotLight = new THREE.SpotLight(0xffffff);  
    spotLight.position.set(10, 20, 10);  
    
    //开启castShadow生成动态的投影
    spotLight.castShadow = true;  
    
    scene.add(spotLight);  

```

## 添加物体

要产生阴影的对象castShadow属性设为true。
接收阴影的对象receiveShadow属性设为true。

```js
 
    var geometry = new THREE.CubeGeometry(40, 1, 40);  
    var material = new THREE.MeshLambertMaterial({  
                color: 0xffffff  
            });  
            
    //接收阴影的对象            
    var plane = new THREE.Mesh(geometry, material);  
    
        plane.receiveShadow = true;  
        
        scene.add(plane);  
        
    //产生阴影的对象                   
    var object = new THREE.Mesh(geometry, material);  
    
        object.castShadow = true;  
        
        scene.add(object);     
 
```
 
 ***
![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f6pjygp8kwj20g809tmx8.jpg)
 
## View source
 
*[source code](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-shadow.html)*
