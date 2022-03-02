---
title: Mysql整理与总结
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/Mysql整理与总结.jpeg
mathjax: true
date: 2022-03-01 21:06:10
tags: Mysql
categories: 程序设计
---

## Mysql基础架构

![Mysql逻辑架构图.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Mysql整理与总结/Mysql逻辑架构图.png)

Mysql大致可以分成Server层和存储引擎层。

### 连接器

连接器负责跟客户端建立链接、获取权限、维持和管理链接。

一个用户成功创建连接后，即使对该用户修改权限，也不会影响当前存在连接的权限。修改完成之后，只有创建新的连接采用使用新的权限。

可以使用`show processlist;`查看连接状态。

客户端通过参数`wait_timeout`来控制没有动静的连接，默认一般是8小时。

使用长链接可能导致Mysql占用内存上涨很快。因为Mysql在执行过程中临时使用的内存是管理在连接对象里面的。这些资源只有在连接断开时才释放。长期积累可能导致内存占用过大，被系统强行OOM，从现象看就是Mysql异常重启。

对此解决方案：

- 定期断开长连接
- 如果是Mysql5.7之后的版本，可以在执行一个较大内存的操作时，通过执行`mysql_reset_connection`来重新初始化连接资源。此时连接会恢复成刚创建完时的状态

### 查询缓存

Mysql拿到一个查询请求后，在老版本会先去查询缓存。之前执行的语句及其结果可能会以kv对的形式存在内存中。key是查询语句，value是查询结果。

但是不推荐使用缓存。因为**通常情况下Mysql的缓存利大于弊。**

查询缓存的失效非常频繁。只要对一个表的更新，这个表上的所有查询缓存就会被清空。对于更新压力较大的数据库来说，查询缓存的命中率很低。**除非你的业务是一个静态表，很长时间才更新一次。**

通过设置参数`query_cache_type`为`DEMAND`，这样默认的SQL语句都不使用查询缓存。需要是显式指定。例如：

```sql
select SQL_cache * from T where ID=...;
```

Mysql8.0之后没有查询缓存这个功能了。

### 分析器

分析器进行sql的词法分析，知道你要做什么。

#### 解析器

解析器处理语法和解析查询，生成对应的解析树。

#### 预处理器

预处理器进一步检查解析树的合法。比如：数据表和数据列是否存在，别名是否有歧义。如果通过则生成新的解析树，再交给优化器。详见《高性能mysql》6.4.3查询优化处理。

### 优化器

优化器是在表里面有多个索引时，决定使用哪个索引；或者一个语句有多个表join的时候，决定各个表的连接顺序。知道要怎么做。

### 执行器

开始执行时会首先检查相应表你的权限。在工程实现上，如果命中查询缓存，会在查询缓存返回结果的时候，做权限验证。

如果有权限，打开表继续执行，执行器会根据表的引擎定义，使用这个引擎提供的接口。如果命中索引的话可以在执行器阶段就直接通过索引查找，没有命中索引就遍历查找每行记录。

在数据库的慢查询日志中可以看到一个`rows_examined`字段，表示这个语句执行过程中扫描了多少行。

在有些场景下，执行器调用一次，在引擎内部则扫描了多行，因此**引擎扫描行数在rows_examined并不是完全相同**。

### redo log

Mysql利用WAL（Write-Ahead Logging）技术，先写日志再落磁盘。具体来说就是当一条记录需要更新时，InnoDB引擎就会先把记录写在redo log中，并更新内存，此时更新算完成。等到InnoDB在恰当的时间会将这个更新操作写入磁盘。

InnoDB的redo log大小是固定的。

![redo_log示意图.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Mysql整理与总结/redo_log示意图.png)
