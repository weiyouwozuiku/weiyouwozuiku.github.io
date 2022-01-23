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

## 参考文献

1. CSDN，[深入HQL学习以及HQL和SQL的区别](https://blog.csdn.net/qq_28633249/article/details/77884062)，2017
