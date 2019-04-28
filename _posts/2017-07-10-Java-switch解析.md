---
layout:     post                    # 使用的布局
title:      Java-switch           # 标题 
subtitle:   Java-switch解析 
date:       2017-07-10              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-keybord.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java

---

#### switch用于多分支选择，使用时的规则是**switch（expr）**,其中expr只能是一下两种：

 - 枚举常量
 内部是由整型或者字符类型实现

 - 整数表达式
 整数表达式指的是基本类型int或者包装类Integer，也包括不同的长度整型，例如short。

----------
换一种说法，整型或者能够隐式转换为int类型的基本类型或者包装类可以，包括：
int，Integer，byte，Byte，short，Short，char，Character

不可以直接当做switch参数的有：long,float,double,String 
如果非要用他们，只能将其强制转换为int

----------
case后面接的可以使常量数值，也可以是常量计算式，但是不能使变量或者是带有变量的表达式。

----------

随着Java的发展，在Java 7中，switch开始支持String类型了。
**原理剖析：**通过对case后面的String对象调用hashcode()方法，返回int型的哈希码值，然后用哈希码值来唯一标识这个case，如果哈希码值匹配，会继续调用equals来进行判断，因此String的值和case后面的字符串都不能为null。

