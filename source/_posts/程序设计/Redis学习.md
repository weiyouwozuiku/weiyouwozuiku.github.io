---
title: Redis学习
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/Redis学习.png
mathjax: true
date: 2022-03-12 22:07:36
tags: Redis
categories: 程序设计
---

# 常用命令和数据类型

## String

`SET key value`新建或者覆盖，只会返回ok



## Hash

## List

## Set

## Sorted Set

## Redis HyperLogLog

# Tip

## Redis的bin目录文件结构

- Redis-benchmark：Redis性能测试工具
- Redis-check-aof：文件修复工具
- Reids-check-rdb：文件修复工具
- Redis-cli：Redis命令行客户端
- Redis-sentinel：Redis集群管理工具
- Redis-server：Redis服务进程命令

## 设置RDB快照地址

RDB快照是把当前内存中的数据集快照写入磁盘，默认文件名是`dump.rdb`，默认存放在启动redis服务器时的当前目录。有两种方法修改`dump.rdb`的存放位置：

- 使用CONFIG SET dir命令来指定dump.rdb的存放位置

  ```
  # 修改dump.rdb 文件的存放路径为/usr/local/redis/conf
  127.0.0.1:6379> CONFIG SET dir /usr/local/redis/conf
  OK
  127.0.0.1:6379> CONFIG GET dir
  1) "dir"
  2) "/usr/local/redis/conf"
  ```

- 修改redis.conf配置文件里的dir配置项

  ```
  dir /usr/local/redis/conf
  ```

## 多线程

Redis6加入了多线程功能。执行命令依然是以单线程的形式，要开启redis的多线程功能，可以在redis.conf配置文件中加入以下配置项。

```
io-threads=do-reads yes #开启IO线程
io-threads 3            #设置IO线程数
```

**IO-threads线程数最多可以设置为Linux服务器中cpu核心数的3/4**。

如果设置超过这个数值，不会有明显的性能提升。可以使用`lscpu`查询服务器的cpu核心数。

