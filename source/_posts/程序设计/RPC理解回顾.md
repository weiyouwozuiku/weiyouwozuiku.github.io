---
title: RPC理解回顾
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/RPC.png
mathjax: true
date: 2021-10-26 10:52:52
tags: 
- Python
- 分布式
- gRPC
- Thrift
categories: 程序设计
---

## 背景

单体服务在面对越来越多的流量显得力不从心，因此微服务应运而生。一个完整的大型服务会被打散成很多很多独立的小服务，每个小服务会由独立的进程去管理来对外提供服务，这就是「微服务」。

当用户的请求到来时，我们需要将用户的请求分散到多个服务去各自处理，然后又需要将这些子服务的结果汇总起来呈现给用户。那么服务之间该使用何种方式进行交互就是需要解决的核心问题。RPC 就是为解决服务之间信息交互而发明和存在的。

## RPC含义

RPC（Remote Procedure Call）,即远程过程调用，是一种分布式系统常用的通信方式。常见的多系统数据交互方式还包括：分布式消息队列、HTTP请求调用、数据库和分布式缓存等。

其中，RPC和HTTP调用都***没有经过中间件**，它们都是端对端系统之间直接数据交互。HTTP调用可以看做是一场特殊的RPC。

## 参考文献

1. 掘金小册，[深入理解 RPC : 基于 Python 自建分布式高并发 RPC 服务](https://juejin.cn/book/6844733722936377351)，2019