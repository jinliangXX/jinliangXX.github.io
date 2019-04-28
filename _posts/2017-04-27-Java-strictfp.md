---
layout:     post                    # 使用的布局
title:      Java-strictfp           # 标题 
subtitle:   Java-strictfp解析 
date:       2017-04-27             # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-e2e-ux.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java
---

strictfp是输入strict float point的缩写，指的是精确浮点，保证浮点数运算的准确性。JVM在进行浮点运算时，在不同的平台上结果可能会不同，但是只要以strictfp修饰一个类、接口、方法，那么在范围内，JVM会完全按照IEEE二进制浮点数算术标准来执行（IEEE 754）。

**注意：**
当一个类被strictfp修饰，那么所有的方法都会自动被strictfp修饰。

示例：

```
import java.awt.List;

public strictfp class Main {
	
	public static void main(String[] args){
		float a = 0.125f;
		double b = 2.25148567451d;
		System.out.println(a+b);
	}
}
```
结果：
![strictfp应用](http://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-04-28-060626.png)

