---
layout: post
title:  "Pointer Lock API"
date:   2016-09-03 09:00:00 +0800
categories: JavaScript
tags: JavaScript
author: JiuYang Chen
---

* content
{:toc}

Pointer Lock API 提供了基于鼠标移动随着时间的增量。
是3D游戏最常见的方式，你可以通过鼠标来控制视角，消除了在一个方向上的移动限制。



## 基本概念

指针锁定和 鼠标捕获有关。鼠标获指的是当鼠标拖动的时候持续发送事件，但是当鼠标按钮被放开时它就会停止。
指针锁定相较鼠标捕获在以下方面有所不同：

* 持久性。指针锁定不会释放鼠标，直到有一个明确的 API 调用或是用户使用一个专门的释放方法。
* 不局限于浏览器或者屏幕边界。
* 持续发送事件，而不管鼠标按钮状态。
* 隐藏光标。


## Function/Properties

### requestPointerLock()

Pointer Lock API 通过添加一个新方法扩展了 `dom`元素 `requestPointerLock()`

```js

    element.requestPointerLock = element.requestPointerLock || element.mozRequestPointerLock || element.webkitRequestPointerLock;
    
    element.requestPointerLock();
    
```

目前`requestPointerLock()`的实现和`requestFullScreen`以及`Fullscreen API`紧紧地绑在一起的。
保证在指针锁定之前进入了全屏模式。锁定指针的过程是异步。
使用`pointerlockchange`和`pointerlockerror`事件表示请求成功或是失败了。

### pointerLockElement/exitPointerLock() 

Pointer Lock API 也扩展了 `Document`接口，添加了新的属性和方法。

新的属性被用来访问当前绑定的元素，并将元素命名为`pointerLockElement`

```js

    document.pointerLockElement = document.pointerLockElement || document.mozPointerLockElement || document.webkitPointerLockElement;
    
    1.pointerLockElement可以用来确定是否有被指针锁定的元素
    
    if(document.pointerLockElement){
    
       }else {
    
    }
    
    2.pointerLockElement用来获取被指针锁定的元素
    
    if (document.pointerLockElement === someElement) {
 
       }
    
    
```

新的方法用来退出指针锁定

和`requestPointerLock()`一样也是异步的，使用`pointerlockchange` 和 `pointerlockerror`事件表示请求成功或是失败了。

```js

    document.exitPointerLock = document.exitPointerLock || document.mozExitPointerLock || document.webkitExitPointerLock;
    
    document.exitPointerLock();
    
```

### pointerlockchange

当指针状态发生变化的时候。如`requestPointerLock()`，`exitPointerLock()`时会触发`pointerlockchange`事件。这是一个简单事件所以不包含任何的额外数据。

* `Chrome`：`webkitpointerlockchange`
* `Firefox`：`mozpointerlockchange`

### pointerlockerror

当存在因调用错误`requestPointerLock()`或者`exitPointerLock()`，会触发`pointerlockchange`事件。这是一个简单事件所以不包含任何的额外数据。

* `Chrome`：`webkitpointerlockerror`
* `Firefox`：`mozpointerlockerror`

### 鼠标事件扩展

`Pointer lock API` 使用 `movement` 属性扩展了标准的`MouseEvent`。

` movementX`、` movementY`提供了鼠标在`X`和`Y`轴位置变化


### 锁定状态

当指针锁定被启动之后，正常的 `MouseEvent` 属性` clientX`, `clientY`, `screenX`, 和 `screenY` ，保持不变，就像鼠标没有在移动一样。 movementX 和 movementY 属性持续提供鼠标的位置变化。如果鼠标在一个方向上持续移动，movementX 和 movementY的值是没有限制的。不存在鼠标光标的概念，而且光标无法移到窗口之外，而且也不会被屏幕边缘所固定。

### 未锁定状态

无论鼠标锁定状态是怎样的，` movementX` 和 `movementY` 参数一直有效，并且为了方便起见，甚至在未锁定状态也是有效的。

当鼠标被解除锁定，系统光标可以退出并重新进入浏览器窗口。如果发生这种情况，movementX 和 movementY 可能会被设置成0。

### iframe 的限制

指针锁定一次只能锁定一个 iframe。如果你锁定了一个 iframe，你不能试图锁定另外一个 iframe 然后把目标转移到这个 iframe 上；指针锁定将会出错。为了避免这一问题，首先解锁那个锁定的 iframe，然后再锁定另外一个。

在` iframe` 默认的情况下， `sandboxed` iframes 会阻止指针锁定。避免这种限制的能力，即以属性/值 `<iframe sandbox="allow-pointer-lock">` 组合的形式 , 有望很快在 Chrome 中出现。














