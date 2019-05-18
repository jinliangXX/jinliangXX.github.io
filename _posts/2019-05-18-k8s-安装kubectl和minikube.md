---
layout:     post                    # 使用的布局
title:      linux安装kubectl与minikube过程           # 标题 
subtitle:   linux安装kubectl与minikube详细过程 
date:       2019-05-18              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-diqiu.jpeg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - k8s
---

## 1. 安装kubectl

**kubectl是什么？**

The Kubernetes command-line tool, [kubectl](https://kubernetes.io/docs/user-guide/kubectl/), allows you to run commands against Kubernetes clusters. You can use kubectl to deploy applications, inspect and manage cluster resources, and view logs. 

kubectl是k8s的命令行工具，允许你使用命令在k8s集群上运行。

你可以使用kubectl部署应用、检查和管理集群资源、查看日志等。

我将在Ubuntu上安装kubectl~

#### 1. 下载最新版本

```shell
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
```

我下载到Ubuntu的"下载"目录。

#### 2. 使kubectl二进制文件可执行

```shell
chmod +x ./kubectl
```

![image-20190517222252917](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-142253.png)

#### 3. 将kubectl二进制文件移到PATH目录下

```shell
sudo mv ./kubectl /usr/local/bin/kubectl
```

![image-20190517222405082](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-142405.png)

因为我当前的用户非root用户，PATH路径却是root用户权限才可以操作，因此使用sudo。

![image-20190517222607058](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-142607.png)

#### 4. 确保安装的版本是最新的

```shell
kubectl version
```

![image-20190517222629973](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-142630.png)

client版本即为kubectl的版本，k8s因为没有安装，因此server版本没有显示。

安装完kubectl，我将安装k8s的虚拟环境minikube。

---

kubectl已经安装完成，下一步是可选的，为安装kubectl自动补全插件，我认为功能很不错，因此我选择安装。

不过测试了一下，貌似自动安装了~

![image-20190517223517442](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-143517.png)



## 2. 安装minikube

minikube是什么？

a tool that runs a single-node Kubernetes cluster in a virtual machine on your laptop.

一种在笔记本电脑上的虚拟机中运行单节点Kubernetes群集的工具。

#### 1. 验证机器是否支持虚拟化

```shell
egrep --color 'vmx|svm' /proc/cpuinfo
```

为空不行的呦，需要开启。

![image-20190517223921048](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-143921.png)

#### 2. 安装[VirtualBox](https://www.virtualbox.org/wiki/Downloads)软件

```shell
wget https://download.virtualbox.org/virtualbox/6.0.8/virtualbox-6.0_6.0.8-130520~Ubuntu~bionic_amd64.deb
```

![image-20190517224730103](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-144730.png)

然后安装即可：

```shell
sudo dpkg -i virtualbox-6.0_6.0.8-130520~Ubuntu~bionic_amd64.deb
```

![image-20190517225513958](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-145514.png)

#### 3. 下载二进制软件

```shell
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

![image-20190517225652416](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-145653.png)

#### 4. 添加可执行文件

```shell
chmod +x minikube
```

![image-20190517225757353](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-145758.png)

#### 5. 将二进制文件添加到path路径

```shell
sudo cp minikube /usr/local/bin
```

#### 6. minikube开启

```shell
minikube start minikube start --vm-driver=virtualbox
```

![image-20190517231354244](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-17-151354.png)

貌似没开启AMD-V，重启电脑进入BIOS开启一下吧~

重启电脑屏幕下方会显示怎样进入BIOS，然后开启AMD-V(不同的硬件产品会有不同的名字)

然后重新运行命令：

![image-20190518200724626](/Users/jinliangxu/Library/Application Support/typora-user-images/image-20190518200724626.png)

#### 7. 检测k8s集群是否建立

```shell
kubectl version
```

![image-20190518200942140](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-18-120943.png)

此处的server version就是k8s集群中的版本信息，此处指minikube。

#### 8. 关闭minikube

```shell
minikube stop
```

![image-20190518201252139](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-05-18-121252.png)