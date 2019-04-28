---
layout:     post                    # 使用的布局
title:      Web-cookie和session有什么区别           # 标题 
subtitle:   Web-cookie和session有什么区别 
date:       2017-06-24              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-iWatch.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Web
---

**cookie**是在http下，服务器或脚本能够可以维护客户工作站上信息的一种方式。它是由web服务器保存在用户**浏览器**上的小文件，可以包含有关的用户信息（如身份识别、密码等信息）。

**session**用来在客户端与服务器之间保持状态的解决方案以及存储结构。

----------
**相同点：**都能进行信息存储。
**不同点：**

 1. cookie采用在客户端保持状态的方案，即数据保存在客户端的浏览器上；session采用的是在服务器端保持状态的方案，即数据存放在服务器上。
 2. cookie安全性不够，因为cookie保存在浏览器上，其他人很容易得到保存在本地的cookie，进行cookie欺骗；而session保存在服务器上，相比较为安全。
 3. cookie性能更高一点。session会在一定时间内保存在服务器上，所以当访问量增加时，会降低服务器的性能。
 4. 单个cookie保存的数据不能超过4KB，很多浏览器限制一个站点最多保存20个cookie；session不存在此问题，视服务器而定。

***因此，鉴于以上特点，将登陆信息等重要信息放在session，不重要的放在cookie中，增加用户体验。***

