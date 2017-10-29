---
layout: post
title:  "three.js-drawLine"
date:   2016-08-11 12:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇上一篇介绍的`raycaster`和`curves`(曲线)实现在canvas画画。
目前只支持画线。



## 添加画板

我们可以添加一个`Plane`当做画板，要足够的大。

```js

    var geometry = new THREE.PlaneGeometry(10000, 10000, 20);
				var material = new THREE.MeshBasicMaterial({
				
				    // 这里利用这两个参数设置plane为透明
					opacity: 0,
					transparent: true
				});
				var ground = new THREE.Mesh(geometry, material);
				
				ground.position.set(0, 0, 0);
				array.push(ground);
				scene.add(ground);

```

## 添加画笔

然后添加一个`raycaster`当做画笔。利用`raycaster`的`intersectObjects()`方法获取和射线相交对象，通过该对象的`point`属性获取当前相交的交点坐标。

```js
            
			var helpPoint = new THREE.Vector3();

			function render() {

				stats.update();
				raycaster.setFromCamera(mouse, camera);
				
				var intersects = raycaster.intersectObjects(array);

				if(intersects.length > 0) {
				    
				    //获取画笔和画板的交点左边，并保存到`helpPoint`中
					helpPoint = intersects[0].point;
				}
				requestAnimationFrame(render);
				renderer.render(scene, camera);
			}


```

## 添加画线事件

```js           

                var v = []; //用来存储要绘制曲线的点
                var obj = new THREE.Object3D(); //用来存储绘制好的曲线
                  
                //添加鼠标按下事件
                
                //鼠标按下开始绘制
				document.addEventListener('mousedown', onDocumentMouseDownLine, false);
						
				function onDocumentMouseDownLine(event) {
				
				    event.preventDefault();
				    //初始化`v`，获取的所有点的集合
				    v = [];
				//添加鼠标移动事件，画线
				    document.addEventListener('mousemove', onDocumentMouseMoveLine, false);
			    }
				
				function onDocumentMouseMoveLine(event) {
				
				event.preventDefault();
                
                //将绘制过程中获取的点放入`v`中
				v.push(new THREE.Vector2(helpPoint.x, helpPoint.y));
			}
                
                //添加鼠标抬起事件
                function onDocumentMouseUpLine(event) {
                
				event.preventDefault();
				
				//将绘制的曲线放入`obj`中
				obj.add(line);
				//初始化 v
				v = [];
				document.removeEventListener('mousemove', onDocumentMouseMoveLine, false);
			}

```

## 画2D线

```js     

            function drawLine(v) {
                
                // 用`v`中的点生成一条曲线
				var curve = new THREE.SplineCurve(v);

				var Linematerial = new THREE.LineBasicMaterial({
					color: 0x000000
				});
                
				var path = new THREE.Path(curve.getPoints(1000));
                
				Linegeometry = path.createPointsGeometry(1000);

                //使用曲线在场景中绘制出曲线
				line = new THREE.Line(Linegeometry, Linematerial);
				
				
				scene.add(line);
			}

			function render() {
                
                //因为创建SplineCurve必须要两个点，所以要`v.length`大于2是才能调用drawLine()
				if(v.length > 2) {
				
				    //要移除了场景中的线，实时画新的线。直到鼠标抬起重置`v = []`
					scene.remove(line);
					drawLine(v);
					//添加以前画的所有线段
					scene.add(obj);
				}
			}

```

 ***
![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f6qo6ghdmgj20zm0ho75w.jpg)
 
 
## 画3D线
同样的我们也可以用上述方法画3D的线，但是由于`point`属性只包含`x`和`y`轴的坐标。所以我们需要自定义另一个坐标轴的坐标。
这里我们可以自定义`z`轴坐标（本例中设为了一个常量）。

```js    

    //重写`mousemove`鼠标移动方法
    function onDocumentMouseMoveLine(event) {
				event.preventDefault();
                
                //因为下面要画3D生成过称中不允许出现重复的点，所以要去除重复的点  
				var temp = v[v.length - 1];
                
                //这里我们将`z`轴坐标设为常量50 
				if(temp) {
					if(temp.x == helpPoint.x && temp.y == helpPoint.y && temp.z == 50) {
						return;
					}
				}
				v.push(new THREE.Vector3(helpPoint.x, helpPoint.y, 50));
			}
    
    //重写函数`drawLine(v)`
    
    function drawLine(v) {
                
                //这里使用`CatmullRomCurve3`生成曲线
				var curve = new THREE.CatmullRomCurve3(v);

				var extrudeSettings = {
					curveSegments: 12,
					steps: 200,
					amount: 2,
					extrudePath: curve
				};
				var pts = [];
				pts.push(new THREE.Vector2(5, 5));
				pts.push(new THREE.Vector2(5, -5));
				pts.push(new THREE.Vector2(-5, -5));
				pts.push(new THREE.Vector2(-5, 5));

				var shape = new THREE.Shape(pts);
                
                //使用`ExtrudeGeometry`来扩展或说是挤压曲线，将曲线变成3D
				var geometry = new THREE.ExtrudeGeometry(shape, extrudeSettings);

				var material = new THREE.MeshBasicMaterial({
					color: 0xff8000
				});

				mesh = new THREE.Mesh(geometry, material);

				scene.add(mesh);

			}
    
```    

 ***
![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f6qpxbyc92j20qp0gqmyx.jpg)


## ExtrudeGeometry

### Constructor

```js    

    var geometry = new THREE.ExtrudeGeometry(shapes,options);

```   


### Main Properties 

* `shapes` :形状或是一系列的形状 
* `options` :选项参数

#### Options

* `curveSegments` :曲线上的点数
* `steps` :用于细分曲线段数
* `amount` :深度挤压的形状数
* `extrudePath` :THREE.CurvePath,3D曲线来扩展 
 
## View source
 
* *[source code 2D](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-raycaster-drawLine-2D.html)*
* *[source code 3D](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-raycaster-drawLine-3D.html)*
