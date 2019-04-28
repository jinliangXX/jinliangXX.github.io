---
layout:     post                    # 使用的布局
title:      Java-Collection与Collections的区别           # 标题 
subtitle:   Java-Collection与Collections的区别解析 
date:       2017-06-04              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-ioses.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java
---



**Collection**是一个集合接口。

它提供对集合对象进行基本操作的通用接口方法，实现该接口主要有list、set、map等，该接口的设计目标是为各种具体的集合提供最大化的同意操作方式。

**Collections**是针对集合类的一个包装类。

它提供一系列静态方法以实现对各种集合的搜索、排序、线程安全化等操作，其中大多数方法用来存储线性表。它就是一个工具类，为Collection框架服务。

