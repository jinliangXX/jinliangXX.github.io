---
layout:     post                    # 使用的布局
title:      Java-static关键字的作用           # 标题 
subtitle:   Java-static关键字的作用解析 
date:       2017-06-29              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-kuaidi.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java

---

###  **static关键字的两个作用：**

 1. 为数据类型或者对象分配单一的存储空间，与创建对象的个数无关。
 2. 将属性或者方法与类绑定在一起，也就是可以不创建对象的情况下直接通过类调用属性和方法。



**static的四种使用情况：**


 ***- static修饰成员变量***

 Java中没有全局的概念，但是通过static可以达到全局的效果。
 Java提供两种类型的变量：用static修饰的静态变量和不用static修饰的实例变量。
 静态变量属于类，在内存中有且只有一个复制，只要静态变量所在的类被加载，那么静态变量就会被分配空间。
 对静态变量使用的两种方式：类.静态变量       对象.静态变量
 **不能再成员函数内部定义static变量**

---

 ***- static修饰成员方法***


 static修饰方法与static修饰属性类似，同样可以通过类直接调用，不需要对象的实例化。

 **注意：** static静态方法不能使用this和super，不能调用非static方法和变量。因为static方法被调用时，类的对象还没有被创建，即是被创建了，也无法确定调用哪儿个对象的方法。（和类绑定，而不是对象）

static的一个用途是实现单例模式，下面演示线程安全的单例模式。

```java
package 设计模式;

public class Singleton {
	private Singleton(){}
	
	private static Singleton singleton = null;
	
	public static Singleton getInstance(){
		if(singleton == null){
			synchronized (Singleton.class) {
				if(singleton == null){
					singleton = new Singleton();
				}
			}
		}
		return singleton;
	}
}
```

----------


 ***- static修饰代码块***

static代码块是类中独立于成员变量和成员函数存在的。JVM在加载类时会执行静态代码块（不是类的实例化时），如果有多个静态代码块，将会按照顺序执行，需要注意的是，静态代码块只会执行一次。

----------

***static修饰内部类***
static静态内部类不依赖于类的实例化而实例化，它不能与外部类拥有相同的名字，不能访问外部类的静态方法和变量，只能访问外部类中的静态成员和静态方法。
关于内部类，详细请转到：[内部类详解](http://blog.csdn.net/fushaonian/article/details/66967877)

