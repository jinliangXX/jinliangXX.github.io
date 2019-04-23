---
layout:     post                    # 使用的布局
title:      linux-centos7安装配置jdk,mysql,tomcat           # 标题 
subtitle:   centos7配置jdk、mysql、tomcat教程 
date:       2017-03-11              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-miui6.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Linux
---

centos 7—Java web环境的配置（vim编写）
----------------------
初接触linux就是为了让自己的程序在服务器上跑起来，现在手动配置一下运行web的环境，主要是jdk用的安装，mysql数据库的安装，以及tomcat的安装，使本系统能够运行正常的war包。好，不多说，下面记载了我配置的一些过程。

####  1.首先安装jdk1.8

​    1>首先卸载自带的openjdk,用‘`yum list installed | grep java`’查看已经安装的openjdk，然后'`yum -y remove ~*`'(~代表jdk的名字)卸载相应文件。然后用命令'`yum -y remove tzdata-java.noarch`'来卸载其他相应的文件，最后检查是否卸载了('`java -version`')。    

​	2>检查网络是否正常，可以'`ping www.baidu.com`'。(不知为何，明明我的网速可以，但是ping的时间很长很长),所以我终止了，先进行到下一步试以下。

​	3>首先找到jdk的下载地址，'http://www.cnblogs.com/spiders/archive/2016/09/06/5845727.html'下载jdk的三种方式。但是不是很好用。于是通过自己查百度，最后通过命令‘`wget --no-cookie --header "Cookie: oraclelicense=accept-securebackup-cookie"`'下载成功，因为oracle官网下载东西需要接受许可协议。上面的命令后面加上oracle官网的jdk的下载连接就可以了。全部命令是' `wget --no-cookie --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u111-b14/jdk-8u111-linux-x64.rpm`'

​	4>下一步，就是安装jdk了，用rpm命令，后面加‘-ivh’参数，再加上刚才下载文件的名字就好了,全部的安装命令是‘`rpm -ivh jdk-8u111-linux-x64.rpm`’

​	5>检验是否安装成功。用命令'java -version'就可以查看了。（其实，如果这步成功就不用配置了，但是为了以防万一，还是配置了下，具体配置方式在下几步。）

​	6>配置环境变量。主要是修改'/etc/profile'文件，用vim命令编写。此文件中追加的内容：
'

```
JAVA_HOME=/usr/java/jdk1.8.0_111
JRE_HOME=/usr/java/jdk1.8.0_111/jre
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools/jar:$JRE_HOME/lib
export JAVA_HOME JRE_HOME PATH CLASSPATH
```

'
然后通过命令'`source /etc/profile`'编译文件，最后检验是否配置成功，可以通过'`echo $PATH`'来检查是否将环境变量加入到系统中。

​	7>最后一布，重新执行步骤5，如果可以显示jdk版本，jdk就完全配置成功了。

####   2.安装mysql

​    1>首先检测系统中是否已安装mysql,使用命令'`yum list installed | grep mysql`'，我的系统中没有安装，所以不需要卸载，直接进行下一步。

​	2>首先到mysql官网去找到下载mysql的连接,我选择的是rpm安装，所以找到的地址为'`https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm`'，然后通过wget命令下载，具体命令为'`wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm`'。

​	3>安装mysql源。具体命令为'`yum localinstall mysql57-community-release-el7-9.noarch.rpm`',然后检测是否安装成功，命令为'`yum repolist enabled | grep "mysql.*-community.*"`'，如果出现以下内容，证明安装成功(windows也是这些文件，理解时可以对比windows系统),出现的内容为：

```
mysql-connectors-community/x86_64 MySQL Connectors Community                  3
mysql-tools-community/x86_64      MySQL Tools Community                       4
mysql57-community/x86_64          MySQL 5.7 Community Server 
```
    4>安装mysql。命令为'`yum install mysql-community-server`'遇到选择是否选择'y',这里才是真正的安装mysql,其实可以叫做下载安装，因为开始下载的rpm文件只有9K，只是下载了'源'而已。显示'Complete!'代表安装成功。
    5>启动mysql服务。命令为'`systemctl start mysqld`',这里类似于windows上的双击打开一个软件，当然也可以设置开机自启，下一步讲解。此处没有任何反映代表启动成功，如果不放心，可以通过命令'`systemctl status mysqld`'查看启动状态。
    6>设置开机自启。命令有两个，依次为'`systemctl enable mysqld'/'systemctl daemon-reload`'。没有输出代表成功。
    7>修改默认密码(选作)。密码保存在文件'`/var/log/mysqld.log`'中，通过这个文件找到密码，然后登陆mysql，然后修改密码。找到密码的命令为'`grep 'temporary password' /var/log/mysqld.log`',然后通过'`mysql -u root -p`'登陆mysql。然后用mysql的命令修改密码。
    安装完成！

####   3.安装tomcat

​    1>下载tomcat。还是到官网找到下载地址，利用'wget'下载到本地。命令为'`wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.11/bin/apache-tomcat-8.5.11.tar.gz`'

​	2>解压安装包，删除安装包后并移动文件到指定目录。解压的命令为'`tar -zxv -f apache-tomcat-8.5.11.tar.gz`'，然后删除安装包，命令为'`rm -rf apache-tomcat-8.5.11.tar.gz`',最后移动到'/usr/local/tomcat'目录下，命令为 `mv apache-tomcat-8.5.11 /usr/local/tomcat`'。

​	3>运行tomcat。到tomcat的bin目录下,然后运行'startup.sh'，打开浏览器，输入'`http://localhost:8080/'`就可以进入tomcat的安装界面。

    java web的环境大体上配置好了，不同的系统会有些许的区别，但是主要步骤不变。