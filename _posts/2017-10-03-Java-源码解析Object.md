---
layout:     post                    # 使用的布局
title:      Java-源码解析Object           # 标题 
subtitle:   Java-源码解析Object 
date:       2017-10-03              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-huoying.jpeg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java
---

#### 首先，粘贴上Object的源代码：

```java
package java.lang;

public class Object {
    private static native void registerNatives();
    static {
        registerNatives();
    }

    public final native Class<?> getClass();

    public native int hashCode();


    public boolean equals(Object obj) {
        return (this == obj);
    }

   
    protected native Object clone() throws CloneNotSupportedException;

 
    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }


    public final native void notify();

 
    public final native void notifyAll();

    
    public final native void wait(long timeout) throws InterruptedException;

    
    public final void wait(long timeout, int nanos) throws InterruptedException {
        if (timeout < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }

        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException(
                                "nanosecond timeout value out of range");
        }

        if (nanos > 0) {
            timeout++;
        }

        wait(timeout);
    }

    
    public final void wait() throws InterruptedException {
        wait(0);
    }

    
    protected void finalize() throws Throwable { }
}

```

#### 分析一下Object类包含的方法（构造函数为默认无参）：

 1. `private static native void registerNatives();`
   这是其中的第一个方法，它并没有方法体，而且存在关键字native，代表着*Java中，用native关键字修饰的函数表明该方法的实现并不是在Java中去完成，而是由C/C++去完成，并被编译成了.dll，由Java去调用*，因此此处我们并不需要关心，只需要知道此方法的作用是调用操作系统的方法就好，不同的操作系统会有不同的方法。
   虽然是用private修饰，并且没有内容，但是接下来Java就对它进行了静态编译，看源码：
   `static {
        registerNatives();
    }`

 2. `public final native Class<?> getClass();`
 它也是一个native方法，作用是返回正在运行的类。与Object.class相同。例如在反射机制时，获得Class类的途径之一就是“实例名.getClass”。

 3. `public native int hashCode();`
 返回一个整型（int）数字，表示对象的哈希码值。此方法的作用是增强哈希表的性能。
 官网给出的约定为：
 1.只要程序在运行，同一个类返回的哈希码值是相同的，前提是equals方法的信息标尺未作修改。当程序的一次执行到另一次执行时，程序的哈希码值无需保持相同。
 2.如果两个对象通过equals比较后相同，那么哈希码值一定相同。
 3.如果两个对象的哈希码值相同，那么对象不一定相同；如果哈希码值不相同，那么一定不相同。

 至于为什么会增强哈希表的性能，比如在set中，如果没有hashcode，那么需要遍历set表，然后与要存入的对象equals，如果没有相同的则存入set；有了hashcode后，则可以查到对象的哈希码值，然后根据哈希算法算出此对象的位置，判断此位置是否已有对象。

 4. 
```java
public boolean equals(Object obj) {
        return (this == obj);
    }
```
这个方法返回的是布尔值，表示判断对象的内容是否完全相同。但是在此处，我们可以观察到equals与==的作用是完全相同的，着颠覆了我们开始学习时的常识“==表示的是变值完全相同，equals表示的是内容相同”，但是此处与==一样，又为什么引用equals呢？为什么我们平时字符串比较内容用的是equals而不是==呢？
原来是因为在Object类中，他们是等价的，但是在字符串或者我们自己定义的类中，当我们要判断地址不同的对象其中包含的内容是否相同时使用equals,是因为在字符串中或者我们自己编写的类中，equals方法被覆盖了，也就是说判断对象相等的标尺改变了。
但是实际使用时应注意，改写了equals方法，必然要改写hashcode方法，详见上一解析。

 5.  `protected native Object clone() throws CloneNotSupportedException;`
    此方法的声明也是native，因此clone方法也不是原生的Java方法，而是用c/c++编写，然后用Java调用的。
    clone方法返回到的是一个引用，指向的是新的对象，与被克隆的对象处于不同的堆空间。
    Cloneable是一个标志接口，不存在任何方法，如果复制前不实现Cloneable接口，会抛出CloneNotSupportedException。
  
 6. 
```java
public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
```
这个方法是我们经常用到的方法之一，当我们使用System.out.println(cla)已经隐示的调用了它，这个方法经常被覆盖，在Object中，他的返回值是“类的名称+以16进制无符号整数形式返回此哈希码”。
 7. 

```java
public final native void wait(long timeout) throws InterruptedException;
public final void wait() throws InterruptedException {
        wait(0);
    }
public final void wait(long timeout, int nanos) throws InterruptedException {
        if (timeout < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }

        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException(
                                "nanosecond timeout value out of range");
        }

        if (nanos > 0) {
            timeout++;
        }

        wait(timeout);
    
```
这三个方法是多线程中的方法，调用此方法所在的当前线程等待，知道其他线程调用notify()/notifyAll()方法或者到达时间。
wait(long timeout)首先是这个方法，我们看到是native的，毫无疑问，也是本地的方法，其他wait方法，如果仔细观察，发现调用的是此方法，因此，所有的wait方法都是调用本地的或者说是系统的方法。
wait()方法的原理是，或者说等同于wait(0).
 wait(long timeout, int nanos),代表更精确的控制，timeout的单位是毫秒，nanos的单位是纳秒，当nanos>0时，也做一毫秒处理。


 8.
```java
public final native void notify();

 
public final native void notifyAll();
```
唤醒在此对象监视器上等待的单个线程/所有线程。
多线程将在后面讲解。

9.`protected void finalize() throws Throwable { }`
这个方法与Java的垃圾回收机制有关，此方法将在JVM回收时进行调用，并不是认为调用的。