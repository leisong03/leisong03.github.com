---
layout: post
title:  "Unity 3d 几种Update"
date:   2016-11-08 11:00:00 +0800
categories: Unity3d C#
tags: Unity3d C#
author: JiuYang Chen
---

* content
{:toc}


本篇简单介绍`Unity3d`中几种`Update`方法的区别。






## `Update`方法

所有

### `Update`

`Update`是在每次渲染新的一帧的时候才会调用，也就是说，这个函数的更新频率和设备的性能有关以及被渲染的物体（可以认为是三角形的数量）。在性能好的机器上可能fps 30，差的可能小些。这会导致同一个游戏在不同的机器上效果不一致，有的快有的慢。因为Update的执行间隔不一样了。

### `FixedUpdate`

`FixedUpdate`是在固定的时间间隔执行，不受游戏帧率的影响。Tips：在处理Rigidbody的时候最好用`FixedUpdate`。


`FixedUpdate`设置：`Edit --> Project Settings --> Time`

![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1f9l0nci75fj20cc05674c.jpg)

### `LateUpdate`

`LateUpdate`是在所有`Update`函数调用后被调用。这可用于调整脚本执行顺序。

### `Update`和`FixedUpdate`的区别

* FPS = 2;

```c#

using UnityEngine;
using System.Collections;

public class FPS : MonoBehaviour {
	
	void Awake() {
		Application.targetFrameRate = 2;
	}

	void Update () {
		
		Debug.Log ("Update");
		
	}
	
	void FixedUpdate () {
		
		Debug.Log ("FixedUpdate");
		
	}
}


```

![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f9l0nb3pc4j20oc07ymxg.jpg)


* FPS = 60;

```c#

using UnityEngine;
using System.Collections;

public class FPS : MonoBehaviour {
	
	void Awake() {
		Application.targetFrameRate = 60;
	}

	void Update () {
		
		Debug.Log ("Update");
		
	}
	
	void FixedUpdate () {
		
		Debug.Log ("FixedUpdate");
		
	}
}


```

![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f9l0nc8ndjj20of08874n.jpg)

































