---
layout:     post                    # 使用的布局
title:      Java-浅谈clone方法           # 标题 
subtitle:   Java-浅谈clone方法 
date:       2017-08-03              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-swift2.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java
---

#### 首先阐述一个概念：

> Java在处理基本数据类型（例int,char,double等）时，都是采用按值传递（传入的是输入参数的复制）的方式执行，除此之外的其他类型都是按引用传递（传递的是一个对象的引用）的方式执行。


在实际的操作中，可能需要一个与已存在对象相同的另一个对象，并且对另一个对象的修改不会影响先前的对象，于是Java提供了一个clone()方法实现这种需求。这种方法返回的Object对象的复制，而不是引用。

#### 下面阐述一下clone()方法的使用步骤：

 1. 首先需要继承Cloneable接口。而Cloneabe只是一个标志接口，没有任何方法的。下面贴上Cloneable的源码。
```java
package java.lang;
/**
 1. 没有任何方法，知识一个标志接口。
 2. @author liang
 3.  */
public interface Cloneable {
}
```
 2. 在类中重写clone()方法。
 3. 在clone()方法中调用super.clone()方法，super.clone()调用的是Object中的clone方法，Object中的方法是这么定义的：`protected native Object clone() throws CloneNotSupportedException;`。
 4. 把浅复制的引用指向新的克隆体。
 5. 深复制每一个对象，或者说是引用的对象。



----------
下面说一下浅复制与深复制的区别：
**浅复制：**复制后的所有变量都拥有与以前对象相同的值，而所有对其他对象的引用，仍然指向之前的对象。简单地说，浅复制之后，对对象的引用没有变。
**深复制：**复制后的所有变量都拥有与以前对象相同的值，那些引用其他对象的变量指向被复制的新对象，而不再是被引用的对象。简单地说，深复制把变量引用的对象也复制了一遍。

