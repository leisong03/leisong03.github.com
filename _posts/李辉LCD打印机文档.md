#李辉LCD打印机文档

## 问题

1. 1000层司机

2. 无法打印大模型

## 读代码

   ```
   ModelListActivity
   ```

InjectView是干啥的：

是一个黄油刀 Butter Knife是高手向的东西，我先不改了

```java
private volatile String currentStlName = null;
private volatile String currentGcodeName = null;
```

这两个是读取stl文件和gcode文件，从cws里面读取

slc是不需要读取的

