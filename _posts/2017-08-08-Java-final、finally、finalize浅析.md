---
layout:     post                    # 使用的布局
title:      Java-final、finally、finalize浅析           # 标题 
subtitle:   Java-final、finally、finalize浅析 
date:       2017-08-08              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-os-metro.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java
---

##  final

final可以修饰属性、方法、类，分别代表属性不可变、方法不可覆盖、类不能被继承

**final修饰属性**：被final修饰的变量不可变。但是不可变有两种含义，一种是引用不可变、一种是对象不可变。final在这里指的是引用不可变，所以，被final修饰的属性必须初始化，初始化有以下几种方式：

 1. 在定义的时候初始化
 2. 可以在初始化块中初始化，但是不能再静态初始化块中初始化
 3. 静态final成员变量可以在静态初始化块中初始化，不能再初始化块中初始化
 4. 在类的构造器中初始化（构造函数），静态final成员属性不可以

**final修饰方法**：不允许任何子类重写这个方法，但是可以使用这个方法。内联机制：当调用一个被final修饰的方法时，直接将方法插入到调用处，而不是方法的调用。
**final修饰参数**：用来表示这个参数在函数内部不允许被修改。
**final修饰类**：表示类不能被继承，所有的方法都不能被重写。但是final类的成员变量是可以改变的。例如：String、StringBuffer


finally
-------
作为异常处理的一部分，用在try/catch块中
经常被用在需要释放资源的情况下。
例如连接数据库时，结束后需要关闭连接，但是有可能发生异常，没有关闭连接，造成资源的浪费，就可以用finally执行关闭连接的部分。


finalize
--------
Object类中的方法，在垃圾回收器执行时会调用此方法，可以覆盖此方法来实现对其他资源的回收，例如关闭文件等。
[这里有Object类源码的解读](http://blog.csdn.net/fushaonian/article/details/63687855)

