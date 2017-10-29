---
layout: post
title:  "three.js-sun-lensflare"
date:   2016-08-10 22:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇介绍一下高级光源，镜头眩光。镜头眩光在生活中比较常见，比如说当你直接朝着太阳拍照的时候就会出现镜头眩光。
大多数情况下要避免这种情况的发生，但是在三维场景和游戏中却是一种很好的效果。让场景更加的真实。
同时也会简单介绍一下相机控件。



## 渲染器设置

要生成阴影需要将渲染器的`alpha`模式开启。

```js

    render.alpha = true;

```


## 添加光源

这里的需要添加一个光源来模型太阳光。一般使用`SpotLight`。

```js

    var spotLight = new THREE.SpotLight(0xffffff);  
    
        spotLight.position.set(50, 10, -50);  
        spotLight.castShadow = true; 
        
        scene.add(spotLight);  

```

## 添加LensFlare对象

这里要添加LensFlare对象来模型眩光的效果。

### LensFlare

#### Constructor

```js

    var lensFlare = new THREE.LensFlare(texture, size, distance , blending, color);  

```

#### Main Properties

* `texture`： 眩光的纹理 
* `size`：眩光的尺寸 
* `distance`：光源到相机的距离
* `blending`：为眩光提供多种材质。融合模式决定他们如何融合在一起。`default：THREE.AdditiveBlending`半透明的眩光
* `color`：眩光的颜色


```js
 
    var textureFlare0 = THREE.ImageUtils.loadTexture("img/lensflare0_alpha.png"); 
     
    var textureFlare1 = THREE.ImageUtils.loadTexture("img/lensflare3.png");  
    
    var lensFlare = new THREE.LensFlare(textureFlare0, 350, 0, THREE.AdditiveBlending, new THREE.Color(0xffffff));
    
        //可以使用  LensFlare add()方法添加多个LensFlare对象 来优化眩光的效果
        lensFlare.add(textureFlare1, 60, 0.6, THREE.AdditiveBlending);  
        lensFlare.add(textureFlare1, 70, 0.7, THREE.AdditiveBlending);  
        lensFlare.add(textureFlare1, 120, 0.9, THREE.AdditiveBlending);  
        lensFlare.add(textureFlare1, 70, 1.0, THREE.AdditiveBlending);  
        lensFlare.position = spotLight.position;  
            scene.add(lensFlare);  
 
```
 
## 添加相机控件
为了更好的看到眩光效果，我们可以添加一个相机控件通过鼠标来控制相机的角度和位置。
 
### Controls

#### Types of Control

* `TrackballControls`:轨迹球控件(最常用的控件)，通过鼠标移动、平移和缩放场景。
* `FirstPersonControls`:第一人称控件，类似于第一人称射击游戏，用键盘移动，用鼠标转动。  
* `FlyControls`:飞行模拟控件，用键盘和鼠标来控制相机的移动和转动。 
* `OrbitControls`:轨道卫星模拟控件，可以使用鼠标和键盘在场景中游走。

#### Use to Control

这里以TrackballControls为例，其他控制器可以类推。

```js

    <script type="text/javascript" src="../js/three/TrackballControls"></script>
    
    var control = new THREE.TrackballControls(camera);
    
    var clock = new THREE.Clock();
    
    function render(){
        
        var delta = clock.getDelta();
        
        //实时更新相机控件
        control.update(delta);
        
        requestAnimationFrame(render);
		renderer.render(scene, camera);
    
    }


```

 ***
![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1f6ppjjv9o7j20c00c1t8w.jpg)
 
## View source
 
 *[source code](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-sun-lensflare.html)*
