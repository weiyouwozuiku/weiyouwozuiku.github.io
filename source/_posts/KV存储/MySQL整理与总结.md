---
title: MySQL整理与总结
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/KV存储/MySQL整理与总结.jpeg
mathjax: true
top: true
date: 2022-03-01 21:06:10
tags: MySQL
categories: KV存储
---

## MySQL基础架构

![MySQL逻辑架构图.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/KV存储/MySQL整理与总结/MySQL逻辑架构图.png)

MySQL大致可以分成Server层和存储引擎层。

### 连接器

连接器负责跟客户端建立链接、获取权限、维持和管理链接。

一个用户成功创建连接后，即使对该用户修改权限，也不会影响当前存在连接的权限。修改完成之后，只有创建新的连接采用使用新的权限。

可以使用`show processlist;`查看连接状态。

客户端通过参数`wait_timeout`来控制没有动静的连接，默认一般是8小时。

使用长链接可能导致MySQL占用内存上涨很快。因为MySQL在执行过程中临时使用的内存是管理在连接对象里面的。这些资源只有在连接断开时才释放。长期积累可能导致内存占用过大，被系统强行OOM，从现象看就是MySQL异常重启。

对此解决方案：

- 定期断开长连接
- 如果是MySQL5.7之后的版本，可以在执行一个较大内存的操作时，通过执行`MySQL_reset_connection`来重新初始化连接资源。此时连接会恢复成刚创建完时的状态

### 查询缓存

MySQL拿到一个查询请求后，在老版本会先去查询缓存。之前执行的语句及其结果可能会以kv对的形式存在内存中。key是查询语句，value是查询结果。

但是不推荐使用缓存。因为**通常情况下MySQL的缓存利大于弊。**

查询缓存的失效非常频繁。只要对一个表的更新，这个表上的所有查询缓存就会被清空。对于更新压力较大的数据库来说，查询缓存的命中率很低。**除非你的业务是一个静态表，很长时间才更新一次。**

通过设置参数`query_cache_type`为`DEMAND`，这样默认的SQL语句都不使用查询缓存。需要是显式指定。例如：

```sql
select SQL_cache * from T where ID=...;
```

MySQL8.0之后没有查询缓存这个功能了。

### 分析器

分析器进行sql的词法分析，知道你要做什么。

#### 解析器

解析器处理语法和解析查询，生成对应的解析树。

#### 预处理器

预处理器进一步检查解析树的合法。比如：数据表和数据列是否存在，别名是否有歧义。如果通过则生成新的解析树，再交给优化器。详见《高性能MySQL》6.4.3查询优化处理。

### 优化器

优化器是在表里面有多个索引时，决定使用哪个索引；或者一个语句有多个表join的时候，决定各个表的连接顺序。知道要怎么做。

### 执行器

开始执行时会首先检查相应表你的权限。在工程实现上，如果命中查询缓存，会在查询缓存返回结果的时候，做权限验证。

如果有权限，打开表继续执行，执行器会根据表的引擎定义，使用这个引擎提供的接口。如果命中索引的话可以在执行器阶段就直接通过索引查找，没有命中索引就遍历查找每行记录。

在数据库的慢查询日志中可以看到一个`rows_examined`字段，表示这个语句执行过程中扫描了多少行。

在有些场景下，执行器调用一次，在引擎内部则扫描了多行，因此**引擎扫描行数在rows_examined并不是完全相同**。

### 日志

#### redo log

redo log是属于InnoDB特有的日志，属于存储引擎层，记录了这个页“做了什么改动”。

MySQL利用WAL（Write-Ahead Logging）技术，先写日志再落磁盘。具体来说就是当一条记录需要更新时，InnoDB引擎就会先把记录写在redo log中，并更新内存，此时更新算完成。等到InnoDB在恰当的时间会将这个更新操作写入磁盘。

InnoDB的redo log大小是固定的。

![redo_log示意图.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/KV存储/MySQL整理与总结/redo_log示意图.png)

`write pos` 是当前记录的位置，一边写一边后移，写到第 3 号文件末尾后就回到 0 号文件开头。`checkpoint` 是当前要擦除的位置，也是往后推移并且循环的，擦除记录前要把记录更新到数据文件。

`wirte pos`与`checkpoint`之间的空间就是还可以存放的容量。

有了 redo log，InnoDB 就可以保证即使数据库发生异常重启，之前提交的记录都不会丢失，这个能力称为`crash-safe`。

`innodb_flush_log_at_trx_commit`设置为1表示每次事务的redo log直接持久化到磁盘。

#### binlog

binlog属于Sever层，binlog不具备`crash-safe`的能力。

binlog有两种模式，statement格式会记录sql语句，row格式会记录行的内容，记两条更新前后都有。

`sync_binlog`设置为1表示每次事务的binlog直接持久化到磁盘。

#### 二者对比

1. redo log是InnoDB引擎特有的；binlog是MySQL的Server层实现的，所有引擎都可以使用。
1. redo log是物理日志，记录的是在某个数据页上做了什么修改；binlog是逻辑日志，记录的是这个语句的原始逻辑。
1. redo log是循环写，空间固定会用完；binlog是写到一定大小后切换到下一个，并不会覆盖之前的日志。

#### 整体运行逻辑

![update语句执行流程.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/KV存储/MySQL整理与总结/update语句执行流程.png)

有了对这两个日志的概念性理解，我们再来看执行器和 InnoDB 引擎在执行简单的 update 语句`update table T set c=c+1 where ID=2`时的内部流程。

1. 执行器先找引擎取 ID=2 这一行。ID 是主键，引擎直接用树搜索找到这一行。如果 ID=2 这一行所在的数据页本来就在内存中，就直接返回给执行器；否则，需要先从磁盘读入内存，然后再返回。
2. 执行器拿到引擎给的行数据，把这个值加上 1，比如原来是 N，现在就是 N+1，得到新的一行数据，再调用引擎接口写入这行新数据。
3. 引擎将这行新数据更新到内存中，同时将这个更新操作记录到 redo log 里面，此时 redo log 处于 prepare 状态。然后告知执行器执行完成了，随时可以提交事务。
4. 执行器生成这个操作的 binlog，并把 binlog 写入磁盘。
5. 执行器调用引擎的提交事务接口，引擎把刚刚写入的 redo log 改成提交（commit）状态，更新完成。

在上述过程中，redo存在“两阶段提交”，即prepare和commit。

两阶段提交的目的是**为了让两份日志之间的逻辑一致**。

如果要实现一定时间内的数据库数据恢复，可以通过binlog中存储的所有逻辑操作实现以及定期的整库备份。

当需要恢复数据到指定的某一秒时，那么可以：

1. 首先找到最近的一次全量备份。将这个表恢复到临时表。
2. 从备份的时间点开始，将备份的binlog依次取出，重放到误操作之前的那个时刻。
3. 再对比临时表与线上表之前的差异，同步到线上表。

基于数据恢复的过程，可以论证为什么需要进行两阶段提交。这里不妨使用反证法，即两种情况：1. 先写redo log再写binlog 2. 先写binlog再写redo log。

针对情况1：假设redo log完成，binlog没有执行，MySQL进程异常重启。redo log保证了数据在系统重启后还能恢复，但是此时binlog中并没有相应的原始逻辑记录，这会导致当前数据能依据redo log恢复，但是之后通过binlog恢复则不存在这次操作的记录，也就没有相应的数据。

针对情况2：假设binlog完成，redo log没有执行，MySQL进程异常重启。由于redo log没有写，恢复后这个事务无效。数据为原本的数据。但是binlog中记录了修改数据的原始逻辑，之后用binlog恢复时就会多出一个事务来。

因此，不使用两阶段提交，数据库的状态就可能和用它日志恢复恢复出来的库的状态不一致。

除了数据恢复的场景，在扩容的时候，需要一些备库来增加系统的读能力的时候。现在主流的做法是全量备份加上应用binlog实现。

redo log和binlog都能表示事务的提交状态，而两阶段保持逻辑上的一致。

## 事务

**事务的支持在引擎层实现**。

ACID（Atomicity、Consistency、Isolation、Durability，即原子性、一致性、隔离性、持久性）。

### 隔离性与隔离级别

当数据库上有多个事务同时执行的时候，就可能出现脏读（dirty read）、不可重复读（non-repeatable read）、幻读（phantom read）的问题，为了解决这些问题，就有了“隔离级别”的概念。

在谈隔离级别之前，你首先要知道，你隔离得越严实，效率就会越低。因此很多时候，需要在二者之间寻找一个平衡点。

SQL 标准的事务隔离级别包括：

- 读未提交（read uncommitted）：是指，一个事务还没提交时，它做的变更就能被别的事务看到。
- 读提交（read committed）：是指，一个事务提交之后，它做的变更才会被其他事务看到。
- 可重复读（repeatable read）：是指，一个事务执行过程中看到的数据，总是跟这个事务在启动时看到的数据是一致的。当然在可重复读隔离级别下，未提交变更对其他事务也是不可见的。
- 串行化（serializable ）：顾名思义是对于同一行记录，“写”会加“写锁”，“读”会加“读锁”。当出现读写锁冲突的时候，后访问的事务必须等前一个事务执行完成，才能继续执行。
