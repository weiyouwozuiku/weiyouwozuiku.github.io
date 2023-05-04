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

### SET

`SET key value`

新建或者覆盖，只会返回ok。对应非字符串类型的key会将原本的类型更新成string

### GET

`GET key`

### SETNX

`SETNX key value`

SETNX是Set If Not Exists（如果不存在，则SET）的简写。SETNX用于将key的值设为String类型的value，当key不存在时，返回1；若key已经存在，则SETNX不执行任何操作，返回0。

### SETEX

`SETEX key second value`

SETEX用于设置key对应的值为String类型的value，并指定此key对应的有效期，有效期的过期时间以秒（seconds）为单位。如果key对应的值已经存在，那么SETEX将覆盖旧值。

这个命令是**原子操作**。常用于缓存。

### SETRANGE

`SETRANGE key offset value`



## Hash

## List

## Set

## Sorted Set

## Redis HyperLogLog

# Tip

## 安装

1. 下载源码[7.0.11](https://js.xxooo.ml/https://github.com/redis/redis/archive/refs/tags/7.0.11.tar.gz)
2. 安装gcc基础依赖包`yum install gcc-c++`
3. 更新gcc版本`$ yum -y install centos-release-scl devtoolset-11-gcc devtoolset-11-gcc-c++ devtoolset-11-binutils $ scl enable devtoolset-11 bash $ echo "source /opt/rh/devtoolset-11/enable" >>/etc/profile` *从 CentOS8/RHEL8 开始，devtoolset 更名为 gcc-toolset*
4. 进入解压后的源码目录执行make
5. `$ make install PREFIX=/usr/local/redis`
6. 复制配置文件`$ mkdir -p /usr/local/redis/conf $ cp redis.conf /usr/local/redis/conf`
7. 环境变量中添加`export PATH=$PATH:/usr/local/redis/bin`
8. 使其成为自启动服务`$ vi /usr/local/redis/conf/redis.conf`
   1. daemonize默认值是false，表示Redis服务作为守护进程来运行，需要把它改成daemonize yes。
   2. pidfile默认值是pidfile/var/run/redis_6379.pid，表示当Redis服务以守护进程方式运行时，Redis服务默认会把pid写入/var/run/redis_6379.pid文件，Redis服务运行时该文件就存在，Redis服务一旦停止该文件就会自动删除，因而可以用来判断Redis服务是否正在运行。该配置项不用修改。
9. 将redis_init_script脚本复制到/etc/init.d目录下并修改脚本名字为redis`$ cp redis_init_script /etc/init.d/redis`
10. `$ chmod +x /etc/init.d/redis`
11. `$ chkconfig redis on`
12. `$ service redis start`
13. 设置密码，将conf配置文件中的`# requirepass foobared`取消注释，foobared就是密码的位置
14. 监听所有ip，将conf配置文件中的`bind 127.0.0.1`注释

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

