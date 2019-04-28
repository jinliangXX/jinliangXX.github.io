---
layout:     post                    # 使用的布局
title:      Algorithm-二维数组的查找           # 标题 
subtitle:   Algorithm-二维数组的查找解析 
date:       2017-05-19              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-senlin.jpeg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Algorithm
---

# 二维数据中查找是否存在某个整数

> 在一个二维数组中（每个一维数组的长度相同），
> 每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序
> 请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

| 1 | 2 | 3 | 
| 4 | 5 | 6 | 
| 7 | 8 | 9 | 

如上面所示，是一个符合的二维矩阵，但是这只是其中一个特例，例如：
位置5 > 位置1
~~位置4 > 位置2~~     
~~位置4 < 位置2~~

 

----------
首先想到的是暴利破解，也就是遍历二维数组，如果遇到匹配的整数，那么返回True，否则返回False；
当然也可以稍微利用一下有序的规则，例如第二层遍历遇到比target大的数就break内层遍历，但是还是治标不治本，时间复杂度仍旧为$n^2$ 
还是贴一下代码

```java
    def Find_n2(self, target, array):
        if len(array) == 0:
            return False
        rows = len(array)
        columns = len(array[0])
        for row in array:
            for column in row:
                if column == target:
                    return True
        return False
```
这种方法我在oj上试验过，可以通过，但是应该是oj对内存，复杂度要求度不高导致的，暴力方式肯定不会是最优解，那么最优解是什么呢？

----------
首先我们想一下普通的二维数组查找是否有最优解呢？貌似并没有
那么这个题目与普通二维数组的差别在哪呢？不错，就是在
> 每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序

这句话，那么我们只能从这句话入手！！
我们分析一下二维数组的四个角，分别是左上角、右上角、左下角、右下角，他们的特性不难分辨出左上角是最小的数，右下角是最大的数，左下角向右变大向上变小，右上角向左变小向下变大。
好了，突破点有了，我们可以从左下角和右上角入手，我们以左下角为例：

 1. 如果这个数字大于左下角的数，那么第一列的数都比左下角的数小，第一列排除，向右移一位
 2. 如果这个数小于左下角的数，那么将数字上移一位，继续判断
 3. 如果等于，那么找到了！

 我们按照上面的方法思考，当走步骤1时，肯定会排除一列，并且新位置同一列下面的数字同样也可以不同考虑了，因为规则：每一行从左到右递增，这样会呈现斜线的方式尽快找到合适的值。

几个例子，二维数组就是上面的数组，我们现在想要找数字6，那么我们经过的数依次是：
7、4、5、6

上代码：

```java
class Solution:
    # array 二维列表
    def Find(self, target, array):
        if len(array) == 0:
            return False
        # write code
        rows = len(array)
        columns = len(array[0])
        row = rows - 1
        column = 0
        while row >= 0 and column < columns:
            if array[row][column] > target:
                row = row - 1
            elif array[row][column] < target:
                column = column + 1
            else:
                return True
        return False
```

----------
误区：
开始接触这道题目时，总走到一个误区，那就是过分关注左上角和右下角，但是他们是死胡同，因为判断后，向右向下都是变大的，此时就不能确认向什么地方走了！
当我们想不明白时，不妨换个思路，正确答案就在眼前！！！