---
layout: post
title:  "Unity3d Final IK 插件的使用"
date:   2016-10-12 20:00:00 +0800
categories: Unity3d C#
tags: Unity3d C#
author: JiuYang Chen
---

* content
{:toc}


本篇简单介绍`Unity3d` 插件 `Final IK`的使用




## `Final IK`简介

`Final IK`是一个反向动力控制插件。(`unity`版本为`5.1`及以上版本)

反向动力控制：通俗的将就是通过子骨骼,推导出其所在骨骼链上n级父骨骼位置，从而确定整条骨骼链的方法。所以你只需要控制子骨骼进行运动操作。

### `Final IK`IK类型

`Final IK`为我们提供了很多IK类型供选择,主要的IK类型。

* [Aim IK](http://www.root-motion.com/finalikdox/html/page1.html):是CCD算法的改进版本。可以设置一个目标,使关节末端始终瞄准该目标,可以用于`武器的瞄准`。它可以消除单一的向前瞄准，甚至可以瞄准角色的背后

* [Biped IK](http://www.root-motion.com/finalikdox/html/page2.html):它可以自动检测两足动物的骨骼,而且包括一个头部IK控制。可以使人物的姿势更加的自然。

* [CCD IK](http://www.root-motion.com/finalikdox/html/page3.html):将每个关节和末端对齐,使得最后一个骨骼迭代更接近末端。可以用来制作尾巴、机械臂(工业机器人)。

* [FABR IK](http://www.root-motion.com/finalikdox/html/page4.html):类似于CCD IK,但是相较来说每个关节不需要和末端对齐。所以更加的自由。可以用来制作绳索。

* [Limb IK](http://www.root-motion.com/finalikdox/html/page7.html):主要是用于3段手和腿或是其他肢体类型。

* [Look At IK](http://www.root-motion.com/finalikdox/html/page8.html):主要用于旋转一系列骨骼面向目标物体。

### `Final IK`的使用

#### 手动添加`CCD IK`

以CCD IK为例

* 首先我们在`3Dmax`创建一个简单的机械臂。

![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1f9jtu3zxhej20tf0b1js9.jpg)


* 我们给Axis1添加一个`CCDIK` 你可以在插件的 `RootMotion --> FinalIK --> IK Components` 位置找到。然后依次添加三个`Bones`。

![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f9jtu6p07sj2089088wev.jpg)

ps： 你也可以通过添加不同的限制属性来限制每个轴的运动。

![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f9jtu9el3gj206e08wq32.jpg)

如图我们给Axis2添加了一个角度限制，限制它只能在荧光区域进行旋转。

![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1f9jtuah3l9j20un0a975n.jpg)

* 最后我们创建一个脚本来控制IK。

```c#

using UnityEngine;
using System.Collections;
using RootMotion.FinalIK;

public class CCD : MonoBehaviour {

	CCDIK ccdIK;

    // 获取ccdIK 
	void Start () {
		ccdIK = GetComponent<CCDIK> ();
	}
	
	// 设置ccdIK解算器的x 坐标为 -10
	void LateUpdate () {
		ccdIK.solver.IKPosition.x = -10;
	}
}


```

绑定并运行，我们发现棍子可耻的弯了。

![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1f9jtu86bvej20m50a40t0.jpg)

上面的那个就是`IK解算器`。


#### 运行时添加`CCD IK`

* 我们创建一个脚本

然后绑定到我们的棍子上，然后运行它又弯了。

```c#

using UnityEngine;
using System.Collections;
using RootMotion.FinalIK;

public class CCD2 : MonoBehaviour {

	Transform obj1;
	Transform obj2;
	CCDIK ccdIK;

	void Start () {
	
	    //给绑定的物体添加一个`CCDIK`
		gameObject.AddComponent<CCDIK>();

        //获取要添加的其他两个`bones`
	    obj1 = GameObject.Find ("Axis_2_2").transform;
		obj2 = GameObject.Find ("Axis_2_2/Axis_2_3").transform;
		
		Transform[] tran = new Transform[] {gameObject.transform, obj1, obj2 };
	
		ccdIK = gameObject.GetComponent<CCDIK> ();
		
		// 使用`ccdIK.solver.SetChain()`绑定骨骼
		ccdIK.solver.SetChain (tran,gameObject.transform);
	}

	void Update () {
		ccdIK.solver.IKPosition.x = -2;
	}
}


```


![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f9jtub0zjhj20ur0emtab.jpg)






























