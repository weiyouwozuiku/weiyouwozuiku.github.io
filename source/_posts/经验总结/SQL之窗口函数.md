---
title: SQL之窗口函数
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/经验总结/SQL之窗口函数.jpg
mathjax: true
date: 2021-12-21 21:04:24
tags:
- SQL
categories: 经验总结
---

## 什么是窗口函数

窗口函数，也叫OLAP函数（Online Anallytical Processing，联机分析处理），可以对数据库数据进行实时分析。

窗口函数的基本用法如下：

```sql
<窗口函数> over (partition by <用户分组的列名> order by <用于排序的列名>) 
```

窗口函数可以放两种函数：

1. 专用窗口函数
2. 聚合函数

因为窗口函数是对where或者group by子句进行处理后的操作，所以**窗口函数原则上只能写在select子句中**

## 窗口函数的好处

窗口函数实现的功能类似group by和order by。但是group by分组汇总会改变了表的行数，一行只有一个类别。而partition by和rank函数不会减少原表中的行数。

![group_by与partition_by的区别.jpg](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/经验总结/SQL之窗口函数/group_by与partition_by的区别.jpg)

## 参考资料

1. 知乎，[通俗易懂的学会：SQL窗口函数](https://zhuanlan.zhihu.com/p/92654574)，2021
