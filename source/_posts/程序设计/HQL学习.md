---
title: HQL
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/HQL学习.jpg
mathjax: true
date: 2022-01-14 10:51:41
tags: HQL
categories: 程序设计
---

## 背景

HQL（Hibernate Query Language）是面向对象的查询语言。，它与SQL查询语言类似。 在 Hibernate 提供的各种检索方式中, HQL 是使用最广的一种检索方式. 它有如下功能:

1. 在查询语句中设定各种查询条件；
2. 支持投影查询, 即仅检索出对象的部分属性；
3. 支持分页查询；
4. 支持连接查询；
5. 支持分组查询, 允许使用 HAVING 和 GROUP BY 关键字；
6. 提供内置聚集函数, 如 sum(), min() 和 max()；
7. 支持子查询；
8. 支持动态绑定参数；
9. 能够调用 用户定义的 SQL 函数或标准的 SQL 函数。

HQL 查询包括以下步骤:

1. 获取Hibernate Session对象。
2. 编写HQL语句
3. 以HQL语句作为参数，调用Session的createQuery方法创建查询对象。
4. 如果HQL语句包含参数，则调用Query的setXxx方法为参数赋值。
5. 调用Query对象的list()或uniqueResult()方法返回查询结果列表（持久化实体集）

Qurey 接口支持方法链编程风格, 它的 setXxx() 方法返回自身实例, 而不是 void 类型，因此可以写类似于`.setXxx().setXxx().setXxx()...`样式的语句。

## HQL VS SQL

HQL 查询语句是面向对象的, Hibernate 负责解析 HQL 查询语句, 然后根据对象-关系映射文件中的映射信息, 把 HQL 查询语句翻译成相应的 SQL 语句。HQL 查询语句中的主体是域模型中的类及类的属性。

SQL 查询语句是与关系数据库绑定在一起的。SQL 查询语句中的主体是数据库表及表的字段。

HQL与SQL基本一致，在用法上：

1. HQL不支持行级别的增删改，所有数据在加载时就已经确定好了，不可更改，只能整个分区覆盖写
2. 不支持事务
3. 支持分区存储

HQL有以下特点：

1. 不支持等值连接，一般使用left join、right join、或者inner join替代
2. 不能智能识别`concat(';',key')`，只会将分号作为SQL结束符
3. 不支持insert into、update、delete等操作
4. HQL中String类型的字段若是空(empty)字符串, 即长度为0, 那么对它进行IS NULL的判断结果是False，使用left join可以进行筛选行
5. 不支持 ‘< dt <’这种格式的范围查找，可以用dt in(”,”)或者between替代

## 参考文献

1. CSDN，[深入HQL学习以及HQL和SQL的区别](https://blog.csdn.net/qq_28633249/article/details/77884062)，2017
2. 简书，[【数据库】HIVE SQL与SQL的区别](https://www.jianshu.com/p/0696a52226bb)，2019
