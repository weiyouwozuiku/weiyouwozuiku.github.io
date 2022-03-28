---
title: 常用SQL整理
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/常用SQL整理.jpg
mathjax: true
date: 2021-12-21 21:04:24
tags:
- SQL
categories: 程序设计
---

## 窗口函数

### 什么是窗口函数

窗口函数，也叫OLAP函数（Online Anallytical Processing，联机分析处理），可以对数据库数据进行实时分析。

窗口函数的基本用法如下：

```sql
<窗口函数> over (partition by <用户分组的列名> order by <用于排序的列名>) 
```

窗口函数可以放两种函数：

1. 专用窗口函数
2. 聚合函数

因为窗口函数是对where或者group by子句进行处理后的操作，所以**窗口函数原则上只能写在select子句中**

### 窗口函数的好处

窗口函数中的窗口表示的是范围的意思。

窗口函数实现的功能类似group by和order by。但是group by分组汇总会改变了表的行数，一行只有一个类别。而partition by和rank函数不会减少原表中的行数。

![group_by与partition_by的区别.jpg](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/常用SQL整理/group_by与partition_by的区别.jpg)

窗口函数中的order by只是决定窗口内的数据按什么顺序进行排序或计算，对最后返回结果的排列顺序并没有影响。

### 专业窗口函数

专业窗口函数主要为`rank,dense_rank,row_number`，其区别如下case所示：

```sql
select *,
   rank() over (order by 成绩 desc) as ranking,
   dense_rank() over (order by 成绩 desc) as dese_rank,
   row_number() over (order by 成绩 desc) as row_num
from 班级表
```

![专业窗口函数.jpg](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/常用SQL整理/专业窗口函数.jpg)

总结一下：

- rank函数：如果有并列名次的行，会占用下一名次的位置。
- dense_rank函数：如果有并列名次的行，不占用下一名次的位置。
- row_number函数：不考虑并列名次的情况。

**在上述的这三个专用窗口函数中，函数后面的括号不需要任何参数，保持()空着就可以。**

### 聚合函数作为窗口函数

聚和窗口函数和上面提到的专用窗口函数用法完全相同，只需要把聚合函数写在窗口函数的位置即可，**但是函数后面括号里面不能为空，需要指定聚合的列名。**

不仅是sum求和，平均、计数、最大最小值，也是同理，**都是针对自身记录、以及自身记录之上的所有数据进行计算。**

用处：聚合函数作为窗口函数，可以在每一行的数据里直观的看到，截止到本行数据，统计数据是多少（最大值、最小值等）。同时可以看出每一行数据，对整体统计数据的影响。

另外，在聚合窗口可以指定汇总范围，这个功能称之为框架。

自身+上1条记录：`SUM(sale_price) over(order by product_id rows 1 preceding)`

自身+下1条记录：`SUM(sale_price) over(order by product_id rows 1 following) `

自身+上1条记录+下2条记录：`SUM(sale_price) over(order by product_id rows between 1 preceding and 2 following) 

## CASE-WHEN

### 用法

**case函数只返回第一个符合条件的值，剩下的case部分将会被自动忽略。如果省略了ELSE子句，则返回NULL。**

### 简单case函数

```sql
--简单case函数
case sex
  when '1' then '男'
  when '2' then '女’
  else '其他' end
```

####  case搜索函数

```sql
case when sex = '1' then '男'
     when sex = '2' then '女'
     else '其他' end  
     
CASE WHEN score IS NULL THEN '缺席考试' ELSE '正常' END
```

#### sum和case结合使用

```sql
select
  2    sum(case u.sex when 1 then 1 else 0 end)男性,
  3    sum(case u.sex when 2 then 1 else 0 end)女性,
  4    sum(case when u.sex <>1 and u.sex<>2 then 1 else 0 end)性别为空
  5  from users u;
 
        男性         女性       性别为空
---------- ---------- ----------
         3          2          0
```



## 参考资料

1. 知乎，[通俗易懂的学会：SQL窗口函数](https://zhuanlan.zhihu.com/p/92654574)，2021
2. 知乎，[精益SQL —— “窗口函数”的正确食用方式](https://zhuanlan.zhihu.com/p/60226935)，2019
2. 博客园，[SQL之case when then用法](https://www.cnblogs.com/Richardzhu/p/3571670.html)，2014
4. 知乎，[SQL之CASE WHEN用法详解](https://zhuanlan.zhihu.com/p/63333847)，2019
