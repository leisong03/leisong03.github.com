---
typora-copy-images-to: ../images
---

SLC开发文档

## 问题1 编译不对

![image-20180809083854135](/Users/lei/Documents/workshop/githubleisong03/images/image-20180809083854135.png)

根目录下的settings.gradle文件的内容应该是这个

```javascript
include ':app'
```

用完这个就可以正常编译了

## APK运行黑屏

![image-20180809084045182](/Users/lei/Documents/workshop/githubleisong03/images/image-20180809084045182.png)

程序的基本结构是这样的

jnilabs可以排除了，这个目录是用来放jni库的

cn.rayland这个域下面有6个子目录

![image-20180809084205555](/Users/lei/Documents/workshop/githubleisong03/images/image-20180809084205555.png)

我们一个一个看

# 看data.java

### 先看diskslcdataresource.java

```java
public class DiskSlcDataResource implements SlcDataResource {
```

这是个单例的程序

```java
private static DiskSlcDataResource instance;
```

构造函数是private的

```java
private DiskSlcDataResource(){
```

这个文件实现了下面这个接口

```java
public interface SlcDataResource {

    interface LoadDataCallback {
        void onDataLoaded(List<SlcData> datas);
        void onDataNotAvailable();
    }

    interface GetDataCallback {
        void onDataLoaded(SlcData data);
        void onDataNotAvailable();
    }

    void getDatas(@NonNull LoadDataCallback callback);

    void getData(@NonNull String dataPath, @NonNull GetDataCallback callback);

    void refreshDatas();

    void deleteAllDatas();

    void deleteData(@NonNull String dataPath);

}
```

##  看SlcData.java

这里面有两个文件

模型文件`modelFile`

支撑文件`supportFile`

里面`private void init(String file) {`对文件进行了解析

这里面对文件内容进行了解析

## SlcDataRepository.java

实现了下面这个接口

```java
public interface SlcDataResource {

    interface LoadDataCallback {
        void onDataLoaded(List<SlcData> datas);
        void onDataNotAvailable();
    }

    interface GetDataCallback {
        void onDataLoaded(SlcData data);
        void onDataNotAvailable();
    }

    void getDatas(@NonNull LoadDataCallback callback);

    void getData(@NonNull String dataPath, @NonNull GetDataCallback callback);

    void refreshDatas();

    void deleteAllDatas();

    void deleteData(@NonNull String dataPath);

}
```

这也是一个单例

这里面都是对slc文件的解析‘

# 看一下graphic这个文件夹



![image-20180810074416646](/Users/lei/Documents/workshop/githubleisong03/images/image-20180810074416646.png)



这里面有对点线面的定义

# 看一下remote文件夹

## command.java

不知道干啥的

## CommandSender.java

不知道干啥的

## Device.java

不知道干啥的

## DeviceManager.java

控制设备的

## DeviceSearcher.java

不知道干啥的

## NetQueryService.java

不知道干啥的

## SendCommandThreadFactory.java

不知道干啥的

# 看slapadnew文件夹

## base 文件夹看下

没啥有用的

# control 文件夹看下

没啥有用的

## hardwarePresent.java

这个非常有用，这个定义了所有的动作

# index 根本不知道干啥用的

# SLC 这个文件夹很有用的

## CalibrationData.java

 这里面的变量定义的非常清楚：

校准点个数，横竖各有一些，还有一个从编号到offset的映射

本质上这个函数对所有的点进行了一个排序

```java
private int xNum;
private int yNum;
private Map<Integer, Offset> offsets;
private int size;
private PointF startPointF;
private PointF endPointF;
private Offset wholeOffset;
```



## MuitlSlcDataSelector.java

这个作用还不明确

## multiSlcData.java

这个类是处理多个SLC文件排布的



## SLCtask.java

这个是打印相关的，里面区分了slc的打印任务

这是最重要的文件，目前看没有之一，一定要背下来

## 遇到问题怎么调

### 刮刀怎么调

![image-20180901113452029](/Users/lei/Documents/workshop/githubleisong03/images/image-20180901113452029.png)

现在的问题是start和end的位置不对

