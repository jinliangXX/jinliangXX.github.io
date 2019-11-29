---
layout:     post                    # 使用的布局
title:      Apache Hadoop（二）           # 标题 
subtitle:   HDFS体系结构 
date:       2019-11-29              # 时间
author:     Jinliang                      # 作者
header-img: img/post-bg-linzhongxiaowu.jpeg    #这篇文章标题背景图片
catalog: true                       # 是否归档
mathjax: true                       #是否显示公式
tags:                               #标签
    - Big Data
---

## 1. HDFS简介

Hadoop分布式文件系统(HDFS)是一个设计用于在普通硬件上运行的分布式文件系统。它与现有的分布式文件系统有许多相似之处。但是，与其他分布式文件系统的区别非常明显。HDFS是**高度容错**的，设计用于部署在低成本的硬件上。HDFS提供对应用程序数据的**高吞吐量**访问，适用于具有大数据集的应用程序。HDFS放宽了对POSIX的一些要求，允许对文件系统数据进行流访问。HDFS最初是作为Apache Nutch web搜索引擎项目的基础架构构建的。HDFS是Apache Hadoop Core项目的一部分。

相关特性：

- 硬件故障

  硬件故障在HDFS是常见的。HDFS实例可能由数百或数千台服务器机器组成，每台机器存储文件系统数据的一部分。组件数量庞大，而且每个组件的失败概率都很高，这意味着HDFS的某些组件总是不起作用。因此，**故障检测和快速、自动恢复**是HDFS的核心体系结构目标。

- 流式数据访问

  运行在HDFS上的应用程序需要对其数据集进行流访问。它们不是通常在通用文件系统上运行的通用应用程序。HDFS更多地被设计成批处理，而不是用户交互使用。重点是数据访问的高吞吐量，而不是低延迟的数据访问。POSIX强加了许多针对HDFS的应用程序不需要的硬需求。POSIX语义在几个关键领域已经被交换以提高数据吞吐率。

- 大数据集合

  在HDFS上运行的应用程序有大量的数据集。HDFS中的一个典型文件的大小是gb到tb。因此，HDFS被调优为支持大文件。它应该提供高聚合数据带宽并可扩展到单个集群中的数百个节点。它应该在一个实例中支持数千万个文件。

- 简单的一致性模型

  HDFS应用程序需要文件的一次写，多访问读模型。一个文件一旦创建、写入和关闭，除了附加和截断之外就不需要更改。支持将内容附加到文件末尾，但不能在任意点进行更新。这种假设简化了数据一致性问题，并支持高吞吐量数据访问。MapReduce应用程序或web爬虫应用程序非常适合这个模型。

- 移动计算比移动数据更便宜

  如果应用程序请求的计算在其操作的数据附近执行，则其效率要高得多。当数据集很大时，尤其如此。这最小化了网络拥塞，增加了系统的总吞吐量。这里的假设是，将计算迁移到离数据更近的地方通常比将数据迁移到应用程序正在运行的地方更好。HDFS为应用程序提供接口，使它们更接近数据所在的位置。

- 跨异构硬件和软件平台的可移植性

  HDFS被设计成易于从一个平台移植到另一个平台。这有助于将HDFS作为大量应用程序的首选平台广泛采用。



## 2. NameNode 和 DataNodes

HDFS有一个主/从架构。HDFS集群由一个NameNode组成，NameNode是一个主服务器，它管理文件系统名称空间并控制客户机对文件的访问。此外，还有许多datanode，通常是集群中的每个节点一个datanode，它们管理附加到它们所运行的节点上的存储。HDFS公开文件系统名称空间，并允许将用户数据存储在文件中。在内部，一个文件被分成一个或多个块，这些块存储在一组datanode中。NameNode执行文件系统名称空间操作，如打开、关闭和重命名文件和目录。它还确定块到datanode的映射。datanode负责处理来自文件系统客户机的读和写请求。datanode还根据来自NameNode的指令执行块创建、删除和复制。

![HDFS Architecture](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-11-22-075049.png)

NameNode和DataNode是为在普通机器上运行而设计的软件。这些机器通常运行GNU/Linux操作系统(OS)。HDFS是使用Java语言构建的;任何支持Java的机器都可以运行NameNode或DataNode软件。使用高度可移植的Java语言意味着HDFS可以部署在广泛的机器上。一个典型的部署有一个专用的机器，它只运行NameNode软件。集群中的其他机器都运行DataNode软件的一个实例。该体系结构不排除在同一台机器上运行多个datanode，但在实际部署中很少出现这种情况。

集群中单个NameNode的存在极大地简化了系统的体系结构。NameNode是所有HDFS元数据的仲裁程序和存储库。系统的设计方式是，用户数据永远不会流经NameNode。



## 3. 文件系统命名空间（Namespace）

HDFS支持传统的分层文件组织。用户或应用程序可以创建目录并将文件存储在这些目录中。文件系统名称空间层次结构与大多数现有文件系统相似;可以创建和删除文件、将文件从一个目录移动到另一个目录或重命名文件。HDFS支持用户配额和访问权限。HDFS不支持硬链接或软链接。但是，HDFS体系结构并不排除实现这些特性。

虽然HDFS遵循文件系统的命名约定，但是某些路径和名称是保留的。透明加密和快照等功能使用保留的路径。

NameNode维护文件系统名称空间。对文件系统名称空间或其属性的任何更改都由NameNode记录。应用程序可以指定HDFS应该维护的文件副本的数量。一个文件的拷贝数称为该文件的复制因子。此信息由NameNode存储。



## 4. 数据备份（Data Replication）

HDFS被设计成在大型集群中的机器之间可靠地存储非常大的文件。它将每个文件存储为块序列。复制文件的块是为了容错。每个文件都可以配置块大小和复制因子。

除了最后一个块之外，文件中的所有块大小都是相同的，而用户可以在append和hsync中添加可变长度块支持之后，不需要按照配置的块大小填写最后一个块，就可以启动一个新块。

应用程序可以指定文件的副本数量。复制因子可以在文件创建时指定，以后可以更改。HDFS中的文件只写一次(附加和截断除外)，并且任何时候只有一个写器。

NameNode做出关于复制块的所有决定。它定期从集群中的每个datanode接收一个心跳和一个块报告。接收到心跳意味着DataNode功能正常。块报告包含DataNode上的所有块的列表。

![HDFS DataNodes](https://jinliangxx.oss-cn-beijing.aliyuncs.com/2019-11-22-081145.png)



1. 复制位置:第一个简单步骤

   副本的放置对HDFS的可靠性和性能至关重要。优化复制位置是HDFS与大多数其他分布式文件系统的不同之处。这是一个需要大量调优和经验的特性。支持机架的副本放置策略的目的是提高数据可靠性、可用性和网络带宽利用率。当前的复制放置策略实现是这方面的第一个尝试。实现这个策略的短期目标是在生产系统上验证它，了解它的行为，并为测试和研究更复杂的策略打下基础。

   大型HDFS实例运行在通常分布在许多机架上的计算机集群上。不同机架中的两个节点之间的通信必须经过交换机。在大多数情况下，同一机架上的计算机之间的网络带宽大于不同机架上的计算机之间的网络带宽。

   NameNode通过Hadoop rack感知中概述的过程来确定每个DataNode所属的rack id。一个简单但非最优的策略是将副本放在唯一的机架上。这可以防止在整个机架发生故障时丢失数据，并允许在读取数据时使用多个机架的带宽。这个策略在集群中均匀地分布副本，这使得在组件失败时很容易平衡负载。但是，这个策略会增加写操作的成本，因为写操作需要将块转移到多个机架。

   如果复制因子大于3，则随机确定第4个和后续副本的位置，同时将每个机架的副本数量保持在上限(基本上是(副本- 1)/机架+ 2)之下。

   因为NameNode不允许datanode具有同一块的多个副本，所以所创建的最大副本数是当时datanode的总数。

   在将对存储类型和存储策略的支持添加到HDFS之后，除了上面描述的机架感知之外，NameNode还考虑了副本放置策略。NameNode首先基于rack感知选择节点，然后检查候选节点是否具有与文件关联的策略所需的存储空间。如果候选节点没有存储类型，NameNode将查找另一个节点。如果在第一个路径中找不到足够的节点来放置副本，那么NameNode将在第二个路径中查找具有回退存储类型的节点。

2. 副本选择
   为了最小化全局带宽消耗和读取延迟，HDFS尝试满足来自最接近读取器的副本的读取请求。如果在与读取器节点相同的机架上存在一个副本，则首选该副本来满足读取请求。如果HDFS集群跨越多个数据中心，那么驻留在本地数据中心的副本比任何远程副本更可取。

3. 安全模式

   在启动时，NameNode进入一个称为Safemode的特殊状态。当NameNode处于Safemode状态时，不会发生数据块的复制。NameNode接收来自datanode的心跳和块报告消息。块报告包含DataNode所承载的数据块列表。每个块具有指定的最小副本数。当该数据块的最小副本数量通过NameNode检入时，一个块被认为是安全复制的。在安全复制的数据块的可配置百分比与NameNode签入之后(外加30秒)，NameNode退出Safemode状态。然后它确定数据块的列表(如果有的话)，这些数据块仍然具有少于指定数量的副本。然后NameNode将这些块复制到其他datanode。



## 5. 文件系统元数据的持久化

HDFS名称空间由NameNode存储。NameNode使用名为EditLog的事务日志持久地记录文件系统元数据发生的每个更改。例如，在HDFS中创建一个新文件会导致NameNode将一条记录插入到表明这一点的**EditLog**中。类似地，更改文件的复制因子会导致将新记录插入EditLog。NameNode使用本地主机OS文件系统中的一个文件来存储EditLog。整个文件系统名称空间(包括块到文件的映射和文件系统属性)存储在一个名为FsImage的文件中。**FsImage**也作为文件存储在NameNode的本地文件系统中。

NameNode在内存中保存整个文件系统名称空间和文件块映射的映像。当NameNode启动时，或者一个检查点被一个可配置的阈值触发时，它将从磁盘读取FsImage和EditLog，将EditLog中的所有事务应用到FsImage的内存表示中，并将这个新版本清除到磁盘上的一个新FsImage中。然后，它可以截断旧的EditLog，因为它的事务已经应用于持久的FsImage。这个过程称为**检查点**。检查点的目的是通过获取文件系统元数据的快照并将其保存到FsImage，确保HDFS具有文件系统元数据的一致视图。即使读取FsImage是有效率的，但是直接对FsImage进行增量编辑是无效率的。我们不为每个编辑修改FsImage，而是将编辑保存在Editlog中。在检查点期间，Editlog中的更改应用于FsImage。检查点可以在以秒为单位的给定时间间隔(`dfs.namenode.checkpoint.period`)内触发，也可以在积累了给定数量的文件系统事务之后触发(`dfs.namenode.checkpoint.txns`)。如果设置了这两个属性，则要到达的第一个阈值将触发检查点。

DataNode将HDFS数据存储在本地文件系统的文件中。DataNode不知道HDFS文件。它将每个HDFS数据块存储在本地文件系统中的一个单独文件中。DataNode不会在同一目录中创建所有文件。相反，它使用启发式方法来确定每个目录的最优文件数量，并适当地创建子目录。在同一个目录中创建所有本地文件并不是最佳的选择，因为本地文件系统可能无法有效地支持单个目录中的大量文件。当DataNode启动时，它扫描本地文件系统，生成所有HDFS数据块的列表，这些数据块对应于每个本地文件，并将该报告发送给NameNode。该报告称为`Blockreport`。



## 6. 通信协议

所有HDFS通信协议都位于TCP/IP协议之上。客户端在NameNode机器上建立到可配置TCP端口的连接。它使用NameNode来处理ClientProtocol。DataNode使用DataNode协议与NameNode通信。远程过程调用(RPC)抽象封装了客户端协议和DataNode协议。按照设计，NameNode从不启动任何rpc。相反，它只响应由datanode或客户机发出的RPC请求。



## 7. 鲁棒性

HDFS的主要目标是在出现故障时可靠地存储数据。常见的三种故障类型是NameNode故障、DataNode故障和网络分区故障。

- 数据磁盘故障、心跳和重新复制

  每个DataNode定期向NameNode发送一个心跳消息。一个网络分区可能导致一个datanode子集失去与NameNode的连接。NameNode通过缺少心跳消息来检测这种情况。NameNode将没有最近心跳的数据节点标记为死节点，并且不会向它们发送任何新的IO请求。已注册到死DataNode的任何数据都不再对HDFS可用。DataNode的死亡可能导致某些块的复制因子低于它们的指定值。NameNode不断跟踪需要复制哪些块，并在必要时启动复制。需要重新复制的原因有很多:DataNode可能不可用，副本可能损坏，DataNode上的硬盘可能失败，或者文件的复制因子可能增加。

  将datanode标记为死的超时时间比较长(默认超过10分钟)，以避免datanode的状态抖动引起的复制风暴。用户可以设置更短的时间间隔，将datanode标记为过期节点，并避免通过为性能敏感的工作负载配置读写过期节点。

- 集群再平衡

  HDFS架构与数据再平衡方案兼容。如果DataNode上的空闲空间低于某个阈值，则方案可以自动将数据从一个DataNode移动到另一个DataNode。在某个特定文件突然出现高需求时，方案可以动态地创建其他副本并重新平衡集群中的其他数据。这些类型的数据再平衡方案还没有实现。

- 数据完整性

  从DataNode获取的数据块可能损坏。这种损坏可能是由于存储设备、网络故障或软件错误造成的。HDFS客户端软件实现对HDFS文件内容的校验和检查。当客户端创建一个HDFS文件时，它计算该文件的每个块的校验和，并将这些校验和存储在同一个HDFS名称空间中的一个单独的隐藏文件中。当客户端检索文件内容时，它将验证从每个DataNode接收到的数据是否与存储在关联校验和文件中的校验和相匹配。如果没有，那么客户端可以选择从另一个具有该块副本的DataNode检索该块。

- 元数据磁盘故障

  FsImage和EditLog是HDFS的中心数据结构。这些文件的损坏可能导致HDFS实例不可用。因此，可以将NameNode配置为支持维护FsImage和EditLog的多个副本。对FsImage或EditLog的任何更新都会导致同步更新每个FsImage和EditLog。FsImage和EditLog的多个副本的同步更新可能会降低NameNode每秒能够支持的名称空间事务的速率。但是，这种降级是可以接受的，因为即使HDFS应用程序本质上是数据密集型的，但它们不是元数据密集型的。当NameNode重新启动时，它选择要使用的最新一致的FsImage和EditLog。

  提高对故障的弹性的另一个选项是使用多个namenode来启用高可用性，这些namenode可以使用NFS上的共享存储，也可以使用分布式编辑日志(称为Journal)。推荐使用后者。

- 快照

  快照支持在特定时刻存储数据的副本。快照特性的一种用法可能是将损坏的HDFS实例回滚到以前知道的正确时间点。



## 8. 数据组织

- 数据块

  HDFS被设计成支持非常大的文件。与HDFS兼容的应用程序是处理大数据集的应用程序。这些应用程序只写他们的数据一次，但他们读它一次或多次，并要求这些读满足在流速度。HDFS支持文件上的一次写-读-多语义。HDFS使用的典型块大小是128 MB，因此，HDFS文件被分割成128 MB的块，如果可能，每个块将驻留在不同的DataNode上。

- 复制流水线

  当客户机将数据写入复制因子为3的HDFS文件时，NameNode使用复制目标选择算法检索数据阳极列表。此列表包含将承载该块副本的datanode。然后客户端写入第一个DataNode。第一个DataNode开始按部分接收数据，将每个部分写入其本地存储库，并将该部分传输到列表中的第二个DataNode。第二个DataNode开始接收数据块的每个部分，将该部分写入它的存储库，然后将该部分刷新到第三个DataNode。最后，第三个DataNode将数据写入本地存储库。因此，DataNode可以从管道中的前一个接收数据，同时将数据转发到管道中的下一个。因此，数据被流水线从一个DataNode传输到下一个DataNode。



## 9. 可访问性

可以从应用程序以许多不同的方式访问HDFS。从本质上说，HDFS为应用程序提供了一个文件系统Java API。这个Java API和REST API的C语言包装器也可用。此外，还可以使用HTTP浏览器来浏览HDFS实例的文件。通过使用NFS网关，HDFS可以作为客户机本地文件系统的一部分挂载。

- FS Shell

  HDFS允许以文件和目录的形式组织用户数据。它提供了一个名为FS shell的命令行接口，允许用户与HDFS中的数据进行交互。这个命令集的语法类似于用户已经熟悉的其他shell(例如bash、csh)。下面是一些动作/命令对的例子:

  | Action                                                 | Command                                  |
  | :----------------------------------------------------- | :--------------------------------------- |
  | Create a directory named `/foodir`                     | `bin/hadoop dfs -mkdir /foodir`          |
  | Remove a directory named `/foodir`                     | `bin/hadoop fs -rm -R /foodir`           |
  | View the contents of a file named `/foodir/myfile.txt` | `bin/hadoop dfs -cat /foodir/myfile.txt` |

  FS shell的目标是需要脚本语言与存储的数据交互的应用程序。

- DFSAdmin

  DFSAdmin命令集用于管理HDFS集群。这些命令仅供HDFS管理员使用。下面是一些动作/命令对的例子:

  | Action                                   | Command                             |
  | :--------------------------------------- | :---------------------------------- |
  | Put the cluster in Safemode              | `bin/hdfs dfsadmin -safemode enter` |
  | Generate a list of DataNodes             | `bin/hdfs dfsadmin -report`         |
  | Recommission or decommission DataNode(s) | `bin/hdfs dfsadmin -refreshNodes`   |

- 浏览器界面

  典型的HDFS安装配置web服务器，通过可配置的TCP端口公开HDFS名称空间。这允许用户导航HDFS名称空间，并使用web浏览器查看其文件的内容。



## 10. 空间回收

- 文件删除和取消删除

  如果启用了垃圾配置，由FS Shell删除的文件不会立即从HDFS中删除。相反，HDFS将它移动到一个垃圾目录(每个用户在/user/<username>/. trash下都有自己的垃圾目录)。只要文件仍在垃圾中，就可以快速恢复。

  最近删除的文件被移动到当前垃圾目录(/user/<username>/. trash / current)，在可配置的间隔内，HDFS为当前垃圾目录中的文件创建检查点(在/user/<username>/. trash /<date>下)，并在旧的检查点过期时删除它们。有关垃圾的检查点，请参阅FS shell的expunge命令。

  在垃圾中的生命期结束后，NameNode从HDFS名称空间中删除文件。删除一个文件会释放与该文件关联的块。请注意，从用户删除文件到HDFS中相应增加的空闲空间之间可能存在相当大的时间延迟。

  下面的示例将展示如何通过FS Shell从HDFS中删除文件。我们在delete目录下创建了两个文件(test1和test2)

  ```shell
  $ hadoop fs -mkdir -p delete/test1
  $ hadoop fs -mkdir -p delete/test2
  $ hadoop fs -ls delete/
  Found 2 items
  drwxr-xr-x   - hadoop hadoop          0 2015-05-08 12:39 delete/test1
  drwxr-xr-x   - hadoop hadoop          0 2015-05-08 12:40 delete/test2
  ```

  我们将删除文件test1。下面的注释显示文件已经移动到垃圾目录。

  ```shell
  $ hadoop fs -rm -r delete/test1
  Moved: hdfs://localhost:8020/user/hadoop/delete/test1 to trash at: hdfs://localhost:8020/user/hadoop/.Trash/Current
  ```

  现在我们将使用skipTrash选项删除文件，该选项不会将文件发送到垃圾箱。它将完全从HDFS中删除。

  ```shell
  $ hadoop fs -rm -r -skipTrash delete/test2
  Deleted delete/test2
  ```

  现在我们可以看到，垃圾目录只包含文件test1。

  ```shell
  $ hadoop fs -ls .Trash/Current/user/hadoop/delete/
  Found 1 items\
  drwxr-xr-x   - hadoop hadoop          0 2015-05-08 12:39 .Trash/Current/user/hadoop/delete/test1
  ```

  因此文件test1进入垃圾，文件test2被永久删除。

- 降低复制因子

  当文件的复制因子减少时，NameNode选择可以删除的多余副本。下一个心跳将此信息传输到DataNode。然后DataNode删除相应的块，集群中出现相应的空闲空间。同样，在完成setReplication API调用和集群中出现空闲空间之间可能会有一个时间延迟。