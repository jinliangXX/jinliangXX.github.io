---
layout:     post                    # 使用的布局
title:      Java-String、StringBuffer、StringBuilder、StringTokenizer区别           # 标题 
subtitle:   Java-String、StringBuffer、StringBuilder、StringTokenizer区别解析 
date:       2017-05-09              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-ios9-web.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java

---

### 引言：

Java语言有四个类可以对字符或字符串操作，他们分别是**Character、String、StringBuffer、StringTokenizer**，其中Character是对于单个字符的操作，String用于字符串操作，属于不可变类，StringBuffer也用于字符串操作，不同之处是StringBuffer属于可变类。

 - ### **String与StringBuffer的区别**

String属于不可变类，String一旦被创建，就不能被修改，但是StringBuffer创建后还可以被修改。如果要保存一个经常被修改的字符串，应该用StringBuffer存储，如果用String存储，在字符串修改时会产生许多无用的对象，这些对象会被垃圾回收器回收，因此影响程序的性能，在大项目中体现明显。

两者初始化时不同，String实例化时，可以利用构造函数new String（String str），也可以利用String s = "abc"的方式来赋值。但是StringBuffer只能用构造函数new StringBuffer（“abc”）来初始化。

 - ### **String修改时的原理**

    首先创建StringBuffer对象，方式是利用StringBuffer的构造方法，传入String对象，然后调用append方法对其进行修改，最后调用toString方法把结果返回。


 - ### **StringBuilder与StringBuffer**

    StringBuilder与StringBuffer类似，但是StringBuilder不是线程安全的，如果只是在单线程中使用字符串缓冲区，那么StringBuilder效率更高。
     单线程使用StringBuilder，多线程使用Stringbuffer,因为StringBuffer在必要时可以对这些方法进行同步。


 - ### **三者比较**

    执行效率比较：
     StringBuilder > StringBuffer > String 
     操作的数据量小，优先使用String，单线程大数据量，使用StringBuilder，多线程操作大量数据，优先使用StringBuffer 。


 - ### **StringTokenizer**

    是用来分割字符串的工具类。