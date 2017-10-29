---
layout: post
title:  "three.js-model"
date:   2016-08-11 20:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL OBJ
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇介绍`model`（模型）及相关问题。
`OBJ`格式的模型较为通用，较为适合用于3D软件互导。所以我们以`OBJ`模型以及其材质文件`mtl`为例。
`STL`、`CTM`、`VTK`等其他格式模型均类似。只需要替换对应的加载`js`文件。




## 添加OBJ模型


```js
    
	<script type="text/javascript"  src="../js/three/DDSLoader.js"></script> 
    <script type="text/javascript"  src="../js/three/MTLLoader.js"></script>
	<script type="text/javascript"  src="../js/three/OBJLoader.js"></script> 

    //这里的`test.obj`是我从3Dmax新鲜导出的一个简陋的`OBJ`模型 
    //比较基本，没有材质
    
    function loadObj() {
    
				// model

				var onProgress = function(xhr) {
					if(xhr.lengthComputable) {
						var percentComplete = xhr.loaded / xhr.total * 100;
						console.log(Math.round(percentComplete, 2) + '% downloaded');
					}
				};

				var onError = function(xhr) {};

				THREE.Loader.Handlers.add(/\.dds$/i, new THREE.DDSLoader());
                
                //导入mtl材质
				var mtlLoader = new THREE.MTLLoader();
				mtlLoader.setPath('../js/three/obj/');
				mtlLoader.load('test.mtl', function(materials) {

					materials.preload();
                    
                    //导入obj文件
					var objLoader = new THREE.OBJLoader();
					objLoader.setMaterials(materials);
					objLoader.setPath('../js/three/obj/');
					objLoader.load('test.obj', function(object) {

						scene.add(object);

					}, onProgress, onError);

				});

			}
```

 ***
![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f6qrqzvbmpj20r40djaa9.jpg)


## 给模型添加阴影

我们在`scene.add(object);`之后通过控制台打印出`object`。

```js

    console.log(object);

```

导入三维场景中的`model`会被封装成一组`mesh`。
以`test.obj`为例，结构如下

```js

    THREE.Group
          || children:Array[1]
             || THREE.Mesh
                || geometry:THREE.BufferGeometry   
                || material:THREE.MeshPhongMaterial

```

所以你也会发现`THREE.Mesh`包含`castShadow：fals`只要设为`true`即可。当然`THREE.Group`也是包含`castShadow`属性的，但是设置无效。
所以你要循环遍历将所有子节点都设为`true`。


 ***
![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1f6qsq3dn5qj20uz0fzaaj.jpg)


* *[source code obj](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-obj.html)*
* *[source code obj-shadow](https://github.com/Chenjy1225/Chenjy1225.github.io/blob/master/source/three-obj-shadow.html)*
