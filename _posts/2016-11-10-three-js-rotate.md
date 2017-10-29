---
layout: post
title:  "three.js 中的矩阵变换及两种旋转表达方式"
date:   2016-11-13 11:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL
author: JiuYang Chen
---

* content
{:toc}


本篇简单介绍`three.js`中矩阵变换及两种旋转表达方式。






## 矩阵变换

`three.js`使用矩阵来保存`Object3D`的变换信息。

### 矩阵变换的基础

* 平移变换

![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f9qtuy0woqj20en05ymxh.jpg)


* 比例变换

![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f9qtuy1pq8j20f305sglz.jpg)


* 旋转变换

`(x,y,z,1)` 绕`x`轴旋转

![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f9qtv01aazj20hk061dgb.jpg)

`(x,y,z,1)` 绕`y`轴旋转

![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1f9qtv0ceb7j20i105yq3g.jpg)


`(x,y,z,1)` 绕`z`轴旋转

![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1f9qtv0ta6lj20hu06c74s.jpg)

### `three.js`中的矩阵

```js

    var cube = new THREE.Mesh(new THREE.CubeGeometry(1,1,1),new THREE.MeshBasicMaterial());
        cube.position.set(1,2,3);
        cube.scale.set(7,8,9);

        scene.add(cube);


```

![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f9qtv1gao5j20bd0ew75g.jpg)

我们可以看到正如上面的公式 `cube`的平移`（1,2,3）`所以elements[12]、elements[13]、elements[14]依次为1,2,3

`cube`的缩放为（7,8,9）所以elements[02]、elements[5]、elements[10]依次为7,8,9


然后我们选择一下`cube`的`x`轴

```js

    var cube = new THREE.Mesh(new THREE.CubeGeometry(1,1,1),new THREE.MeshBasicMaterial());
        cube.rotation.x = Math.PI/3;
        scene.add(cube);


```

![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1f9qtv1yv7xj20b90a4gma.jpg)


## 三维旋转表达方式

`three.js`提供了两种三维旋转表达方式：`欧拉角(euler)`和`四元数(quaternion)`。它们相比较使用`矩阵的方式`进行变换更加的节省存储空间和更方便的进行插值。
但是`欧拉角(euler)`存在万向锁问题，配置可能失去一定的自由度所以都是使用在`四元数(quaternion)`。

### 欧拉角

欧拉角需要指定x,y,z三个轴的角度和旋转的顺序。

```js

    Euler( x, y, z, order )
    
```

`万向锁`问题：当三个万向节其中两个的轴发生重合时，会失去一个自由度的情形。

>https://zh.wikipedia.org


![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f9qtuvgfm8j20l40l5god.jpg)

正常状态：三个独立的旋转轴


![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f9qtuuaxeuj20lo0kkwgt.jpg)

万向锁：一旦选择±90°作为pitch角，就会导致第一次旋转和第三次旋转等价，整个旋转表示系统被限制在只能绕竖直轴旋转，丢失了一个表示维度。

### 四元数

四元数的出现就可以解决欧拉角的万向锁问题和万向锁带来的插值不是线性的问题。

具体的四元数在旋转的使用的原理可以参照：

[维基百科—四元数与空间旋转](https://zh.wikipedia.org/wiki/%E5%9B%9B%E5%85%83%E6%95%B0%E4%B8%8E%E7%A9%BA%E9%97%B4%E6%97%8B%E8%BD%AC)

```js

    Quaternion( x, y, z, w )
    
```

## three.js中的旋转方式

从源码中我们可以看出，`three.js`都是使用`quaternion`（四元数）来控制旋转。

```js

	setRotationFromAxisAngle: function ( axis, angle ) {

		// assumes axis is normalized

		this.quaternion.setFromAxisAngle( axis, angle );

	},

	setRotationFromEuler: function ( euler ) {

		this.quaternion.setFromEuler( euler, true );

	},

	setRotationFromMatrix: function ( m ) {

		// assumes the upper 3x3 of m is a pure rotation matrix (i.e, unscaled)

		this.quaternion.setFromRotationMatrix( m );

	},

	setRotationFromQuaternion: function ( q ) {

		// assumes q is normalized

		this.quaternion.copy( q );

	},

	rotateOnAxis: function () {

		// rotate object on axis in object space
		// axis is assumed to be normalized

		var q1 = new THREE.Quaternion();

		return function rotateOnAxis( axis, angle ) {

			q1.setFromAxisAngle( axis, angle );

			this.quaternion.multiply( q1 );

			return this;

		};

	}(),


```

### `object3D` `rotation` 属性

在`three.js`你可以使用`rotation`来设置`object3D`的旋转。

我们使用`rotation`设置的为一个`Euler（欧拉角）`所以它会先将`Euler（欧拉角）`转换为一个`quaternion（四元数）`。

源码：

```js

	rotation.onChange( onRotationChange );

	function onRotationChange() {

		quaternion.setFromEuler( rotation, false );

	}
	
	//setFromEuler()
	
		setFromEuler: function ( euler, update ) {
    
    		if ( euler instanceof THREE.Euler === false ) {
    
    			throw new Error( 'THREE.Quaternion: .setFromEuler() now expects a Euler rotation rather than a Vector3 and order.' );
    
    		}
    
    		// http://www.mathworks.com/matlabcentral/fileexchange/
    		// 	20696-function-to-convert-between-dcm-euler-angles-quaternions-and-euler-vectors/
    		//	content/SpinCalc.m
    
    		var c1 = Math.cos( euler._x / 2 );
    		var c2 = Math.cos( euler._y / 2 );
    		var c3 = Math.cos( euler._z / 2 );
    		var s1 = Math.sin( euler._x / 2 );
    		var s2 = Math.sin( euler._y / 2 );
    		var s3 = Math.sin( euler._z / 2 );
    
    		var order = euler.order;
    
    		if ( order === 'XYZ' ) {
    
    			this._x = s1 * c2 * c3 + c1 * s2 * s3;
    			this._y = c1 * s2 * c3 - s1 * c2 * s3;
    			this._z = c1 * c2 * s3 + s1 * s2 * c3;
    			this._w = c1 * c2 * c3 - s1 * s2 * s3;
    
    		} else if ( order === 'YXZ' ) {
    
    			this._x = s1 * c2 * c3 + c1 * s2 * s3;
    			this._y = c1 * s2 * c3 - s1 * c2 * s3;
    			this._z = c1 * c2 * s3 - s1 * s2 * c3;
    			this._w = c1 * c2 * c3 + s1 * s2 * s3;
    
    		} else if ( order === 'ZXY' ) {
    
    			this._x = s1 * c2 * c3 - c1 * s2 * s3;
    			this._y = c1 * s2 * c3 + s1 * c2 * s3;
    			this._z = c1 * c2 * s3 + s1 * s2 * c3;
    			this._w = c1 * c2 * c3 - s1 * s2 * s3;
    
    		} else if ( order === 'ZYX' ) {
    
    			this._x = s1 * c2 * c3 - c1 * s2 * s3;
    			this._y = c1 * s2 * c3 + s1 * c2 * s3;
    			this._z = c1 * c2 * s3 - s1 * s2 * c3;
    			this._w = c1 * c2 * c3 + s1 * s2 * s3;
    
    		} else if ( order === 'YZX' ) {
    
    			this._x = s1 * c2 * c3 + c1 * s2 * s3;
    			this._y = c1 * s2 * c3 + s1 * c2 * s3;
    			this._z = c1 * c2 * s3 - s1 * s2 * c3;
    			this._w = c1 * c2 * c3 - s1 * s2 * s3;
    
    		} else if ( order === 'XZY' ) {
    
    			this._x = s1 * c2 * c3 - c1 * s2 * s3;
    			this._y = c1 * s2 * c3 - s1 * c2 * s3;
    			this._z = c1 * c2 * s3 + s1 * s2 * c3;
    			this._w = c1 * c2 * c3 + s1 * s2 * s3;
    
    		}
    
    		if ( update !== false ) this.onChangeCallback();
    
    		return this;
    
    
    	},

```

### `Geometry` `rotateX`方法

通过源码我们可以发现该方法使用的是矩阵变换的方式来旋转物体。

```js

	rotateX: function () {

		// rotate geometry around world x-axis

		var m1;

		return function rotateX( angle ) {

			if ( m1 === undefined ) m1 = new THREE.Matrix4();

			m1.makeRotationX( angle );

			this.applyMatrix( m1 );

			return this;

		};

	}(),

	makeRotationX: function ( theta ) {

		var c = Math.cos( theta ), s = Math.sin( theta );

		this.set(

			1, 0,  0, 0,
			0, c, - s, 0,
			0, s,  c, 0,
			0, 0,  0, 1

		);

		return this;

	},

```


