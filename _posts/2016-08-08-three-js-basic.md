---
layout: post
title:  "three.js-Basic"
date:   2016-08-08 12:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇为基础部分(学习如何快速的创建一个三维场景)



## three.js是什么

> Three.js是一个`3D` `JavaScript`库。

## three.js能干什么

* 创建简单或是比较复杂的三维图形并应用丰富多彩的纹理和材质。
* 添加五光十色的光源。
* 在3D场景中移动物体或是添加脚本动画。
* 从三维建模软件(3dmax,maya..)中加载三维模型。
* 定制着色器和后期渲染的处理。

![whatThree](http://ww2.sinaimg.cn/mw690/c584f169gw1f6mdsnocdjj20q50fonmp.jpg)

*learn more from `http://threejs.org/`*

## 为什么要用three.js

Three.js为我们封装了底层的`WebGl接口`，使我们在无需掌握繁冗的图形学知识的基础下可以轻松的创建三维场景。相比较使用底层的WebGL我们可以使用更少的代码，大大的降低了学习成本，使开发变的更高效。

## 如何使用three.js

### 新建HTML页面

首先新建一个HTML页面，引入Three.js文件。

```js
<!DOCTYPE html>  
<html>  

    <head>  
        <meta charset="UTF-8"> 
        <title>three.js</title> 
    </head> 
    <body> 
        <script type="text/javascript" src="js/Three/three.js"></script> 
        <script> 
          
        </script> 
    </body> 
  
</html>  

```

### 创建一个三维场景

```js
    var scene = new THREE.Scene();
```

### 添加一个渲染器

Three.js为我们提供了如：canvas,SVG,CSS3..众多渲染器，但是WebGL渲染器相较比较灵活，所以均以WebGL为例。

```js
    var render = new THREE. THREE.WebGLRenderer({  
                    //抗锯齿属性，WebGLRenderer常用的一个属性
                    antialias: true  
                });  
                
        //设置背景色为白色(0xffffff)
        render.setClearColor(0xffffff);  
        
        //设置渲染尺寸为页面大小
        render.setSize(window.innerWidth, window.innerHeight); 
        
        //使用WebGLRenderer生成canvas元素。
        document.body.appendChild(render.domElement);  
   
```

> 当然也可以自己定义一个`canvas`
>
> `canvas <canvas id="myCanvas" width="400px" height="300px" ></canvas>`
>
> 并将WebGLRenderer定义改为
>
> `var render = new THREE.WebGLRenderer({`  
>             `canvas: document.getElementById('myCanvas'),`  
>             `antialias: true`  
>                 `});`  




### 三维场景中重要的组件

三维场景中重要的组件包括 `camera`,`light`,`object`。

three.js使用的`右手坐标系`。

![rightAxis](http://ww3.sinaimg.cn/mw690/c584f169gw1f6mecd1d28j20ci0augll.jpg)


#### camera

camera(相机)，决定了你开始看到三维场景的位置，朝向和角度等信息。

```js
    var camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);  
   
       //设置相机朝向位置为(20,0,20) 
        camera.lookAt(new THREE.Vector3(20, 0, 20));  

```

#### light

light(光源)，决定了你的场景拥有的光源类型和会产生的光照效果。

```js
    var light = new THREE.AmbientLight(0xffffff);
    
        //使用 Scene()的add方法将light添加到Scene中  
        scene.add(light);
```

#### object

object(物体)，你想要添加到场景中的各式各样的物体。

```js
    //物体三维图形
    var geometry = new THREE.CubeGeometry(4, 4, 4); 
    
    //物体材质 
    var material = new THREE.MeshBasicMaterial({  
                    color: 0x4d6dad  
                });  
                
    //生成三维物体
    var mesh = new THREE.Mesh(geometry, material);  
    
        mesh.position.set(10, 0, 10);  
        
        //使用 Scene()的add方法将mesh添加到Scene中
        scene.add(mesh);  
```

### 渲染场景

定时刷新场景，调用WebGLRenderer的render函数刷新场景。

```js
    function render() {
 
            requestAnimationFrame(render);
            renderer.render(scene, camera);
 
           }  
```

### 添加动画

我们可以通过在render()函数中对object(物体)的position(位置)，rotation(旋转)和scale(缩放)属性进行控制来实现简单地动画效果。渲染的FPS为60，也就意味着一秒钟会变化60次。

```js

   mesh.rotation.y += Math.PI / 180 * 1; 

```

***
![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f6mejp1jsnj211e0hbjrk.jpg)

## View source

*[source code](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-basic.html)*
