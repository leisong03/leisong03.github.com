---
layout: post
title:  "run your blog system locally"
date:   2016-08-06 15:00:00
categories: ruby jekyll
tags: ruby jekyll
author: JiuYang Chen
---

* content
{:toc}




windows

## 安装 ruby 和 jekyll 环境

### ruby

* 使用[RubyInstaller](http://rubyinstaller.org/) 安装ruby环境，安装的时候可以勾选添加环境变量
 
* 安装devkit [RubyInstaller](http://rubyinstaller.org/) 在页面的下方就可以看到。选择和`ruby`对应版本的`devkit`

然后执行

```

    cd devkit
    ruby dk.rb init
    ruby dk.rb install

```

便安装完成了 ruby。

### jekyll

* 安装 `RubyGems`，`RubyGems`是一个 Ruby 包的管理工具。建议使用 [RubyGems 镜像- Ruby China](https://gems.ruby-china.org/) 安装 jekyll

* 在终端通过：

```

    $ gem install jekyll

```

来安装jekyll



