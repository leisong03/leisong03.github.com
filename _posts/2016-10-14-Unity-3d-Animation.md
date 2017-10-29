---
layout: post
title:  "Unity3d 动画系统"
date:   2016-10-14 20:00:00 +0800
categories: Unity3d C#
tags: Unity3d C#
author: JiuYang Chen
---

* content
{:toc}


`Unity3d`学习笔记 本篇简单介绍`Unity3d`中的动画系统及其使用。





## 模型导入


> 以带动画的`FBX`文件为例，`unity`版本为`5.0`以上版本。

### 新版动画系统

当你导入`FBX`文件后，会发现模型具有一个`Animator`(状态机)组件。

![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f8taf5py8xj20970b4q3k.jpg)


我们一般将它成为（新）动画系统，新动画系统和旧版的动画系统（`unity3d 4.3`之前版本）的区别就在于使用了`Animator`组件代替了原来的`Animation`。

### 旧版动画系统

同理当你使用的是旧版的动画系统就会发现是`Animation`组件。

![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f8taf6qo3vj20960b43zb.jpg)


在高版本的unity3d你可以通过设置模型的`Rig`选项设置动画的类型。


参数 `Animation type`

* `Legacy`： 旧版本的动画
* `Generic`： 通用的动画
* `Humanoid`： 类人动画

![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1f8taf4saovj20970b3q3d.jpg)

## 动画导入

* 动画分割 `Animation Clips`

你可以将动画分割成若干个独立的动画。

参数 `Loop Time` :是否循环播放。

![outPut](http://ww4.sinaimg.cn/mw690/c584f169gw1f8taf31h86j20990ewmy2.jpg)

* 多个模型文件

你也可以使用多个模型文件导入动画，命名约定——`"模型名称"@"动画名称".fbx`

如： `test.fbx` ,`test@idle.fbx` ,`test@move.fbx`



## 动画的播放和使用

### Animator

你需要在项目中新建一个`Animator Controller`来控制动画的播放。
然后双击切换到`Animator`视图

你会发现有三个默认状态
 
* `Entry`: Animator的入口

* `Any State`：任意的状态

* `Exit`： 退出状态

我们添加两个状态用来表示两个分别不同的动画，分别名为`idle`和`move`。

`idle`和`Entry`状态连接表示`Animator`进来执行该动画，点击状态在`motion`选择要执行的动画。
一般入口状态为idle 动画，用来表示模型闲置的状态。


再将idle状态和move状态连接，箭头指向move，表示从闲置状态开始运动。
将move状态和idle状态连接，箭头指向idle，表示从运动状态转为闲置状态。


![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1f8taf84trdj20tz0a9tag.jpg)

动画的`Animator`（状态机）配置好了，我们需要添加一些参数来作为`Animator`在各个状态之间切换的条件。

在左上角的`Prameters`中添加两个`bool`变量，分别表示模型闲置和移动两种状态。

`Prameters`参数类型：

* `Float`：float类型的参数，多用于控制状态机内部的浮点型参数。

* `Int`：int类型的参数，多用于控制状态机内部的整型参数。

* `Bool`：bool类型参数，多用于状态切换。

* `Trigger`：本质上也是一个bool类型的参数，但是其值默认为false，且设置为true后系统会自动将其还原为false。


设置 `Idling ：true  Moving : false `

点击箭头，为状态的转换切换条件

参数 `Has Exit Time`：表示在动画播放完才会切换到下一动画。

![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1f8tahwfw92j20u10a5gnh.jpg)



最后使用代码控制动画：

```js

using UnityEngine;
using System.Collections;

public class test : MonoBehaviour {

	private Animator _animator;
	
	void Start()
	{
		_animator = this.GetComponent<Animator>();
	}
	
	void Update()
	{
		if( Input.GetKeyDown("q") )
		{
			_animator.SetBool ("Idling",false);
			_animator.SetBool ("Moving",true);
		}
		if( Input.GetKeyDown("w") )
		{
			_animator.SetBool ("Idling",true);
			_animator.SetBool ("Moving",false);
		}
	}
}



```

### Animation

你可以通过 `play Automatically`选择是否自动播放或是

代码控制：


```js

using UnityEngine;
using System.Collections;

public class test : MonoBehaviour {

		
	Animation _animation;
		
		void Start() {
			
		_animation = GetComponent<Animation>();


		}

	void Update(){
		
		if( Input.GetKeyDown("q") )
		{
			_animation.Play("Take 001");
		}
		if( Input.GetKeyDown("w") )
		{
			_animation.Play("Take 0010");
		}
	}

		
}



```
































