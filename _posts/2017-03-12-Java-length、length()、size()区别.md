---
layout:     post                    # 使用的布局
title:      Java-Socket通信           # 标题 
subtitle:   Java-length、length()、size()区别介绍 
date:       2017-05-19              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-digital-native.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java

---

### length ###
length是属性，用来获取数组的长度。
在C/C++中，每当调用方法传递数组时，必须同时传递数组的长度，因为在C/C++中，方法调用时传递的参数为数组的首地址，而对数组的长度无法获知，可能会导致越界问题。


###length() ###
length()方法是针对字符串而言的，String提供的length()方法来计算字符串的长度。


## size() ##
size()方法是用来计算对象大小的方法，是针对泛型集合而言的，查看泛型中有多少个元素。
**泛型：**泛型是Java SE 1.5的新特性，泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。这种参数类型可以用在类、接口和方法的创建中，分别称为泛型类、泛型接口、泛型方法。 Java语言引入泛型的好处是安全简单。