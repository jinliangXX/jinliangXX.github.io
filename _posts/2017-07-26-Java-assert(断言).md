---
layout:     post                    # 使用的布局
title:      Java-assert(断言)           # 标题 
subtitle:   Java-assert(断言)解析 
date:       2017-07-26              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-miui6.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java
---

**assert是一种软件测试的方法，提供了一种在代码中正确性检查的机制，目前很多开发语言都支持这种机制。**

它的作用是检查一个boolean表达式，程序的正确运行必须保证这个表达式为TRUE，返回false则代表程序不正常，系统需要提供告警信息并退出程序。

assert主要是用来保证程序的正确性，一般用于程序的开发和测试，为了提高程序运行的效率，软件发布后，assert检查默认是关闭的。

**assert的用法：**
包括两种表达式：

 1. assert expression
 2. assert expression： expressionOther
 expression 代表boolean表达式，expressionOther代表基本类型或者是一个对象。

对于Java程序，要用（-ea）去开启assert检查

**应用的范围：**

 1. 检查控制流
 2. 检查输入参数是否有效
 3. 检查函数结果是否有效
 4. 检查程序不变量

**assert与if的区别：**
assert是在调试程序时使用，不能用assert来控制程序的业务流程，因为去掉了assert意味着修改了程序的正常逻辑，但是if本身就是控制程序逻辑的。