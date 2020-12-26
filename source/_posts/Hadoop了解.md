---
title: Hadoop了解
author: 不二
date: 2019-07-09 12:33:48
mathjax: true
tags: 
- Hadoop
categories: 云计算
---

## 大数据

1. 特点
   * 数据大
   * 价值大
   * 速度快（时效性）
   * 多样性
2. 架构
   - 数据采集：Mysql
   - 数据存储：HDFS、HBase、Hive
   - 数据计算：Spark、MapReduce

---


## Hadoop

Hadoop实现了HDFS分布式存储，下面介绍其中的八个相关部件：

- HDFS中NameNode管理DataNode

  *HDFS是分布式存储，具有数据备份和数据容错的优点。*

  **DataNode由block组成，其中的数据平行传递，不通过client，通过心跳检测（监测数据是否正常）。**

  **每个block数据块大小都是128M，因此不适合小数据的存储。**

  $$主从模式
  \begin{cases}
  \text{NameNode（master）}\\
  \text{DataNode（slave）}
  \end{cases}$$

- Yarn（资源管理器），负责创建RM（资源管理、作业调度）和AM（任务调度）

  **先启动Hadoop，后启动Yarn。**

- MapReduce，处理大规模数据集的编程模型。

  Map:输入数据格式解析：InputFormat

  ​		输入数据处理：Mapper

  ​		输入数据分组：Partitioner

  **总的来说，Map负责分割。**

  Reduce：数据远程拷贝

  ​				数据按Key排序

  ​				数据处理：Reducer

  ​				数据输出格式：OutFormat

  **总的来说，Reduce负责处理。**

  *其中有一个shuffle，可以打乱分组。*

- Hive 数据仓库工具，将SQL转换成MapReduce执行。

- HBase 存放结构化的数据。

- ZooKeeper 担任服务生产者和服务消费者的注册中心。

  大致上分成
  $$
  \begin{cases} Leader\\
  Follower\\
  Obsever
  \end{cases}
  $$
  **推荐使用奇数集群**

- Flume 分布式日志收集系统

- Storm 分布式流式计算的架构
