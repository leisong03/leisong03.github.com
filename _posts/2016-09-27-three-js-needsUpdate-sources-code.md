---
layout: post
title:  "three.js needsUpdate 方法"
date:   2016-09-27 20:00:00 +0800
categories: three.js JavaScript
tags: three.js JavaScript
author: JiuYang Chen
---

* content
{:toc}


本篇介绍`Material`类中的`needsUpdate`方法。`Geometry`中类似的`update`方法可以类推。






## 问题提出

```js

function render() {
    
    material.map = canvasMap;
    material.map.needsUpdate = true;
    
}


```

我想实时的更新材质(material)的贴图(map)，所以我在`render函数`中为`material`赋值并将`needsUpdate`设为`true`。
后来跑着跑着页面挂了，我发现这个赋值操作很占`GPU`，我在找解决方案的时候发现这玩意不需要赋值操作！！！

`needsUpdate`设为`true`它会实时的检测贴图是否更新，并更新贴图。



## `needsUpdate`


### step1

首先转跳到`three.js`中

```js

THREE.Texture.prototype = {

	constructor: THREE.Texture,

	set needsUpdate( value ) {      // value = true;

		if ( value === true ) this.version ++;  //this.version ++ 变为了1;

	}
	
	}


```

### step2

这个`version = 1`有什么用我们继续看 
由于是`Texture` 调用了`WebGLTextures`。


```js

THREE.WebGLTextures = function ( _gl, extensions, state, properties, capabilities, paramThreeToGL, info ) {
    
    
}


```

接着我们发现`WebGLTextures`中`setTexture2D` `setTextureCube`这两个函数用到了`version `所以我们先看`setTexture2D`。

* `setTexture2D()` 删减版

```js
    
    //  参数`texture`就是前面的`THREE.Texture`
    
	function setTexture2D( texture, slot ) {
           
	    // 这里 `texture.version = 1`

		if ( texture.version > 0 && textureProperties.__version !== texture.version ) {

            // 替换图片
            
			var image = texture.image;

				uploadTexture( textureProperties, texture, slot );
				return;

			}

		}


```

* `uploadTexture()` 用来更新纹理












