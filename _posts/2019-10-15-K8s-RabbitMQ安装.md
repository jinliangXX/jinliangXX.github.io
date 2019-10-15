---

layout:     post                    # 使用的布局
title:      RabbitMQ安装过程           # 标题 
subtitle:   Kubernetes安装RabbitMQ 
date:       2019-10-15              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-xiaowu.jpeg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - K8s
---

## 1. RabbitMQ简介

RabbitMQ is a message broker: it accepts and forwards messages. You can think about it as a post office: when you put the mail that you want posting in a post box, you can be sure that Mr. or Ms. Mailperson will eventually deliver the mail to your recipient. In this analogy, RabbitMQ is a post box, a post office and a postman.

The major difference between RabbitMQ and the post office is that it doesn't deal with paper, instead it accepts, stores and forwards binary blobs of data ‒ *messages*.

RabbitMQ是一个消息代理（broker）:它接受和转发消息。你可以把它想象成一个邮局:当你把想要投寄的邮件放进邮箱时，你可以确定邮差先生或女士最终会把邮件送到你的收件人那里。在这个类比中，RabbitMQ是一个邮筒，一个邮局或者一个邮递员。

RabbitMQ和邮局之间的主要区别是它不处理纸张，而是接受、存储和转发二进制的数据消息块。

#### **作用：**

**消息系统允许软件、应用相互连接和扩展．这些应用可以相互链接起来组成一个更大的应用，或者将用户设备和数据进行连接．消息系统通过将消息的发送和接收分离来实现应用程序的异步和解偶．**

或许你正在考虑进行数据投递，非阻塞操作或推送通知。或许你想要实现发布／订阅，异步处理，或者工作队列。所有这些都可以通过消息系统实现。

RabbitMQ是一个消息代理 - 一个消息系统的媒介。它可以为你的应用提供一个通用的消息发送和接收平台，并且保证消息在传输过程中的安全。

#### 优点：

##### 可靠性

RabbitMQ提供了多种技术可以让你在性能和可靠性之间进行权衡。这些技术包括持久性机制、投递确认、发布者证实和高可用性机制。

##### 灵活的路由

消息在到达队列前是通过交换机进行路由的。RabbitMQ为典型的路由逻辑提供了多种内置交换机类型。如果你有更复杂的路由需求，可以将这些交换机组合起来使用，你甚至可以实现自己的交换机类型，并且当做RabbitMQ的插件来使用。

##### 集群

在相同局域网中的多个RabbitMQ服务器可以聚合在一起，作为一个独立的逻辑代理来使用。

##### 联合

对于服务器来说，它比集群需要更多的松散和非可靠链接。为此RabbitMQ提供了联合模型。

##### 高可用的队列

在同一个集群里，队列可以被镜像到多个机器中，以确保当其中某些硬件出现故障后，你的消息仍然安全。

##### 多协议

RabbitMQ 支持多种消息协议的消息传递。

##### 广泛的客户端

只要是你能想到的编程语言几乎都有与其相适配的RabbitMQ客户端。

##### 可视化管理工具

RabbitMQ附带了一个易于使用的可视化管理工具，它可以帮助你监控消息代理的每一个环节。

##### 追踪

如果你的消息系统有异常行为，RabbitMQ还提供了追踪的支持，让你能够发现问题所在。

##### 插件系统

RabbitMQ附带了各种各样的插件来对自己进行扩展。你甚至也可以写自己的插件来使用。



## 2. 下载docker重新打包（用于不能连接外网的环境）

#### 下载镜像

```
docker pull bitnami/rabbitmq
docker pull bitnami/minideb
```

#### 镜像打包

```
docker tag bitnami/rabbitmq:latest 9.111.215.134/zjrepo/rabbitmq:latest
docker tag bitnami/minideb:latest 9.111.215.134/zjrepo/minideb:latest
```

#### 镜像push

```
docker push 9.111.215.134/zjrepo/rabbitmq:latest
docker push 9.111.215.134/zjrepo/minideb:latest
```



## 3. 制作helm chart

#### github地址：

https://github.com/helm/charts/tree/master/stable/rabbitmq

#### 更改：

- ##### 更改image地址

  values.yaml:

  ```
  image:
    registry: 9.111.215.134/zjrepo
    repository: rabbitmq
    tag: latest
  ```

  ```
  volumePermissions:
    enabled: true
    image:
      registry: 9.111.215.134/zjrepo
      repository: minideb
      tag: latest
      pullPolicy: Always
  ```

- ##### 更改NodePort暴露端口

  values.yaml:

  ```
  service:
    type: NodePort
    ## Node port
    ## ref: https://github.com/bitnami/bitnami-docker-rabbitmq#environment-variables
    ##
    nodePort: 31000
    managerNodePort: 31001
  ```

  vc.yaml:

  ```
  - name: stats
      port: {{ .Values.service.managerPort }}
      targetPort: stats
      {{- if (eq .Values.service.type "ClusterIP") }}
      nodePort: null
      {{- else if (and (eq .Values.service.type "NodePort") (not (empty .Values.service.managerNodePort))) }}
      nodePort: {{ .Values.service.managerNodePort }}
      {{- end }}
  ```



## 4. 创建GlusterFS类型的PV/PVC

- ##### 在每个node上执行：

  创建GlusterFS卷

  ```shell
  mkdir cmb-rabbitmq
  ```

- ##### 在master上执行

  创建GlusterFS卷

  ```shell
  gluster volume create cmb-rabbitmq replica 3 9.115.42.33:/data/cmb-rabbitmq 9.115.42.34:/data/cmb-rabbitmq 9.115.42.35:/data/cmb-rabbitmq
  ```

  ```
  gluster volume start cmb-rabbitmq
  ```

  ```
  vim rabbitmq-endpoints.json
  {
    "kind": "Endpoints",
    "apiVersion": "v1",
    "metadata": {
      "name": "cmb-rabbitmq-endpoints",
      "namespace": "cmb"
    },
    "subsets": [
      {
        "addresses": [
          {
            "ip": "9.115.42.33"
          }
        ],
        "ports": [
          {
            "port": 40001
          }
        ]
      },
      {
        "addresses": [
          {
            "ip": "9.115.42.34"
          }
        ],
        "ports": [
          {
            "port": 40001
          }
        ]
      },
      {
        "addresses": [
          {
            "ip": "9.115.42.35"
          }
        ],
        "ports": [
          {
            "port": 40001
          }
        ]
      }
    ]
  }
  ```

  ```
  vim rabbitmq-glusterfs-service.json
  {
    "kind": "Service",
    "apiVersion": "v1",
    "metadata": {
      "name": "cmb-rabbitmq-endpoints",
      "namespace": "cmb"
    },
    "spec": {
      "ports": [
        {"port": 40001}
      ]
    }
  }
  ```

  ```
  vim rabbitmq-pv.yaml
  apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: cmb-rabbitmq-pv
    namespace: cmb
    labels:
      name: cmb-rabbitmq-pv
  spec:
    capacity:
      storage: 8Gi
    accessModes:
      - ReadWriteMany
    glusterfs:
      endpoints: cmb-rabbitmq-endpoints
      path: cmb-rabbitmq
      readOnly: false
    persistentVolumeReclaimPolicy : Retain
  ```

  ```
  vim rabbitmq-pvc.yaml
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: cmb-rabbitmq-pvc
    namespace: cmb
  spec:
    resources:
      requests:
        storage: 8Gi
    selector:
      matchLabels:
        name: cmb-rabbitmq-pv
    accessModes:
      - ReadWriteMany
  ```

  ```shell
  kubectl create -f rabbitmq-endpoints.json
  kubectl create -f rabbitmq-glusterfs-service.json
  kubectl create -f rabbitmq-pvc.yaml
  kubectl create -f rabbitmq-pv.yaml
  ```

  

## 5. 通过helm chart进行安装

```
helm install ···
```

or

##### icp···



## 6. RabbitMQ开启dashboard

http://9.115.42.30:31001/#/users

![image-20191015151858248](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-10-15-071858.png)

![image-20191015151921296](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-10-15-071921.png)