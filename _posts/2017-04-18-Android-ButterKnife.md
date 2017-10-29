---
layout: post
title:  "Android 高效的`InjectView – ButterKnife`"
date:   2017-04-16 14:00:00 +0800
categories: Android 
tags: Android
author: JiuYang Chen
---



* content
{:toc}





本篇简单一种高效的`View`绑定方式`InjectView – ButterKnife`。

## `InjectView`简介

传统的findViewById接收一个int类型的id参数，然后遍历找到对应的View但是写法过于麻烦。

InjectView 是一种基于反射的绑定方式。通过annotation，我们可以把int类型的id声明在对应的filed上面，通过

java的反射，遍历每个field,找到对应的id。

```java

@InjectView(R.id.textView) View textView;  

```

但是这个方法相对于正常的方法调用，在执行效率上会有些劣势，而且反射并不能编译时期的优化，使得性能的差距更加明显。

## `ButterKnife`简介

`ButterKnife`是一种比较高效的`InjectView`，它是通过`AnnotationProcessor`实现的`View Injection`而不是反射所以性能上没有什么顾虑。

> http://jakewharton.github.io/butterknife/ 


### `ButterKnife`使用

对于IDE的自动格式化代码，可能会强行将Annotation单独在一行显示。

```java

@InjectView(R.id.textView) 
View textView;  

```

你也可以进行相应的配置,IDE配置 （Android studio）

![outPut](http://wx2.sinaimg.cn/large/c584f169ly1ffo78mk09hj20qz0j3tbm.jpg)

* `注入`

`ButterKnife.inject(this);` 
在7.0.0版本中，注册方式也作了改变，变成了 
`ButterKnife.bind(this);`

*注:本文用的为`4.0`版本*

### `Activity`

```java

public class MainActivity extends Activity {

    @InjectView(R.id.tv_token)
    TextView tv_token;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        ButterKnife.inject(this);


    }

}

```

### `Fragment`

```java

 @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment, container, false);
        ButterKnife.inject(this,view);

        };

```

### `OnClick`

```java

    @OnClick({R.id.tv_token})
    void onClick(View view){
        switch (view.getId()){
            case R.id.tv_token:
                 Toast.makeText(this, "click!", Toast.LENGTH_SHORT).show();
                break;

        }
    }

```





















