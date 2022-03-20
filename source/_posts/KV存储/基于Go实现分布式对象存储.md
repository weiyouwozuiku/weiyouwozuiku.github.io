---
title: 基于Go实现分布式对象存储
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/KV存储/基于Go实现分布式对象存储.jpeg
mathjax: true
date: 2022-03-18 20:13:39
tags: 
- Go
- 分布式系统
categories: KV存储
---

## 背景

对象存储与传统网络存储的区别

|              | 网络文件系统      | 块存储               | 对象存储         |
| ------------ | ----------------- | -------------------- | ---------------- |
| 数据管理方式 | 以文件形式管理    | 以数据块形式管理     | 以对象的方式管理 |
| 访问数据方式 | 通过NFS等网络协议 | 通过数据块的地址访问 | 通过REST网络服务 |

对象存储中的一个对象通常包含3个部分：

- 对象的数据：该对象中存储的数据本身
- 对象的元数据：对象的描述信息
- 一个全局唯一标识符（即对象ID）：引用该对象

REST（Representational State Transfer），通过标准HTTP服务对网络资源提供一套预先定义的无状态操作。
