---
title: 硬盘IO总结
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/硬盘IO总结.jpeg
mathjax: true
date: 2021-10-03 11:09:54
tags: 硬盘IO
categories: 经验总结
---

## 背景

随着计算机硬件在过去10年中遵循摩尔定律的发展，通用计算机的CPU主频早已超过了4GHz，内存已早已进入了DDR4的时代。但是传统机械磁盘的读写性能并没有明显提升，而SSD的价格又过高。因此，探究传统机械硬盘的物理结构与性能优化方式具有重大学术与工程意义。

## 磁盘的物理结构

由于单一盘片容量有限，一般硬盘都有两张以上的盘片，每个盘片有两面，都可记录信息，所以一张盘片对应着两个磁头。盘片被分为许多扇形的区域，每个区域叫一个扇区，硬盘中每个扇区的大小固定为512字节。盘片表面上以盘片中心为圆心，不同半径的同心圆称为磁道，不同盘片相同半径的磁道所组成的圆柱称为柱面。磁道与柱面都是表示不同半径的圆，在许多场合，磁道和柱面可以互换使用。磁盘盘片垂直视角如下图所示：

![磁盘盘片垂直视角.png ](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/磁盘IO总结/磁盘盘片垂直视角.png)

早期的硬盘每磁道扇区数相同，此时由磁盘基本参数可以计算出硬盘的容量：存储容量=磁头数*磁道（柱面）数*每道扇区数*每扇区字节数。由于每磁道扇区数相同，外圈磁道半径大，里圈磁道半径小，外圈和里圈扇区面积自然会不一样。同时，为了更好的读取数据，即使外圈扇区面积再大也只能和内圈扇区一样存放相同的字节数（512字节）。这样一来，外圈的记录密度就要比内圈小，会浪费大量的存储空间。

如今的硬盘都使用ZBR（Zoned Bit Recording，区位记录）技术，盘片表面由里向外划分为数个区域，不同区域的磁道扇区数目不同，同一区域内各磁道扇区数相同，盘片外圈区域磁道长扇区数目较多，内圈区域磁道短扇区数目较少，大体实现了等密度，从而获得了更多的存储空间。此时，由于每磁道扇区数各不相同，所以传统的容量计算公式就不再适用。实际上如今的硬盘大多使用LBA（Logical Block Addressing）逻辑块寻址模式，知道LBA后即可计算出硬盘容量。

## 影响硬盘性能的因素

影响磁盘的关键因素是磁盘服务时间，即磁盘完成一个I/O请求所花的时间。

磁盘服务时间=寻道时间+旋转延迟+数据传输时间

### 寻道时间

$T_{seek}$是指将读写磁头移动至正确的磁道上所需要的时间。寻道时间越短，I/O操作越快，目前磁盘的平均寻道时间一般在3-15ms。

### 旋转延迟

$T_{rotation}$是指盘片旋转将请求数据所在的扇区移动到读写磁盘下方所需要的时间。旋转延迟取决于磁盘转速，通常用磁盘旋转一周所需时间的1/2表示。比如：7200rpm的磁盘平均旋转延迟大约为60*1000/7200/2 = 4.17ms，而转速为15000rpm的磁盘其平均旋转延迟为2ms。

### 数据传输时间

$T_{transfer}$是指完成传输所请求的数据所需要的时间，它取决于数据传输率，其值等于数据大小除以数据传输率。目前IDE/ATA能达到133MB/s，SATA II可达到300MB/s的接口数据传输率，数据传输时间通常远小于前两部分消耗时间。简单计算时可忽略。

## 衡量性能指标

机械硬盘的连续读写性能很好，但随机读写性能很差，这主要是因为磁头移动到正确的磁道上需要时间，随机读写时，磁头需要不停的移动，时间都浪费在了磁头寻址上，所以性能不高。衡量磁盘的重要主要指标是IOPS和吞吐量。

### IOPS

IOPS（Input/Output Per Second）即每秒的输入输出量（或读写次数），即指每秒内系统能处理的I/O请求数量。随机读写频繁的应用，如小文件存储等，关注随机读写性能，IOPS是关键衡量指标。可以推算出磁盘的IOPS = 1000ms / (Tseek + Trotation + Transfer)，如果忽略数据传输时间，理论上可以计算出随机读写最大的IOPS。常见磁盘的随机读写最大IOPS为：

- 7200rpm的磁盘 IOPS = 76 IOPS
- 10000rpm的磁盘IOPS = 111 IOPS
- 15000rpm的磁盘IOPS = 166 IOPS

### 吞吐量

## 参考文献

1. IBM developerWorks，[AIX 下磁盘 I/O 性能分析](https://www.ibm.com/developerworks/cn/aix/library/1203_weixy_aixio/)，2012。
2. CSDN博客频道，[磁盘性能评价指标—IOPS和吞吐量](https://blog.csdn.net/hanchengxi/article/details/19089589)，2014。
3. IBM developerWorks，[read 系统调用剖析](https://www.ibm.com/developerworks/cn/linux/l-cn-read/)，2008。
4. IBM developerWorks，[从文件 I/O 看 Linux 的虚拟文件系统](https://developer.ibm.com/alert-zh/)，2007。
5. CSDN博客频道，[Linux文件系统预读](http://blog.csdn.net/kai_ding/article/details/17322787)，2013。
6. Linux Kernel Exploration，[Linux通用块设备层](http://www.ilinuxkernel.com/files/Linux.Generic.Block.Layer.pdf)。
7. CSDN博客频道，[Linux块设备的IO调度算法和回写机制](http://blog.csdn.net/hustyangju/article/details/40507647)，2014。
8. Apache，[Kafka](http://kafka.apache.org/)。
9. Taobao，[Taobao File System](http://tfs.taobao.org/)。
10. 美团技术团队，[磁盘I/O那些事](https://tech.meituan.com/2017/05/19/about-desk-io.html),2017
