---
title: Presto实战与演练
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/Presto实战与演练.png
mathjax: true
date: 2022-01-21 21:26:54
tags: Presto
categories: 程序设计
---

## 背景

Facebook的数据仓库存储在少量大型Hadoop/HDFS集群。Hive是Facebook在几年前专为Hadoop打造的一款数据仓库工具。在以前，Facebook的科学家和分析师一直依靠Hive来做数据分析。但Hive使用MapReduce作为底层计算框架，是专为批处理设计的。但随着数据越来越多，使用Hive进行一个简单的数据查询可能要花费几分到几小时，显然不能满足交互式查询的需求。Facebook也调研了其他比Hive更快的工具，但它们要么在功能有所限制要么就太简单，以至于无法操作Facebook庞大的数据仓库。

2012年开始试用的一些外部项目都不合适，他们决定自己开发，这就是Presto。2012年秋季开始开发，目前该项目已经在超过 1000名Facebook雇员中使用，运行超过30000个查询，每日数据在1PB级别。Facebook称Presto的性能比Hive要好上10倍多。2013年Facebook正式宣布开源Presto。

## 参考文献

1. [Presto官方文档](http://prestodb.io/)
2. [Facebook Presto团队介绍Presto的文章](https://www.facebook.com/notes/facebook-engineering/presto-interacting-with-petabytes-of-data-at-facebook/10151786197628920)
3. [SlideShare两个分享Presto的PPT](http://www.slideshare.net/zhusx/presto-overview?from_search=1&&http://www.slideshare.net/frsyuki/hadoop-source-code-reading-15-in-japan-presto)
4. 美团技术团队，[Presto实现原理和美团的使用实践](https://tech.meituan.com/2014/06/16/presto.html)，2014

