---
layout: post
title:  " Android Activity 生命周期"
date:   2017-08-08 20:00:00 +0800
categories: Android 
tags: Android 
author: JiuYang Chen
---



* content
{:toc}





本篇简单学习`Android `Activity`生命周期的相关知识。

## `Activity`

`Android` 中所有的`Activity`都是由`Activity栈`来进行管理的。当切换到一个新的`Activity`时候，此`Activiy`会被压到栈顶部，之前的`Activity`会被压到栈底。

`Activity`的生命周期，可以从下方流程图看出。


![outPut](http://wx3.sinaimg.cn/mw690/c584f169ly1fkrcuarupuj20f50jrjsw.jpg)

1.1 一个`Activity`的完整生命周期 `onCreate -> onStart -> onResume -> onPause -> onStop -> onDestory`，称之为 `entire lifetime`.

2.2 当执行`onStart`回调方法的时候，`Activity`开始被用户所见。所以在`onCreate`的时候用户是看不到的，一直到`onStop`之前，此`Activity`都是可以被用户看到的。此阶段称之为 `visible lifetime`.

3.3 当执行`onResume`回调方法的时候，`Activity`可以开始和用户交互，一直到`onPause`方法之前。此阶段称之为`foreground lifetime`.


注：

* `onPause` , 虽然`Activity`还是可见的 但大多数时候意味着用户即将离开这个`Activity`.所以在`onPause`中，你可以用来 1.停止运行的浪费cpu的操作 2.提交没有保存的修改 3.释放系统资源
   
* `onDestroy` ,应该清除那些可能导致内存泄漏的地方。并且确保所有线程都被`destroyed`并且所有操作都被停止。
   
## `Activity` 操作

1.1 从 A Activity 到 B Activity，

打开 A Activity

**A onCreate -> A onStart -> A onResume**

2.2 点击切换Activity按钮，

**A onPause -> B onCreate -> B onStart -> B onResume -> A onStop**

如果点击 back 按钮，依次降执行 

**B onPause -> A onRestart -> A onStart -> A onResume -> B onStop -> B onDestroy**

B Activity从站顶弹出并销毁，此时 Activity 堆栈只有 A Activity。

3.3 如果再次点击 back 按钮

则执行 **A onPause -> A onStop -> A onDestroy**

4.4 如果点击的是 home 按钮

则执行 **A onPause -> A onStop**

注：

* Android手机，`开发者选项`中有一个`不保留活动`的设置。从**A Activity -> B Activity** 会执行 A onDestroy，但是 A Activity还是处在Activity栈中。

* 点击back 按钮 依次执行 **B onPause -> A onCreate -> A onStart -> A onResume -> B onStop -> B onDestroy**



### finish()

`finish()`方法主要有两层含义

* 将此`Activity`移除`Activity`栈

* 调用此`Activity`的`onDestroy`
















