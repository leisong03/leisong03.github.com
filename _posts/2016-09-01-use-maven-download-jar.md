---
layout: post
title:  "使用Maven下载依赖包及使用Nexus搭建私服"
date:   2016-09-01 22:00:00 +0800
categories: Maven Nexus 
tags: Maven Nexus 
author: JiuYang Chen
---

* content
{:toc}

在项目的搭建过程中，所以很多时候我们需要上网去一个一个找项目的依赖包。这个过程确实是一个效率很低而且很让人恼火的事情。
很多时候能找到对应的官网下载固然是好，但是大部分时候我们都只能在一些第三方网站上下载`未知`的版本。
所以我们需要一个`Maven`。




## Maven使用

Maven核心自带的远程仓库，包括了绝大部分开源构件。我们可以很方便通过Maven提供的坐标机制便捷的下载到你心仪的依赖包。

### Maven下载、安装

下载地址: *http://maven.apache.org/download.html*

 配置环境变量 系统变量: `path：` `%MAVEN_HOME%\bin;`  `MAVEN_HOME` `G:\maven\apache-maven-3.3.9-bin\apache-maven-3.3.9` 到bin文件夹
 
 cmd:mvn -version 查看安装是否成功
 
 配置中央仓库：解压安装路径下    *apache-maven-3.3.9\lib\ maven-model-builder-3.3.9.jar*

 
### m2eclipse下载、安装

这里我已经默认了你安装了`JDK` 并且安装了`eclipse`

> `m2eclipse`下载是`eclipse`下一款Maven插件

  打开eclipse：  help --> Install New SoftWare --> add 
  
     `Name`:m2e  
     `Location`: *http://m2eclipse.sonatype.org/sites/m2e*
  
如果一直停留pending可以将`Contact all update sites during install to find required software` 取消勾选
  

### 新建一个Maven项目

    eclipse：new --> Maven --> MavenProject 
	
Tips:可能会卡住，因为Maven会生成项目骨架，天朝的网络下载是硬伤。`Properties`参数`archetypeCatalog=internal`

    
### 添加要下载的地址

这里以`Spring`为例：

*   你可以在官网download处找到

```xml

		<dependencies>
		    <dependency>
		        <groupId>org.springframework</groupId>
		        <artifactId>spring-context</artifactId>
		        <version>4.3.2.RELEASE</version>
		    </dependency>
		</dependencies>

```
    
*   你也可以使用仓库搜索网站： 

1.1 Sonatype Nexus

*http://repository.sonatype.org/*

2.2 Jarvana

*https://www.jarvana.com/ jarvana/*

3.3 MVNbrowser

*http://www.mvnbrowser.com*

4.4 MVNrepository

*http://mvnrepository.com*

 然后将`pom.xml`中的对应部分替换掉稍加等待，你就可以在项目中看到你需要的依赖包。
 
  ***
![outPut](http://ww1.sinaimg.cn/mw690/c584f169gw1f7f82a10wtj207r03z755.jpg)  
 

### 下载路径更换

本地仓库默认路径：*C:\Users\UserName\.m2\repository*

修改本地仓库的路径：*apache-maven-3.3.9\conf\settings.xml*

如：

```xml

    <localRepository>G:/maven/repository</localRepository>

```

### 下载Tips

下载`json-lib`

```xml

<!-- https://mvnrepository.com/artifact/net.sf.json-lib/json-lib -->
<dependency>
    <groupId>net.sf.json-lib</groupId>
    <artifactId>json-lib</artifactId>
    <version>2.2.2</version>
</dependency>

```

无法下载 要改成

```xml

<!-- https://mvnrepository.com/artifact/net.sf.json-lib/json-lib -->
<dependency>
            <groupId>net.sf.json-lib</groupId>
            <artifactId>json-lib</artifactId>
            <version>2.2.2</version>
            <classifier>jdk15</classifier>
</dependency>

```

## Maven 学习

### 自定义Jar

#### maven项目：编写主代码

   默认主代码目录：*src/main/java*
   运行主代码：根目录 
   
```   

    //clean:清理输出目录target/
    //compile:将主代码编译到target/classes
    $ mvn clean compile
    
```
       
       
#### maven项目：编写测试代码

  默认测试代码目录：*src/test/java*
  运行测试代码：
  
```   
    $ mvn clean compile
  
```     

#### 打包和运行

maven会在打包之前执行编译、测试等操作。

 打包和运行：
  
```     
  $ mvn clean install
```   

### maven 坐标 

maven 的一大功能是管理项目依赖，为了能自动化的解析任意一个Java构件（每个jar包可以当做一个构件）。maven使用坐标将它们唯一标示，每个构件都有一组坐标。
坐标的定义是根据元素：`groupId`、`artifactId`、`version`、`packaging`和`classifier`

* groupId:Maven项目隶属的实际项目
* artifactId:实际项目中的一个Maven项目
* version:该Maven当前所在的版本
* packaging:Maven项目的打包方式
* classifier:构件输出的一些附属构件

 坐标和路径大致的对应为：
*groupId/ artifactId/ version/ artifactId-version.packaging*

### maven 仓库 

 maven仓库可以分为两类：`本地仓库`和`远程仓库`
 
  远程仓库也可以分为`中央仓库`、`私服`和`其他公共库`。
  中央仓库是Maven核心自带的远程仓库，包括了绝大部分开源构件。
  私服是一种特殊的远程仓库，为了节省带宽和时间在局域网架设一个私有的仓库服务器，代理所有的远程仓库，内部的项目也可以部署到私服上供其他项目使用。 

### 依赖下载机制

当maven需要构件的时候，会先查看本地仓库，如果有直接使用。如果不存在会查看是否有最新版本的。如果还不存在则会去远程仓库下载需要的构件。

### 私服的部署

   私服是一种特殊的远程仓库，它是架设在局域网上的仓库服务，私服代理广域网上的远程仓库。私服一大作用就是可以部署第三方构件，供团队人员交流和使用。


## Nexus搭建私服

### Nexus下载、安装

下载地址：*https://nexus.sonatype.org/downloads/*

安装完成了访问：*http://localhost:8081/nexus* 即可看到首页。

### 修改监听端口和ip

在安装目录下：*Nexus\nexus-pro-trial-installer-2.13.0-01\conf\nexus.properties*

`application-port` ： nexus的监听端口

`application-host` ： nexus的监听ip地址，如果使用nginx或apache等其他http代理，可将该ip地址修改为`localhost`或`127.0.01`，增强安全性. 

### 登录

默认账号：`admin` 

密码：`admin123`

### 仓库属性

#### 仓库类型（type）

* grounp(仓库组)
* hosted(宿主)
* peoxy（代理）
* virtual(虚拟)	

#### 仓库策略（Policy）：

* Release（发布版本）
* Snapshot（快照版本）

### 配置Maven从Nexus下载构件
   
```xml
 
<project>
...
<properties>
     <repository>
         <id>nexus</id>
         <name>Nexus</name>
         <url>http://localhost:8081/nexus/content/groups/public/</url>
         <releases><enabled>true</enabled></releases>
         <snapshots><enabled>true</enabled></snapshots>        
     </repository>  
</properties>
<pluginRepositories>
   <pluginRepository>
         <id>nexus</id>
         <name>Nexus</name>
         <url>http://localhost:8081/nexus/content/groups/public/</url>
         <releases><enabled>true</enabled></releases>
         <snapshots><enabled>true</enabled></snapshots>        
   </pluginRepository>
</pluginRepositories>
...
</project>

```