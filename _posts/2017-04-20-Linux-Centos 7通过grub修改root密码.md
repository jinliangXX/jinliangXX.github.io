---
layout:     post                    # 使用的布局
title:      linux-Centos 7通过grub修改root密码           # 标题 
subtitle:   Centos 7通过grub修改root密码 
date:       2017-04-20              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-xingkong.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Linux
---

有时我们自己安装的centos7会忘记root密码，那么一种简单地修改root密码的方式就是通过grub，下面记录了通过grub修改root密码的流程。

 1. 首先在开机时，迅速按下“e”键，进入以下界面：![img](http://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-04-28-075454.png)
 2. 按向下的箭头翻到最后，界面为：![修改root密码的地方](http://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-04-28-075327.png)
 3. 修改"ro”为"rw init=/sysroot/bin/sh"然后按住ctrl+x，界面为：![修改后为](http://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-04-28-075329.png)
 4. 然后修改root密码：输入命令为：![这里写图片描述](http://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-04-28-75330.png)
 5. 然后强制启动系统，我安装的是虚拟机，所以关机重启就好。测验中······
![这里写图片描述](http://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-04-28-075328.png)
修改成功！