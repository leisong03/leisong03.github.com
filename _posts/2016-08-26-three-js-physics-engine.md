---
layout: post
title:  "three.js-physics engine"
date:   2016-08-26 07:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL physijs
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇介绍物理引擎库Physijs，通过物理引擎我们可以向场景添加重力、摩擦、约束条件或是碰撞检测等令人激动的效果。
Physijs是基于著名的物理引擎ammo.js。

*learn more from `https://chandlerprall.github.io/Physijs/`*




## physijs

### step1

导入库文件

```js
    
    //任务线程 
    Physijs.scripts.worker = 'js/Physijs/physijs_worker.js';  
    
    //内部库ammo.js
    Physijs.scripts.ammo = 'js/Physijs/ammo.js';  

```

### step2

创建带有物理效果的场景。

```js
    
    
    var scene = new Physijs.Scene(); 
     
    //设置重力参数为y轴负方向，大小为10
    scene.setGravity(new THREE.Vector3(0,-10,0));  

```

### step3

使用physijs库的Physijs.Mesh()、Physijs.createMaterial()将原Mesh()、Material()封装起来。

```js

    var geometry = new THREE.CubeGeometry(10, 10, 10);  
    
    //参数 restitution(弹性形变)和friction(摩擦系数)
    var material = new Physijs.createMaterial(new THREE.MeshBasicMaterial({  
        restitution:0.5,  
        friction:0.5  
    }))  
    
    //物理场景创建的对象增添了第三个参数   1：可以正常运动   0：困定(类似于墙体)   默认值：1
    var mesh = new Physijs.BoxMesh(geometry,material,1);  


```

### step4

渲染物理场景

```js

    function render(){  
    
       requestAnimationFrame(render);  
       renderer.render(scene, camera);
        
       scene.simulate();  
    }  

```


这样我们就创建出了一个具有重力（y轴负方向大小为10），具有摩擦（摩擦系数为0.5）和碰撞的物体（弹性形变系数为0.5）的物体。
由于我们在物理场景中不能像以往一样通过在render()中使用position来控制物体进行运动。只能通过设置力或是设置速度。

所以很多时候物体的运动可能不尽人意，这的时候`约束`就显得很重要了。它可以保证`你`被击飞的时候你的头在你的脖子上，汽车运动的时候它的轮子在车身上。

### 约束

### 种类 

*  `PointConstraint` 点约束，你可以将两个对象约束起来，一个对象动了，另外一个对象也会动。

```js
    
    // 前两个参数为两个对象
    //第三个参数为绑定的位置 一般来说你要仅仅将两个物体绑定在一起，最好设置在第二个对象的位置。
    
    var pointConstraint = new Physijs.PointConstraint(objectOne,objectTwo,objectPosition);

```

*  `HingeConstraint` 门约束，你可以将两个对象像一扇门的两部分一样约束起来。

```js
    
    // 前两个参数为两个对象
    //第三个参数为绑定的位置 
    //最后一个参数为要绕着旋转的轴
    
    var hingeConstraint = new Physijs.HingeConstraint(objectOne,objectTwo,objectPosition,objectAxis);

```

*  `SliderConstraint` 轴约束，你可以将物体的移动限制在轴上。

```js
    
    //也可以四个参数 如果你想将一个对象约束到另一个上面
    
    var hingeConstraint = new Physijs.HingeConstraint(objectOne,objectPosition,objectAxis);

```

*etc...*

这个demo里面由于两个物体在场景初始化的时候就相交了发生了弹性形变，所以会产生力分来。

 ***
![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f76xaccxe8j20kh0f2t8r.jpg)
 ***
![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1f76xb2tv6nj20kh0f4749.jpg)
 
## View source
 
*[source code](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-js-physics.html)*

