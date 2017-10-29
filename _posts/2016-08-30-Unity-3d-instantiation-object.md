---
layout: post
title:  "Unity3d 实例化对象"
date:   2016-08-30 20:00:00 +0800
categories: Unity3d
tags: Unity3d C#
author: JiuYang Chen
---

* content
{:toc}



当我年轻的在`unity3D`的`C#`文件中使用`new`来创建一个对象的时候。

```c#

		public class Single : MonoBehaviour {
		
		   private Single m_single;

	       m_single = new Single();

		}
```

然后它警告了我

```c#

    ...   You are trying to create a MonoBehaviour using the 'new' keyword.  This is not allowed.MonoBehaviours can only be added using AddComponent() ...

```

而且你会发现`m_single`一直为空。它告诉我们当你继承了`MonoBehaviour`你就不能使用关键字`new`来实例化一个对象。

具体原来我也母鸡。

```c#

		public class Single : MonoBehaviour {
		
		 private Single m_single;
		
		
		  void Awake(){
		       m_single = this;
		   }
		
		
		  public static Single M_single(){
		       return m_single;
		   }
		}

```

好了现在这个`m_single`就是一个`Single`的实例化对象。