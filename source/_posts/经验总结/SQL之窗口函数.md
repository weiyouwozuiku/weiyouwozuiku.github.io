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

窗口函数中的窗口表示的是范围的意思。

窗口函数实现的功能类似group by和order by。但是group by分组汇总会改变了表的行数，一行只有一个类别。而partition by和rank函数不会减少原表中的行数。

![group_by与partition_by的区别.jpg](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/经验总结/SQL之窗口函数/group_by与partition_by的区别.jpg)

## 专业窗口函数

专业窗口函数主要为`rank,dense_rank,row_number`，其区别如下case所示：

```sql
select *,
   rank() over (order by 成绩 desc) as ranking,
   dense_rank() over (order by 成绩 desc) as dese_rank,
   row_number() over (order by 成绩 desc) as row_num
from 班级表
```

![专业窗口函数.jpg](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/经验总结/SQL之窗口函数/专业窗口函数.jpg)

总结一下：

- rank函数：如果有并列名次的行，会占用下一名次的位置。
- dense_rank函数：如果有并列名次的行，不占用下一名次的位置。
- row_number函数：不考虑并列名次的情况。

**在上述的这三个专用窗口函数中，函数后面的括号不需要任何参数，保持()空着就可以。**

## 聚合函数作为窗口函数

聚和窗口函数和上面提到的专用窗口函数用法完全相同，只需要把聚合函数写在窗口函数的位置即可，**但是函数后面括号里面不能为空，需要指定聚合的列名。**

不仅是sum求和，平均、计数、最大最小值，也是同理，**都是针对自身记录、以及自身记录之上的所有数据进行计算。**

用处：聚合函数作为窗口函数，可以在每一行的数据里直观的看到，截止到本行数据，统计数据是多少（最大值、最小值等）。同时可以看出每一行数据，对整体统计数据的影响。

## 参考资料

1. 知乎，[通俗易懂的学会：SQL窗口函数](https://zhuanlan.zhihu.com/p/92654574)，2021
