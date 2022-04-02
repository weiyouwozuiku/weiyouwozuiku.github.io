---
title: TiDB及其TiKV学习记录
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/KV存储/TiDB及其TiKV学习记录.jpeg
mathjax: true
date: 2022-04-02 00:40:15
tags: 
- TiKV
- TiDB
categories: KV存储
---

## 总体概览

整个TiDB体系有三个部分组成：PD Cluster、Storage Cluster、TiDB Server。 

![TiDB体系架构.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/KV存储/TiDB及其TiKV学习记录/TiDB体系架构.png)

## TiDB Server

### 主要功能

1. 处理客户端的连接
2. SQL语句的解析和编译
3. 关系型数据与KV的转化
4. SQL语句的执行
5. 在线DDL的执行
6. 垃圾回收

![TiDB_Server结构.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/KV存储/TiDB及其TiKV学习记录/TiDB_Server结构.png)
