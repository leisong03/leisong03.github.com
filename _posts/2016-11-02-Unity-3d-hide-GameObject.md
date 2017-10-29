---
layout: post
title:  "Unity 3d 隐藏GameObject"
date:   2016-11-02 11:00:00 +0800
categories: Unity3d C#
tags: Unity3d C#
author: JiuYang Chen
---

* content
{:toc}


本篇简单介绍`Unity3d`中隐藏`GameObject`的三种方式。






## 隐藏`GameObject`

`Unity3d 版本5.x`

### `Destroy`方法

`Destroy`方法顾名思义是销毁的意思。使用该方法可以将改GameObject从场景中销毁，来隐藏物体。

```c#

   public GameObject obj;
   
   //销毁物体
   Destroy(obj);

```

### `Renderer`的`enabled`属性

你可以通过`GameObject`的`Renderer`的`enabled`属性的布尔值来控制场景是否渲染该物体。


```c#

   public GameObject obj;
   
   //隐藏物体
   obj.GetComponent<Renderer>().enabled = false;
   
   //显示物体
   obj.GetComponent<Renderer>().enabled = true;


```

当你设置为`false`

![outPut](http://ww3.sinaimg.cn/mw690/c584f169gw1f9i5xg9prwj207f047t8s.jpg)




### `SetActive`方法

众所周知每个`GameObject`都有一个`active`属性,你可以通过`SetActive`方法来选择是否激活该物体，`activeSelf`方法可以用来来查看物体的当前局部的激活状态。

```c#

   public GameObject obj;

   //隐藏物体
   obj.SetActive(false);
   
   //显示物体
   obj.SetActive(false);
   


```

![outPut](http://ww2.sinaimg.cn/mw690/c584f169gw1f9i5xgrwgjj20aw03wglq.jpg)


#### Tips

你的父物体通过`SetActive`设置为不激活的状态的时候，并不影响子物体的激活状态。但是子物体也不会显示因为被父物体覆盖。
所以`SetActive`只会影响到自身的激活状态。


### 隐藏子物体

1.通过`Active`隐藏子物体在`Unity3d 版本4.0`之前是可以使用`SetActiveRecursively`方法的。

如果你现在`Unity3d 版本5.x`控制整个物体子物体的激活状态,则需要进行一次遍历遍历父物体的所有子节点。


```c#

     private void SetChildrenActive (GameObject obj, bool active) {
     		
     		for(int i=0; i < obj.transform.childCount; i++) {
     			
     			GameObject childObj = obj.transform.GetChild(i).gameObject;
     			childObj.SetActive (active);
     			
     		}
     	}


```

2.同样的你也可以使用同样的过程使用`Renderer.enabled`来控制。








































