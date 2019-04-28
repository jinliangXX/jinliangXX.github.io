---
layout:     post                    # 使用的布局
title:      Java-instanceof           # 标题 
subtitle:   Java-instanceof解析 
date:       2017-06-08              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-ios9-web.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java

---

###  instanceof是Java语言中的一个二元运算符，作用是判断变量所指的对象是否是一个类（接口、抽象类、父类）的实例。

返回值为boolean，即TRUE或者FALSE；

```java
import java.awt.List;

public class Main {
	
	public static void main(String[] args){
		String hello = "Hello World!";
		List list = new List();
		if(hello instanceof String){
			System.out.println("是String的一个实例。");
		}
		if(list instanceof Object){
			System.out.println("是Object的一个实例。");
		}
	}
}
```
**执行结果：**
![instanceof](http://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-04-28-060908.png)

