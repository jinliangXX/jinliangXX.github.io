---
layout:     post                    # 使用的布局
title:      Java-“==”、“equals”、“hashCode”比较           # 标题 
subtitle:   Java-“==”、“equals”、“hashCode”比较解析 
date:       2017-04-09              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-ios10.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java

---

 - ### **“==”**

    **用来比较两个变量的值是否相等**
     比较变量对应内存中所存储的值是否相等，可以比较基本类型的数据或两个引用变量。
     如果比较基本类型的变量，可以直接使用“==”来判断值是否相等。
     如果比较引用变量，用“==”可以比较两个变量是否指向同一个对象，即判断两个变量所对应内存中的数值是否相等。但是不能比较两个对象的内容是否相等。


 - ### equals

    equals是Object类提供的方法之一。
     在没有覆盖equals方法之前，equals和==是相等的，比较的是引用。
     相比于==，equals方法的特殊之处在于它可以被覆盖，所有可以通过覆盖让它比较的不是引用，而是数据的内容，例如String方法中的equals，就是比较他们在堆中的内容。


 - ### hashCode

    hashcode方法是从Object类继承过来的，它用来鉴定两个对象是否相等。这个方法返回对象在内存中地址转换成一个int值，使用如果没有重写此方法，任何对象的hashcode（）都是不相等的。
     [Object中equals、hashcode方法详解](http://blog.csdn.net/fushaonian/article/details/63687855)