---
layout: post
title:  "three.js-bone"
date:   2016-08-12 12:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL Bone 
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇介绍`bone`的创建和绑定。完成简单的骨骼动画。




## 初始化数据、创建骨骼

这里我们使用`Cylinder`来创建一段几何图形模拟具有骨骼的物体。
你可以比作人的身体而我们创建的`bones`骨骼以及`skeleton`骨架就相当于人的脊椎。

```js
    
    //每段高度，这里指骨骼长度
    var segmentHeight = 6;
    
    //分段数量，这里指骨骼数量
	var segmentCount = 1;
	
	//骨架总高度 `6`
	var height = segmentHeight * segmentCount;
	
	//骨架一半的高度 `3`
	var halfHeight = height * 0.5;
	var sizing = {
					segmentHeight: segmentHeight,
					segmentCount: segmentCount,
					height: height,
					halfHeight: halfHeight
				};

    
    function createGeometry(sizing) {
				var geometry = new THREE.CylinderGeometry(
					0.5, //圆柱头部半径
					0.5, //圆柱底部半径
					sizing.height,   // 骨架高度`6` 
					32,
					sizing.segmentCount * 6, // 骨骼数`6`
					true 
				);
				
				//遍历几何图形上的所有顶点
				for (var i = 0; i < geometry.vertices.length; i++) {
				
					var vertex = geometry.vertices[i];
					
					
				/*  计算顶点索引值 `skinIndex` （受那个骨骼影响）
				 *  先将原来 `vertice `顶点的 `y `坐标加上骨架的一半高度，
				 *  相当于将原来在骨架顶部的 `y `轴坐标原点上移到骨骼 `y `轴的中心处
				 *   |                                          |
				 *   |                                          |
				 *   |                +  sizing.halfHeight =    |. `y`坐标原点   
				 *   |                                          |
				 *   |                                          |
				 *   |. `y`坐标原点                                                                               |
				 *
				 *  然后除以骨骼数，然后向下取整便可取到索引值
				 *
				 * */					
					
					
					var y = (vertex.y + sizing.halfHeight);
					var skinIndex = Math.floor(y / sizing.segmentHeight);
					
					
				/*  计算权重`skinWeight`受骨骼影响的比重
				 *   
				 *  y % sizing.segmentHeight （这里的y是偏移过的也就是加了`sizing.halfHeight`）
				 * 
				 *  这里取余可以求出相对一根骨骼底部的偏移量（因为索引值上面已经求出来了）
				 *  再除以骨架的高度，便可以求出相对于权重
				 * 
				 * */					
				 
					var skinWeight = (y % sizing.segmentHeight) / sizing.segmentHeight;
					
					//放入相应的`geometry`属性中
					geometry.skinIndices.push(new THREE.Vector4(skinIndex, skinIndex + 1, 0, 0));
					geometry.skinWeights.push(new THREE.Vector4(1 - skinWeight, skinWeight, 0, 0));
				}
				return geometry;
			};
	
```

## 创建骨架和蒙皮


```js

    function createMesh(geometry, bones) {
                
                //创建骨骼材质
				var material = new THREE.MeshPhongMaterial({
					skinning: true,
					color:0xff0000
				});
				
				//创建蒙皮对象
				var mesh = new THREE.SkinnedMesh(geometry, material);
				
				//通过已有的骨骼创建骨架
				var skeleton = new THREE.Skeleton(bones);
				
				//从蒙皮，将蒙皮对象和骨架绑定
				mesh.add(bones[0]);
				mesh.bind(skeleton);
				
				return mesh;
			};

```

### SkinnedMesh

蒙皮对象。

#### Constructor

```js

    var mesh = THREE.SkinnedMesh( geometry, material );

```

#### Main Properties

* `geometry` :一种几个图形，需要设置`Geometry.skinIndices`（索引值）和 `Geometry.skinWeights`（权重）
* `material` :材质，需要设置`skinning: true`

### Skeleton

## 创建身体

```js

    var geometry = createGeometry(sizing);
	var bones = createBones(sizing);
				
	mesh = createMesh(geometry, bones);
	
	scene.add(mesh);

```

## 活动身体

现在只需要在`render()`里面操作骨骼就可以产生简单的骨骼动画了。

```js
    function render() {
    
			requestAnimationFrame(render);
			renderer.render(scene, camera);
			
			//操作第二个骨骼进行旋转
			mesh.skeleton.bones[1].rotation.x = Math.PI / 180 * 30;
			mesh.skeleton.bones[1].rotation.z = Math.PI / 180 * 30;
			}

```


 ***
![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f6qznfai53j20hf0ev0t0.jpg)


*[source code](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-obj-shadow.html)*
