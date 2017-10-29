---
layout: post
title:  "three.js-Raycaster"
date:   2016-08-10 23:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇为Raycaster(射线)的使用。



## Raycaster

### Constructor

```js

    var raycaster = new THREE.Raycaster( origin, direction, near, far );

```

### Main Properties

* `origin`：射线放射的位置 
* `direction` ：方向向量，应该是标准化的`.normalize()`
* `near` ：能投射的最近距离`default：0`
* `far`： 能投射的最远距离 `default：Infinity`

## 拾取物体
我们可以定义一个由mouse(鼠标)发出的射线，从而来拾取物体。
获取第一个和射线相交的物体，进一步可以进行其他操作（本篇用于改变获取物体的颜色）。


### 添加一组物体

```js
    
    //用来保存物体对象
    var array = [];
    
    var geometry = new CubeGeometry(100,100,100);
    
    var materisl = new MeshBasicMaterial({ color: Math.random() * 0xffffff, opacity: 0.5 } );
    
    //定义20个cube
    for(var i=0;i<20;i++){
       
       var mesh = new THREE.Mesh(geometry,materisl);
          
           object.position.x = Math.random() * 800 - 400;
		   object.position.y = Math.random() * 800 - 400;
		   object.position.z = Math.random() * 800 - 400;
		   
       scene.add(mesh);
       
       array.push(mesh);
       
    }

```

### 添加raycaster

```js

    var raycaster = new THREE.Raycaster(); 
    
    //用来保存鼠标坐标信息
    var mouse = new THREE.Vector2();  
    
    //添加鼠标移动事件，检测鼠标的移动
    document.addEventListener('mousemove', onDocumentMouseMove, false);  
    
    function onDocumentMouseMove(event) {  
        
        //获取鼠标的x，y坐标
        mouse.x = (event.clientX / window.innerWidth) * 2 - 1;  
        mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;  
        
            }  
            
    function render(){
        
        //更新鼠标和射线位置
        raycaster.setFromCamera(mouse, camera); 
        
        renderer.render(scene, camera);  
        raycaster.setFromCamera(mouse, camera);  
    }
```

### 拾取物体改变颜色
前面两步我们添加了object（物体）和raycaster（射线），当我们获取到物体还需要进一步操作。改变获取物体的颜色。

```js
     function render() {  
     
                requestAnimationFrame(render);  
                renderer.render(scene, camera);  
                
                raycaster.setFromCamera(mouse, camera);  
                
                //获取和射线相交的array[]中的物体
                var intersects = raycaster.intersectObjects(array);  
                
                var INTERSECTED；
                
                //这里我们操作第一个相交的物体
                if (intersects.length > 0) {  
                    if (INTERSECTED != intersects[0].object) {
                          
                        if (INTERSECTED) INTERSECTED.material.color.setHex(INTERSECTED.currentHex);  
                        INTERSECTED = intersects[0].object;  
                        
                        //设置相交的第一个物体的颜色
                        INTERSECTED.currentHex = INTERSECTED.material.color.getHex(); 
                        //将该物体设为随机的其他颜色 
                        INTERSECTED.material.color.set( 0xff0000 );  
                    }  
                } else {  
                    //当射线离开的时候变为原来的颜色
                    if (INTERSECTED) INTERSECTED.material.color.set(INTERSECTED.currentHex);  
                    INTERSECTED = null;  
                }  
            }  



```

 ***
![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1f6pt9gr3c5j20p20hm75k.jpg)
 
## View source
 
*[source code](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-raycaster.html)*