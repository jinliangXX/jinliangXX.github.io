---
layout:     post                    # 使用的布局
title:      Scala语言入门           # 标题 
subtitle:   Scala语言入门 
date:       2019-11-25              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-lansehumian.jpeg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Scala
---

## 1. 简介

Scala is a modern multi-paradigm programming language designed to express common programming patterns in a concise, elegant, and type-safe way. It smoothly integrates features of object-oriented and functional languages.

Scala是一门现代的多范式语言，志在以简洁、优雅及类型安全的方式来表达常用的编程模型。它平滑地集成了面向对象和函数式语言的特性。

特性：

- Scala是面向对象的

  鉴于[一切值都是对象](https://docs.scala-lang.org/zh-cn/tour/unified-types.html)，可以说Scala是一门纯面向对象的语言。对象的类型和行为是由[类](https://docs.scala-lang.org/zh-cn/tour/classes.html)和[特质](https://docs.scala-lang.org/zh-cn/tour/traits.html)来描述的。类可以由子类化和一种灵活的、基于mixin的组合机制（它可作为多重继承的简单替代方案）来扩展。

- Scala是函数式的

  鉴于[一切函数都是值](https://docs.scala-lang.org/zh-cn/tour/unified-types.html)，又可以说Scala是一门函数式语言。Scala为定义匿名函数提供了[轻量级的语法](https://docs.scala-lang.org/zh-cn/tour/basics.html#函数)，支持[高阶函数](https://docs.scala-lang.org/zh-cn/tour/higher-order-functions.html)，允许[函数嵌套](https://docs.scala-lang.org/zh-cn/tour/nested-functions.html)及[柯里化](https://docs.scala-lang.org/zh-cn/tour/multiple-parameter-lists.html)。Scala的[样例类](https://docs.scala-lang.org/zh-cn/tour/case-classes.html)和内置支持的[模式匹配](https://docs.scala-lang.org/zh-cn/tour/pattern-matching.html)代数模型在许多函数式编程语言中都被使用。对于那些并非类的成员函数，[单例对象](https://docs.scala-lang.org/zh-cn/tour/singleton-objects.html)提供了便捷的方式去组织它们。

  此外，通过对提取器的一般扩展，Scala的模式匹配概念使用了[right-ignoring序列模式](https://docs.scala-lang.org/zh-cn/tour/regular-expression-patterns.html)，自然地延伸到[XML数据的处理](https://github.com/scala/scala-xml/wiki/XML-Processing)。其中，[for表达式](https://docs.scala-lang.org/zh-cn/tour/for-comprehensions.html)对于构建查询很有用。这些特性使得Scala成为开发web服务等程序的理想选择。

- Scala是静态类型的

  Scala配备了一个拥有强大表达能力的类型系统，它可以静态地强制以安全、一致的方式使用抽象。典型来说，这个类型系统支持：

  - [泛型类](https://docs.scala-lang.org/zh-cn/tour/generic-classes.html)
  - [型变注解](https://docs.scala-lang.org/zh-cn/tour/variances.html)
  - [上](https://docs.scala-lang.org/zh-cn/tour/upper-type-bounds.html)、[下](https://docs.scala-lang.org/zh-cn/tour/lower-type-bounds.html) 类型边界
  - 作为对象成员的[内部类](https://docs.scala-lang.org/zh-cn/tour/inner-classes.html)和[抽象类型](https://docs.scala-lang.org/zh-cn/tour/abstract-type-members.html)
  - [复合类型](https://docs.scala-lang.org/zh-cn/tour/compound-types.html)
  - [显式类型的自我引用](https://docs.scala-lang.org/zh-cn/tour/self-types.html)
  - [隐式参数](https://docs.scala-lang.org/zh-cn/tour/implicit-parameters.html)和[隐式转化](https://docs.scala-lang.org/zh-cn/tour/implicit-conversions.html)
  - [多态方法](https://docs.scala-lang.org/zh-cn/tour/polymorphic-methods.html)

  [类型推断](https://docs.scala-lang.org/zh-cn/tour/type-inference.html)让用户不需要标明额外的类型信息。这些特性结合起来为安全可重用的编程抽象以及类型安全的扩展提供了强大的基础。

- Scala是可扩展的

  在实践中，特定领域应用的发展往往需要特定领域的语言扩展。Scala提供了一种语言机制的独特组合方式，使得可以方便地以库的形式添加新的语言结构。

  很多场景下，这些扩展可以不通过类似宏（macros）的元编程工具完成。例如：

  - [隐式类](https://docs.scala-lang.org/overviews/core/implicit-classes.html)允许给已有的类型添加扩展方法。
  - [字符串插值](https://docs.scala-lang.org/overviews/core/string-interpolation.html)可以让用户使用自定义的插值器进行扩展。

- Scala的互操作

  Scala设计的目标是与流行的Java运行环境（JRE）进行良好的互操作，特别是与主流的面向对象编程语言——Java的互操作尽可能的平滑。Java的最新特性如函数接口（SAMs）、[lambda表达式](https://docs.scala-lang.org/zh-cn/tour/higher-order-functions.html)、[注解](https://docs.scala-lang.org/zh-cn/tour/annotations.html)及[泛型类](https://docs.scala-lang.org/zh-cn/tour/generic-classes.html) 在Scala中都有类似的实现。

  另外有些Java中并没有的特性，如[缺省参数值](https://docs.scala-lang.org/zh-cn/tour/default-parameter-values.html)和[带名字的参数](https://docs.scala-lang.org/zh-cn/tour/named-arguments.html)等，也是尽可能地向Java靠拢。Scala拥有类似Java的编译模型（独立编译、动态类加载），且允许使用已有的成千上万的高质量类库。

  

- [导言](https://docs.scala-lang.org/zh-cn/tour/tour-of-scala.html)
- [基础](https://docs.scala-lang.org/zh-cn/tour/basics.html)
- [统一类型](https://docs.scala-lang.org/zh-cn/tour/unified-types.html)
- [类](https://docs.scala-lang.org/zh-cn/tour/classes.html)

- [特质](https://docs.scala-lang.org/zh-cn/tour/traits.html)
- [元组](https://docs.scala-lang.org/zh-cn/tour/tuples.html)
- [通过混入（mixin）来组合类](https://docs.scala-lang.org/zh-cn/tour/mixin-class-composition.html)
- [高阶函数](https://docs.scala-lang.org/zh-cn/tour/higher-order-functions.html)
- [嵌套方法](https://docs.scala-lang.org/zh-cn/tour/nested-functions.html)
- [多参数列表（柯里化）](https://docs.scala-lang.org/zh-cn/tour/multiple-parameter-lists.html)
- [案例类（Case Classes）](https://docs.scala-lang.org/zh-cn/tour/case-classes.html)
- [模式匹配](https://docs.scala-lang.org/zh-cn/tour/pattern-matching.html)
- [单例对象](https://docs.scala-lang.org/zh-cn/tour/singleton-objects.html)
- [正则表达式模式](https://docs.scala-lang.org/zh-cn/tour/regular-expression-patterns.html)
- [提取器对象](https://docs.scala-lang.org/zh-cn/tour/extractor-objects.html)
- [For 表达式](https://docs.scala-lang.org/zh-cn/tour/for-comprehensions.html)
- [泛型类](https://docs.scala-lang.org/zh-cn/tour/generic-classes.html)
- [型变](https://docs.scala-lang.org/zh-cn/tour/variances.html)
- [类型上界](https://docs.scala-lang.org/zh-cn/tour/upper-type-bounds.html)
- [类型下界](https://docs.scala-lang.org/zh-cn/tour/lower-type-bounds.html)
- [内部类](https://docs.scala-lang.org/zh-cn/tour/inner-classes.html)
- [抽象类型](https://docs.scala-lang.org/zh-cn/tour/abstract-type-members.html)
- [复合类型](https://docs.scala-lang.org/zh-cn/tour/compound-types.html)
- [自类型](https://docs.scala-lang.org/zh-cn/tour/self-types.html)
- [隐式参数](https://docs.scala-lang.org/zh-cn/tour/implicit-parameters.html)
- [隐式转换](https://docs.scala-lang.org/zh-cn/tour/implicit-conversions.html)
- [多态方法](https://docs.scala-lang.org/zh-cn/tour/polymorphic-methods.html)
- [类型推断](https://docs.scala-lang.org/zh-cn/tour/type-inference.html)
- [运算符](https://docs.scala-lang.org/zh-cn/tour/operators.html)
- [传名参数](https://docs.scala-lang.org/zh-cn/tour/by-name-parameters.html)
- [注解](https://docs.scala-lang.org/zh-cn/tour/annotations.html)
- [默认参数值](https://docs.scala-lang.org/zh-cn/tour/default-parameter-values.html)
- [命名参数](https://docs.scala-lang.org/zh-cn/tour/named-arguments.html)
- [包和导入](https://docs.scala-lang.org/zh-cn/tour/packages-and-imports.html)
- [包对象](https://docs.scala-lang.org/zh-cn/tour/package-objects.html)



## 2. 基础操作

**在浏览器中尝试Scala**：

1. 网址：https://scalafiddle.io/
2. 编写代码：println("Hello, world!")
3. 点击“运行”按钮

![image-20191125141950945](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-11-25-061951.png)

这是一种简易运行Scala代码的方式，许多实验可以以这种方式进行尝试。



**使用表达式（Expressions）：一种可计算的语句**

```scala
println(1) // 1
println(1 + 1) // 2
println("Hello!") // Hello!
println("Hello," + " world!") // Hello, world!
```

我们可以为表达式结果命名，使用关键字val。

```scala
val x = 1 + 1
println(x) // 2
```

这里的x被称作value，引用一个值不会重新计算他。

值是不能被重新分配的：

```scala
x = 3 // This does not compile.
```

值的类型可以推断，但你也可以显式的声明类型，像这样:

```scala
val x: Int = 1 + 1
```

声明类型在标志符x之后，需要使用“:”

除了可再分配之外，Variables和value是很相似的。我们可以使用关键字var声明它。

```scala
var x = 1 + 1
x = 3 // This compiles because "x" is declared with the "var" keyword.
println(x * x) // 9
```

与value相似，也可以声明类型：

```scala
var x: Int = 1 + 1
```



**块（Blocks）**

可以用{}将表达式括起来。我们称之为块。

块中最后一个表达式的结果也是整个块的结果。

```scala
println({
  val x = 1 + 1
  x + 1
}) // 3
```



**函数（Functions）**

函数是带有参数的表达式

你可以定义一个匿名函数(即没有名字)，返回一个给定的整数加1:

```scala
(x: Int) => x + 1
```

在=>的左边是一个参数列表。

右边是一个包含参数的表达式。

我们也可以为函数命名：

```scala
val addOne = (x: Int) => x + 1
println(addOne(1)) // 2
```

函数可以接收多个参数：

```scala
val add = (x: Int, y: Int) => x + y
println(add(1, 2)) // 3
```

也可以没有参数：

```scala
val getTheAnswer = () => 42
println(getTheAnswer()) // 42
```



**方法（Methods）**

方法的外观和行为与函数非常相似，但是它们之间有一些关键的区别。

方法是用def关键字定义的。def后面是名称、参数列表、返回类型和主体。

```scala
def add(x: Int, y: Int): Int = x + y
println(add(1, 2)) // 3
```

注意，返回类型是如何在参数列表和冒号:Int之后声明的。

方法可以采用多个参数列表。

```scala
def addThenMultiply(x: Int, y: Int)(multiplier: Int): Int = (x + y) * multiplier
println(addThenMultiply(1, 2)(3)) // 9
```

或者没有参数列表：

```scala
def name: String = System.getProperty("user.name")
println("Hello, " + name + "!")
```

还有一些其他的区别，但是现在，你可以把它们看作类似于函数的东西。

方法也可以有多行表达式。

```scala
def getSquareString(input: Double): String = {
  val square = input * input
  square.toString
}
println(getSquareString(2.5)) // 6.25
```



## 3. 统一类型

在Scala中，所有的值都有类型，包括数值和函数。下图阐述了类型层次结构的一个子集。

[![Scala Type Hierarchy](https://docs.scala-lang.org/resources/images/tour/unified-types-diagram.svg)](https://docs.scala-lang.org/resources/images/tour/unified-types-diagram.svg)



**Scala类型层次结构**

[`Any`](https://www.scala-lang.org/api/2.12.1/scala/Any.html)是所有类型的超类型，也称为顶级类 型。它定义了一些通用的方法如`equals`、`hashCode`和`toString`。`Any`有两个直接子类：`AnyVal`和`AnyRef`。

`AnyVal`代表值类型。有9个预定义的非空的值类型分别是：`Double`、`Float`、`Long`、`Int`、`Short`、`Byte`、`Char`、`Unit`和`Boolean`。`Unit`是不带任何意义的值类型，它仅有一个实例可以像这样声明：`()`。所有的函数必须有返回，所以说有时候`Unit`也是有用的返回类型。

`AnyRef`代表引用类型。所有非值类型都被定义为引用类型。在Scala中，每个用户自定义的类型都是`AnyRef`的子类型。如果Scala被应用在Java的运行环境中，`AnyRef`相当于`java.lang.Object`。

这里有一个例子，说明了字符串、整型、布尔值和函数都是对象，这一点和其他对象一样：

```scala
val list: List[Any] = List(
  "a string",
  732,  // an integer
  'c',  // a character
  true, // a boolean value
  () => "an anonymous function returning a string"
)

list.foreach(element => println(element))
```

这里定义了一个类型`List`的变量`list`。这个列表里由多种类型进行初始化，但是它们都是`scala.Any`的实例，所以可以把它们加入到列表中。

下面是程序的输出：

```scala
a string
732
c
true
<function>
```



**类型转换**

值类型可以按照下面的方向进行转换： [![Scala Type Hierarchy](https://docs.scala-lang.org/resources/images/tour/type-casting-diagram.svg)](https://docs.scala-lang.org/resources/images/tour/type-casting-diagram.svg)

例如：

```scala
val x: Long = 987654321
val y: Float = x  // 9.8765434E8 (note that some precision is lost in this case)

val face: Char = '☺'
val number: Int = face  // 9786
```

转换是单向，下面这样写将不会通过编译。

```scala
val x: Long = 987654321
val y: Float = x  // 9.8765434E8
val z: Long = y  // Does not conform
```

你可以将一个类型转换为子类型，这点将在后面的文章介绍。



**Nothing和Null**

`Nothing`是所有类型的子类型，也称为底部类型。没有一个值是`Nothing`类型的。它的用途之一是给出非正常终止的信号，如抛出异常、程序退出或者一个无限循环（可以理解为它是一个不对值进行定义的表达式的类型，或者是一个不能正常返回的方法）。

`Null`是所有引用类型的子类型（即`AnyRef`的任意子类型）。它有一个单例值由关键字`null`所定义。`Null`主要是使得Scala满足和其他JVM语言的互操作性，但是几乎不应该在Scala代码中使用。我们将在后面的章节中介绍`null`的替代方案。



## 4. 类

Scala中的类是用于创建对象的蓝图，其中包含了方法、常量、变量、类型、对象、特质、类，这些统称为成员。类型、对象和特质将在后面的文章中介绍。



**类定义**

一个最简的类的定义就是关键字`class`+标识符，类名首字母应大写。

```scala
class User

val user1 = new User
```

关键字`new`被用于创建类的实例。`User`由于没有定义任何构造器，因而只有一个不带任何参数的默认构造器。然而，你通常需要一个构造器和类体。下面是类定义的一个例子：

```scala
class Point(var x: Int, var y: Int) {

  def move(dx: Int, dy: Int): Unit = {
    x = x + dx
    y = y + dy
  }

  override def toString: String =
    s"($x, $y)"
}

val point1 = new Point(2, 3)
point1.x  // 2
println(point1)  // prints (2, 3)
```

`Point`类有4个成员：变量`x`和`y`，方法`move`和`toString`。与许多其他语言不同，主构造方法在类的签名中`(var x: Int, var y: Int)`。`move`方法带有2个参数，返回无任何意义的`Unit`类型值`()`。这一点与Java这类语言中的`void`相当。另外，`toString`方法不带任何参数但是返回一个`String`值。因为`toString`覆盖了[`AnyRef`](https://docs.scala-lang.org/zh-cn/tour/unified-types.html)中的`toString`方法，所以用了`override`关键字标记。



**构造器**

构造器可以通过提供一个默认值来拥有可选参数：

```scala
class Point(var x: Int = 0, var y: Int = 0)

val origin = new Point  // x and y are both set to 0
val point1 = new Point(1)
println(point1.x)  // prints 1
```

在这个版本的`Point`类中，`x`和`y`拥有默认值`0`所以没有必传参数。然而，因为构造器是从左往右读取参数，所以如果仅仅要传个`y`的值，你需要带名传参。

```scala
class Point(var x: Int = 0, var y: Int = 0)
val point2 = new Point(y=2)
println(point2.y)  // prints 2
```

这样的做法在实践中有利于使得表达明确无误。



**私有成员和Getter/Setter语法**

成员默认是公有（`public`）的。使用`private`访问修饰符可以在类外部隐藏它们。

```scala
class Point {
  private var _x = 0
  private var _y = 0
  private val bound = 100

  def x = _x
  def x_= (newValue: Int): Unit = {
    if (newValue < bound) _x = newValue else printWarning
  }

  def y = _y
  def y_= (newValue: Int): Unit = {
    if (newValue < bound) _y = newValue else printWarning
  }

  private def printWarning = println("WARNING: Out of bounds")
}

val point1 = new Point
point1.x = 99
point1.y = 101 // prints the warning
```

在这个版本的`Point`类中，数据存在私有变量`_x`和`_y`中。`def x`和`def y`方法用于访问私有数据。`def x_=`和`def y_=`是为了验证和给`_x`和`_y`赋值。注意下对于setter方法的特殊语法：这个方法在getter方法的后面加上`_=`，后面跟着参数。

主构造方法中带有`val`和`var`的参数是公有的。然而由于`val`是不可变的，所以不能像下面这样去使用。

```scala
class Point(val x: Int, val y: Int)
val point = new Point(1, 2)
point.x = 3  // <-- does not compile
```

不带`val`或`var`的参数是私有的，仅在类中可见。

```scala
class Point(x: Int, y: Int)
val point = new Point(1, 2)
point.x  // <-- does not compile
```

