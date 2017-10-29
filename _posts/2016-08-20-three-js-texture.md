---
layout: post
title:  "three.js-texture"
date:   2016-08-16 20:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇介绍纹理的使用。



## 纹理

我们可以在材质中加载纹理，在`Material`的`map`属性中。

```js
    
    // 新建 Texture 加载器
    var loader = new THREE.TextureLoader();

	var texture = loader.load("img/text.png");

	var material = new THREE.MeshBasicMaterial({
					color: 0xff0000,
					map: texture
				});

```

前面用到的`THREE.ImageUtils.loadTexture` 已经被摒弃。

```js

var texture = new THREE.ImageUtils.loadTexture("img/text.png");
                
				var material = new THREE.MeshBasicMaterial({
					color: 0x4d6dad,
					map: texture
				});
				
```

纹理的加载时异步的，所以需要注意。支持`PNG`、`GIF`或`JPEG`输入格式。
最好使用正方形图片，保证长宽都是2的次方大小。

### 凹凸贴图

凹凸贴图是一张灰度图，上面有像素的相对高度，可以用来增加材质的高度。

```js 

	var material = new THREE.MeshBasicMaterial({
					color: 0xff0000,
					bumpMap: texture
				});

```

### 法向贴图

法向贴图保存了像素的法向向量，可以使材质具有更加细致的凹凸和皱纹。

```js 

	var material = new THREE.MeshBasicMaterial({
					color: 0xff0000,
					normalMap: texture
				});

```

### 光照贴图

光照贴图是提前渲染好的阴影图片。

```js 

	var material = new THREE.MeshBasicMaterial({
					color: 0xff0000,
					lightMap: texture
				});

```


### other

罗列这些是极为麻烦的，所以
*learn more from `http://threejs.org/`*
