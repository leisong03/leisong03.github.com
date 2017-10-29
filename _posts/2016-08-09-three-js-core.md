---
layout: post
title:  "three.js-core"
date:   2016-08-09 20:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇简单列举一下three.js的核心对象。



## scenes

### scene

三维场景。

#### Constructor

```js

    var scene = new THREE.Scene();

```

#### Main Properties

* `fog` ：定义雾影响场景渲染 `default：null`

### fog

受雾影响的三维场景。

#### Constructor

```js

    var fog = new THREE.Fog(hex, near, far);

```

#### Main Properties

* `hex` ：定义雾的颜色
* `near` ：定义雾效果产生的起始点`default：1`
* `far` ：定义雾效果的结束点`default：1000`

## cameras 

### OrthographicCamera

正交投影相机

#### Constructor

```js

    var camera = new THREE.OrthographicCamera( left, right, top, bottom, near, far );

```

#### Main Properties

* `left` ：相机可视范围的左平面 
* `right` ：相机可视范围的右平面
* `top` ：相机可视范围的上平面
* `bottom` ：相机可视范围的下平面
* `near` ：相机可视范围最近的位置
* `far` ：相机可视范围最远的位置

### PerspectiveCamera

透视投影相机

#### Constructor

```js

    var camera = new THREE.PerspectiveCamera( fov, aspect, near, far );

```

#### Main Properties

* `fov` ：相机的视角
* `aspect` ：相机可视范围的长宽比
* `near` ：相机可视范围最近的位置
* `far` ：相机可视范围最远的位置

## renderers

### WebGLRenderer

WebGL渲染器

#### Constructor

```js

    var webGlRenderer = new THREE.WebGLRenderer({
        antialias:false,
        shadowMapEnabled:flase 
        });

```

#### Main Properties

* `antialias` ：抗锯齿，进行边缘的优化`default：false`
* `shadowMapEnabled` ：如果想让光源可以生成阴影，需要开启`default：false`

## lights 

### AmbientLight

影响整个场景的光源，但是没有特定的光线来源。所以不会产生阴影。

#### Constructor

```js

    var light = new THREE.AmbientLight( color, intensity );
    
    scene.add( light );

```

#### Main Properties

* `color` ：光源颜色
* `intensity` ：光源的光照强度  `default：1`

### PointLight

点光源，照向所有方向的光源

#### Constructor

```js

    var light = new THREE.PointLight( color, intensity, distance, decay );
    
    light.position.set( x, y, z );
    
    scene.add( light );

```

#### Main Properties

* `color` ：光源颜色
* `intensity` ：光源的光照强度  `default：1`
* `distance` ：光源能投射到的最远距离`default：0.0`
* `decay` ：光沿着距离变暗的程度`default：1`
* `shadow` ：存储光的阴影所有相关信息

### SpotLight

聚光灯光源，锥形灯光

#### Constructor

```js

    var light = new THREE.SpotLight( color, intensity, distance, angle, penumbra, decay )
    
    light.position.set( x, y, z );
    //光源产生阴影
    spotLight.castShadow = true;
    
    scene.add( light );

```

#### Main Properties

* `color` ：光源颜色
* `intensity` ：光源的光照强度  `default：1`
* `distance` ：光源能投射到的最远距离`default：0.0`
* `angle` ：锥形光源的角度  `max：Math.PI/2`
* `penumbra` ：聚光灯的从照射点的衰减速度属性 `default：0`
* `decay` ：光沿着距离变暗的程度`default：1`
* `target` ：光投射的目标`default：position(0,0,0)`
* `shadow` ：存储光的阴影所有相关信息
* `castShadow` ：如果设为true则生成动态投影`default：false`

### DirectinalLight

模型类似于太阳的距离很远的光源

#### Constructor

```js

    var directionalLight = new THREE.DirectionalLight( hex, intensity );
    
    directionalLight.position.set( x, y, z );
    
    scene.add( directionalLight );

```

#### Main Properties

* `color` ：光源颜色
* `intensity` ：光源的光照强度  `default：1`
* `target` ：光投射的目标`default：position(0,0,0)`
* `shadow` ：存储光的阴影所有相关信息
* `castShadow` ：如果设为true则生成动态投影`default：false`

## Materials

材质，决定了几何体外表的样子

### Types of Material

* `MeshBasicMaterial` ：基础材质，简单地颜色。
* `MeshDepthMaterial` ：根据网格到相机的距离计算颜色。
* `MeshNormalMaterial` ：根据物体表面的法向向量计算颜色。
* `MeshFaceMaterial` ：一个可以为各个表面指定不同颜色的容器。
* `MeshLambertMaterial` ：考虑光照效果，创建颜色暗淡不光亮的物体。
* `MeshPhongMaterial` ：考虑光照效果，创建光亮的物体。
* `ShaderMaterial` ：自定义着色器程序。
* `LineBasicMaterial` ：这种材质可用于`THREE.Line`。
* `LineDashedMaterial` ：创建虚线效果。

### Common Main Properties

* `ID` ：用来标示材质。在创建时赋值。 
* `name` ：通过这个属性赋予材质名称。
* `opacity` ：设置材质的透明度。
* `transparent` ：是否透明。如果为`true`则用`opacity`值渲染物体。
* `overdraw` ：过度描绘（如果渲染出的物体有缝隙，则可以设为`true`）。
* `visible` ：设置材质是否可见。
* `side` ：决定哪一面应用材质`default：THREE.FrontSide（前面）``THREE.BackSide（后面）``THREE.DoubleSide（双侧）`。
* `needsUpdate` ：告诉Three.js库材质是否改变了。如果为`true`则使用新的材质刷新缓存。

## 2DGeometries

二维网格

### PlaneGeometry

二维矩形 

#### Main Properties

* `width` ：宽度   `needed` ：true
* `height` ：高度   `needed` ：true
* `widthSegments` ：宽度段数   `needed` ：false
* `heightSegments` ：高度段数   `needed` ：false

### CircleGeometry

二维圆 

#### Main Properties

* `radius` ：半径   `needed` ：true
* `segments` ：分段   `needed` ：false
* `thetaStart` ：起始角   `needed` ：false
* `thetalength` ：角度   `needed` ：false

### ShapeGeometry

自定义二维图形
包含了众多的绘图函数。

*learn more from `http://threejs.org/`*

## 3DGeometries

三维网格

### CubeGeometry

三维矩形

#### Main Properties

* `width` ：宽度   `needed` ：true
* `height` ：高度   `needed` ：true
* `depth` ：深度   `needed` ：true
* `widthSegments` ：宽度段数   `needed` ：false
* `heightSegments` ：高度段数   `needed` ：false
* `depthSegments` ：深度段数   `needed` ：false

### SphereGeometry

三维球体

#### Main Properties

* `radius` ：半径`needed` ：false
* `widthSegments` ：宽度段数  `needed` ：false
* `heightSegments` ：高度段数 `needed` ：false
* `phiStart` ：指定x轴从什么地方开始绘制`needed` ：false
* `philength` ：指定从phiStart开始绘制多少 `needed` ：false
* `thetaStart` ：指定y轴从什么地方开始绘制   `needed` ：false
* `thetaLength` ：指定从thetaStart开始绘制多少  `needed` ：false

### CylinderGeometry

三维圆柱体

#### Main Properties

* `radiusTop` ：圆柱体顶部尺寸`needed` ：false
* `radiusbutton` ：圆柱体底部尺寸  `needed` ：false
* `height` ：圆柱的高度 `needed` ：false
* `segmentsX` ：设置沿x轴分多少段`needed` ：false
* `segmentsY` ：设置沿y轴分多少段 `needed` ：false
* `openEnded` ：指定网格顶部和底部是否封闭  `needed` ：false

### otherGeometry

还有很多高级的几何图形。

*learn more from `http://threejs.org/`*

## 有感而言

好吧我承认罗列了那么多并没有卵用，但是原谅我有点强迫症，总觉得没有这个不完整。
虽然没有列全我还是感到很不爽。
蟹蟹


