---
layout:     post                    # 使用的布局
title:      Java-round、ceil、floor           # 标题 
subtitle:   Java-round、ceil、floor解析 
date:       2017-04-08              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-js-version.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java

---

Math类是一个包含许多计算常量与数学方法的类，能自动导入，并且里面全是静态方法。
**round、ceil、floor**这三个方法就位于这Math类中。

----------

 - ### round方法表示四舍五入。

    实现原理是原数字先加上0.5再向下取整
     返回类型为int
     等同于：（int）Math.floor(x + 0.5F);
     例题：
     Math.round(11.5) = 12
      Math.round(-11.5) = -11

 - ### ceil方法表示向上取整

    实现原理就是取大于a的最小整数值
     返回类型为double
     例题：
     Math.ceil(11.5) = 12
      Math.ceil(-11.5) = -11

 - ### floor方法表示向下取整

    实现原理就是取小于a的最大整数值
     返回值是double
      例题：
     Math.ceil(11.5) = 11
      Math.ceil(-11.5) = -12