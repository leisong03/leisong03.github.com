---
layout: post
title:  "three.js-Basic-Expand"
date:   2016-08-09 18:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL Detector.js stats.js dat.gui.js tween.js
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇为基础扩展部分(学习使用插件扩展你的三维场景)



## WebGL支持
目前绝大部分主流浏览器是支持WebGL的，但是IE的大部分版本并不支持WebGL。
所以如果你想使用旧版本的IE浏览器，可以通过下载`chrome`的`Google Chrome Frame`插件
也可以使用`iewebgl`

同时对于浏览器的WebGL支持的检测也是必不可少的。
three.js提供了`Detector.js`用来检测浏览器对于WebGL的支持。

```js

<script src="js/Detector.js"></script>
    if ( ! Detector.webgl ) Detector.addGetWebGLMessage();

```

## 检测浏览器窗体
从上篇的Demo可以看到我们设置渲染的大小为`window.innerWidth``window.innerHeight`也就是浏览器窗体的大小。
当我们的浏览器窗体发生变化的时候，就会发生很尴尬的情况。
![resize](http://ww1.sinaimg.cn/mw690/c584f169gw1f6odzvym4kj20l70e3glk.jpg)

所以当窗体发生变化的时候，我们需要更新相机的信息。并且设置新的`window.innerWidth``window.innerHeight`

```js

    window.addEventListener( 'resize', onWindowResize, false );
    
    function onWindowResize() {

				camera.aspect = window.innerWidth / window.innerHeight;
				camera.updateProjectionMatrix();

				renderer.setSize( window.innerWidth, window.innerHeight );
			}


```

## 性能检测
在3D世界里经常通过帧数`FPS`（Frames Per Second）来评估一个程序的性能。
帧数越高，画面越流畅。大部分的游戏帧数都会超过30。
three.js提供了`stats.js`用来检测程序的性能。

```js

<script src="js/stats.min.js"></script>

    //新建一个信息框
    var stats = new Stats();
    
    // 0: fps, 1: ms, 2: mb, 3+: custom
    stats.showPanel( 1 ); 
    
    document.body.appendChild(stats.domElement);
    
    //渲染函数
    function render(){
         //实时渲染
         stats.update();
        }

```

```js

    //手动开关监控器
    stats.begin();
    stats.end();

```

Panel效果图：
![panel](http://ww4.sinaimg.cn/mw690/c584f169gw1f6nxpaolghj20i202m0t1.jpg)

## 控制面板
`dat.gui.js`
使用`dat.gui.js`可以在页面生成一个控制面板方便你直接控制三维场景中的对象。
以前一篇的Demo为例，通过面板控制`Cube`的`width`，`height`和`depth`

```js

<script src="js/dat.gui.min.js"></script>

     var controls = new function () {
            //初始化width，height和depth属性
            this.width = 4;
            this.height = 4;
            this.depth = 4;
            
            //重绘函数
            this.redraw = function () {
            
                //移出旧的cube
                scene.remove(cube);
                
                //重绘三维图形
                var geometry = new THREE.CubeGeometry(controls.width, controls.height, controls.depth);

				var material = new THREE.MeshBasicMaterial({
					color: 0x4d6dad
				});

				var mesh = new THREE.Mesh(geometry, material);
				//添加新的cube
                scene.add(cube);
            };
       }
     
     //实例化一个gui对象  
     var gui = new dat.GUI();
         
         gui.add(controls, 'width', 0, 40).onChange(controls.redraw);
         gui.add(controls, 'height', 0, 40).onChange(controls.redraw);
         gui.add(controls, 'depth', 0, 40).onChange(controls.redraw);


```

gui效果图：
![gui](http://ww2.sinaimg.cn/mw690/c584f169gw1f6nz6f0jvgj206u02wdfr.jpg)

## 补间动画
`tween.js`
`tween.js`是一个小型的JavaScript库。你可以用来定义某个属性在两个值之间的过渡。
它会自动计算出起始值到结束值之间的所有中间值。从来在三维场景中实现动画效果。

以前一篇的Demo为例，通过`tween.js`控制`Cube`的`position`

```js

<script src="js/tween.min.js"></script>
   
   //定义要控制的对象
    var option = {  
       px: cube.position.x
   };  
   
    /*  
     *  .to 定义起始值和结束值 ，1000划分的中间值个数
     *  .delay 定义开始延时
     *  .start 开启补间动画
     */ 
     
    
    var tween = new TWEEN.Tween(option)  
        .to({  
                px: [-50, -40, -40, -50]
        }, 1000)  
        .onUpdate(function() {  
            cube.position.x = this.px;
        })  
        .delay(1000)  
        .start();  
    
    //渲染函数
    function render() {  
        
        //实时更新
        TWEEN.update();  
        
        requestAnimationFrame(render);  
        renderer.render(scene, camera);  
                  
}  
    // 你也可以调用tween.chain(tween)函数使动画循环播放或是几个动画连续播放。
```

***

![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f6oe9u9w39j20bc0bxaab.jpg)

## View source

*[source code](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-basic-Expand.html)*