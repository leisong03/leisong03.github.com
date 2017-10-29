---
layout: post
title:  "three.js-particle"
date:   2016-08-16 20:00:00 +0800
categories: three.js
tags: three.js javaScript WebGL
author: JiuYang Chen
---

* content
{:toc}

Three.js学习笔记 本篇介绍粒子、粒子系统和精灵。



## 粒子

`THREE.Particle`（粒子）和大多数的`Mesh`对象一样都属于`Object3D`对象的扩展。
创建一个粒子只需要传入一个材质参数。材质可为`ParticleBasicMaterial`或是`ParticleProgramMaterial`

```js
 
    var material = new THREE.ParticleBasicMaterial();
    
    var particle = new THREE.Particle(material);
    
``` 

但是这是相对于`CanvasRenderer`渲染器，使用`WebGLRenderer`创建粒子并没有效果。

## 粒子系统

如果使用`WebGLRenderer`则需要`THREE.ParticleSystem`。
创建一个粒子系统需要传入几何体和材质两个参数。

```js
    
    var geometry = new THREE.Geometry();
    
    var material = new THREE.ParticleBasicMaterial({
      
         //
    });
    
    var particle = new THREE.Particle(geometry, material);
    
``` 

## 精灵

`THREE.Sprite`精灵对象类似于广告牌总是面向镜头。
和粒子一样也只需要传入一个材质参数`THREE.SpriteMaterial`。

```js
    
    var material = new THREE.SpriteMaterial({
    
        //
    });
    
    var sprite = new THREE.Sprite( material);
    
``` 

