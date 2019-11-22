---
layout:     post                    # 使用的布局
title:      Apache Hadoop（一）           # 标题 
subtitle:   Apache Hadoop安装及介绍 
date:       2019-11-22              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-yingxiongxueyuan.png    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Big Data
---

## 1. Apache Hadoop主要功能

- [YARN中支持节点属性](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/NodeAttributes.html)
- [YARN中的Submarine](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-applications/hadoop-yarn-submarine/Index.html)
- [存储策略](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/ArchivalStorage.html)
- ABFS连接器
- 增强S3A连接器
- YARN长时间运行服务的升级

## 2. 设置单节点的hadoop集群

1. 下载hadoop安装包

   http://www.apache.org/dyn/closer.cgi/hadoop/common/

2. 解压缩并修改配置文件

```shell
  # set to the root of your Java installation
  export JAVA_HOME=/usr/java/latest（自己的真实java地址）
```

3. 设置单节点模式

   共有三种模式：

   - 本地模式
   - 伪分布模式
   - 全分布模式

   此处为单节点模式（伪分布模式），修改配置文件：

   etc/hadoop/core-site.xml:

   ```xml
   <configuration>
       <property>
           <name>fs.defaultFS</name>
           <value>hdfs://localhost:9000</value>
       </property>
   </configuration>
   ```

   etc/hadoop/hdfs-site.xml:

   ```xml
   <configuration>
       <property>
           <name>dfs.replication</name>
           <value>1</value>
       </property>
   </configuration>
   ```

1. 设置ssh

   ```shell
   ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
   cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
   chmod 0600 ~/.ssh/authorized_keys
   ```

2. 执行hadoop中的一个MapReduce任务

   1. 格式化文件系统

      ```shell
      bin/hdfs namenode -format
      ```

   2. 启动NameNode守护进程和DataNode守护进程

      ```shell
      sbin/start-dfs.sh
      ```

   3. web浏览NameNode

      http://localhost:9870/

      ![image-20191122130046910](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-11-22-55503.png)

   4. 创建执行MapReduce任务所需的HDFS目录

      ```shell
      bin/hdfs dfs -mkdir /user
      bin/hdfs dfs -mkdir /user/<username>
      ```

   5. 将输入文件拷贝到分布式文件系统

      ```shell
      bin/hdfs dfs -mkdir input
      bin/hdfs dfs -put etc/hadoop/*.xml input
      ```

   6. 运行一些提供的例子

      ```shell
      bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar grep input output 'dfs[a-z.]+'
      ```

   7. 检查输出文件，将输出文件从分布式文件系统拷贝到本地系统

      ```shell
      bin/hdfs dfs -get output output
      cat output/*
      ```

   8. 关闭守护进程

      ```shell
      sbin/stop-dfs.sh
      ```

3. YARN上的单个节点

   1. 修改配置文件

      `etc/hadoop/mapred-site.xml`:

      ```xml
      <property>
              <name>mapreduce.framework.name</name>
              <value>yarn</value>
          </property>
          <property>
              <name>mapreduce.application.classpath</name>
              <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
          </property>
      ```

      `etc/hadoop/yarn-site.xml`:

      ```xml
      <configuration>
          <property>
              <name>yarn.nodemanager.aux-services</name>
              <value>mapreduce_shuffle</value>
          </property>
          <property>
              <name>yarn.nodemanager.env-whitelist</name>
              <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PREPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
          </property>
      </configuration>
      ```

   2. 启动资源管理（ResourceManager）守护进程和节点管理（NodeManager）守护进程

      ```shell
      sbin/start-yarn.sh
      ```

   3. 浏览网页ResourceManager

      http://localhost:8088/

      ![image-20191122132100754](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-11-22-055502.png)

   4. 执行一个MapReduce任务

   5. 执行结束，关闭守护进程

      ```shell
      sbin/stop-yarn.sh
      ```




## 3. Hadoop命令教程

所有的hadoop项目及其子项目都遵循以下基础结构：

> shellcommand [SHELL_OPTIONS] [COMMAND] [GENERIC_OPTIONS] [COMMAND_OPTIONS]

| FIELD           | 描述                                                         |
| :-------------- | :----------------------------------------------------------- |
| shellcommand    | 正在调用项目的命令 。例如Hadoop命令使用 `hadoop`, HDFS 使用 `hdfs`, and YARN 使用 `yarn`. |
| SHELL_OPTIONS   | shell在执行java之前的选项                                    |
| COMMAND         | 展示的动作                                                   |
| GENERIC_OPTIONS | 多个命令支持的公共选项集                                     |
| COMMAND_OPTIONS | 在Hadoop公共子项目的这个文档中描述了各种命令及其选项。HDFS和YARN包含在其他文档中。 |

- Shell Options

  所有的命令都会接受一个公共集合的选项

  | SHELL_OPTION          | Description                                                  |
  | :-------------------- | :----------------------------------------------------------- |
  | `--buildpaths`        | Enables developer versions of jars.                          |
  | `--config confdir`    | Overwrites the default Configuration directory. Default is `$HADOOP_HOME/etc/hadoop`. |
  | `--daemon mode`       | If the command supports daemonization (e.g., `hdfs namenode`), execute in the appropriate mode. Supported modes are `start` to start the process in daemon mode, `stop` to stop the process, and `status` to determine the active status of the process. `status` will return an [LSB-compliant](http://refspecs.linuxbase.org/LSB_3.0.0/LSB-generic/LSB-generic/iniscrptact.html) result code. If no option is provided, commands that support daemonization will run in the foreground. For commands that do not support daemonization, this option is ignored. |
  | `--debug`             | Enables shell level configuration debugging information      |
  | `--help`              | Shell script usage information.                              |
  | `--hostnames`         | When `--workers` is used, override the workers file with a space delimited list of hostnames where to execute a multi-host subcommand. If `--workers` is not used, this option is ignored. |
  | `--hosts`             | When `--workers` is used, override the workers file with another file that contains a list of hostnames where to execute a multi-host subcommand. If `--workers` is not used, this option is ignored. |
  | `--loglevel loglevel` | Overrides the log level. Valid log levels are FATAL, ERROR, WARN, INFO, DEBUG, and TRACE. Default is INFO. |
  | `--workers`           | If possible, execute this command on all hosts in the `workers` file. |

- Generic Options

  许多子命令使用一组通用的配置选项来改变他们的行为

  | GENERIC_OPTION | Description                                                  |
  | :------------- | :----------------------------------------------------------- |
  | `-archives `   | Specify comma separated archives to be unarchived on the compute machines. Applies only to job. |
  | `-conf `       | Specify an application configuration file.                   |
  | `-D =`         | Use value for given property.                                |
  | `-files `      | Specify comma separated files to be copied to the map reduce cluster. Applies only to job. |
  | `-fs  or `     | Specify default filesystem URL to use. Overrides ‘fs.defaultFS’ property from configurations. |
  | `-jt  or `     | Specify a ResourceManager. Applies only to job.              |
  | `-libjars `    | Specify comma separated jar files to include in the classpath. Applies only to job. |

Hadoop公共命令

所有命令都是通过hadoop shell执行的，他们分为用户命令和管理命令

- 用户命令

  **archive**

  创建一个hadoop归档

  **checknative**（hadoop checknative [-a] [-h]）

  -a    检测所有库是否可用

  -h    help

  检查hadoop本地命令的可用性

  **classpath**

  hadoop classpath [--glob |--jar <path> |-h |--help]

  --glob   扩大通配符

  -- jar path   将路径写入名为path的jar中

  打印获取Hadoop jar所需的类路径和所需的库

  **conftest**

  hadoop conftest [-conffile <path>]...

  验证xml配置文件

  **credential**

  hadoop credential <subcommand> [options]

  在凭据提供程序中管理凭据、密码和机密的命令。

  **distch**

  hadoop distch [-f urilist_url] [-i] [-log logdir] path:owner:group:permissions

  一次更改多个文件的所有权和权限

  **distcp**

  递归复制文件或目录

  **dtutil**

  `hadoop dtutil [-keytab` *keytab_file* `-principal` *principal_name* `]` *subcommand* `[-format (java|protobuf)] [-alias` *alias* `] [-renewer` *renewer* `]` *filename…*

  获取和管理凭证文件中的hadoop委托令牌的实用工具

  **fs**

  文件系统指令

  **gridmix**

  Gridmix是Hadoop集群的基准测试工具

  **jar**

  hadoop jar <jar> [mainClass] args...

  执行一个jar文件

  **jnipath**

  hadoop jnipath

  打印计算好的java.library.path

  **kerbname**

  hadoop kerbname principal

  **kdiag**

  hadoop kdiag

  诊断Kerberos问题

  **key**

  hadoop key <subcommand> [options]

  通过密钥提供程序管理密钥

  **kms**

  hadoop kms

  运行密钥管理服务器KMS

  **trace**

  查看和修改Hadoop跟踪设置

  **version**

  hadoop version

  **CLASSNAME**

  hadoop CLASSNAME

  运行名为CLASSNAME的类

  **envvars**

  hadoop envvars

  显示计算过的Hadoop环境变量。

- 管理命令

  对于Hadoop集群管理员非常有用的命令

  **daemonlog**

  ```
  hadoop daemonlog -getlevel <host:port> <classname> [-protocol (http|https)]
  hadoop daemonlog -setlevel <host:port> <classname> <level> [-protocol (http|https)]
  ```

  获取/设置由守护进程中的限定类名动态标识的日志的日志级别

文件

**etc/hadoop/hadoop-env.sh**

该文件存储所有hadoop命令使用的全局设置

**etc/hadoop/hadoop-user-functions.sh**

该文件允许一些高级用户重写命令

**~/.hadooprc**

存储单个用户的私人环境



## 4. FS（File System）命令

文件系统(FS) shell包括各种与Hadoop分布式文件系统(HDFS)以及Hadoop支持的其他文件系统(如本地FS、WebHDFS、S3 FS等)直接交互的类shell命令。

```shell
bin/hadoop fs <args>
```

所有的FS shell命令都以路径uri作为参数。URI格式是scheme://authority/path。对于HDFS，模式是HDFS，对于本地FS，模式是file。方案和权限是可选的。如果未指定，则使用配置中指定的默认方案。HDFS文件或目录(比如/parent/child)可以指定为HDFS://namenodehost/parent/child，或者简单地指定为/parent/child(假设您的配置设置为指向HDFS://namenodehost)。

FS shell中的大多数命令的行为类似于相应的Unix命令。每个命令都描述了不同之处。错误信息被发送到stderr，输出被发送到stdout。

如果使用HDFS，则HDFS dfs是同义词。

可以使用相对路径。对于HDFS，当前工作目录是HDFS主目录/user/<username>，通常必须手动创建。HDFS主目录也可以隐式访问，例如，当使用HDFS垃圾文件夹时，主目录中的. trash目录。