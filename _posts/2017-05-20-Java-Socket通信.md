---
layout:     post                    # 使用的布局
title:      Java-Socket通信           # 标题 
subtitle:   Java-Socket通信介绍 
date:       2017-05-20              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-coffee.jpeg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Java

---

### **概念：**

网络上两个程序通过一个双向的通信连接实现数据交换，这个双向链路的一端称为一个Socket。
Socket也称为套接字，可以用来实现不同的虚拟机或不同的计算机之间的通信。


在Java语言中，Socket可以分为两种类型：面向连接的Socket通信协议（TCP），和面向无连接的Socket通信协议（UDP）。

### **基于TCP的通信过程：**

Server（服务器）端Listen（监听）指定的某个端口（建议大于1024端口）是否有连接请求；
Client（客户）端向Server端发出Connect（连接）请求；
Server端向Client端发回Accept（接受）消息。
连接建立，会话产生，可以通过Send，Write等方法与对方通信。

**Socket的生命周期：**
打开Socket
使用Socket收发数据
关闭Socket

在Java语言中，可以使用ServerSocket作为服务器，Socket作为客户端来实现网络通信。