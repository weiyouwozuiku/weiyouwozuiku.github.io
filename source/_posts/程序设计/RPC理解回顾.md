---
title: RPC理解回顾
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/RPC.png
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

RPC是微服务的基础，但是RPC并不是只是存在于“微服务”中。

## RPC含义

RPC（Remote Procedure Call）,即远程过程调用，是一种分布式系统常用的通信方式。常见的多系统数据交互方式还包括：分布式消息队列、HTTP请求调用、数据库和分布式缓存等。

其中，RPC和HTTP调用都**没有经过中间件**，它们都是端对端系统之间直接数据交互。HTTP调用可以看做是一场特殊的RPC。只不过传统意义上的 RPC 是指长连接数据交互，而 HTTP 一般是指即用即走的短链接。

## RPC场景

### Nginx

Nginx 是互联网企业使用最为广泛的代理服务器。它可以为后端分布式服务提供负载均衡的功能，它可以将后端多个服务地址聚合为单个地址来对外提供服务。

Nginx默认与后端服务走的HTTP协议，但它还支持其他协议，如uWSGI（Python），fastcgi（PHP-fpm）等协议。这两种协议都是采用了比HTTP协议更加节省流量的二进制协议。

### Hadoop

最常见的 Hadoop 文件系统 hdfs，一般包括一个 NameNode 和多个 DataNode，NameNode 和 DataNode 之间就是通过一种称为 Hadoop RPC 的二进制协议进行通讯。

### HTTP与RPC

HTTP1.0 协议时，HTTP 调用还只能是短链接调用，一个请求来回之后连接就会关闭。HTTP1.1 在 HTTP1.0 协议的基础上进行了改进，引入了 KeepAlive 特性可以保持 HTTP 连接长时间不断开，以便在同一个连接之上进行多次连续的请求，进一步拉近了 HTTP 和 RPC 之间的距离。

当 HTTP 协议进化到 2.0 之后，Google 开源了一个建立在 HTTP2.0 协议之上的通信框架直接取名为 gRPC，也就是 Google RPC，这时 HTTP 和 RPC 之间已经没有非常明显的界限了。所以在后文我们不再明确强调 RPC 和 HTTP 请求调用之间的细微区别了，直接统一称之为 RPC。

## RPC交互流程

左边的客户端进程写 RPC 指令消息到内核的发送缓存中，内核将发送缓存中的数据传送到物理硬件 NIC，也就是网络接口芯片 (Network Interface Circuit)。NIC 负责将翻译出来的模拟信号通过网络硬件传递到服务器硬件的 NIC。服务器的 NIC 再将模拟信号转成字节数据存放到内核为套接字分配的接收缓存中，最终服务器进程从接收缓存中读取数据即为源客户端进程传递过来的 RPC 指令消息。

消息从用户进程流向物理硬件，又从物理硬件流向用户进程，中间还经过了一系列的路由网关节点。

上图呈现的只是 RPC 一次消息交互的上半场，下半场是一个逆向的过程，从服务器进程向客户端进程返回响应数据。完整的一次 RPC 过程如下图所示：

![RPC过程.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/RPC回顾理解/RPC过程.png)

## RPC协议



## 参考文献

1. 掘金小册，[深入理解 RPC : 基于 Python 自建分布式高并发 RPC 服务](https://juejin.cn/book/6844733722936377351)，2019
1. 极客时间，[RPC 实战与核心原理](https://time.geekbang.org/column/intro/100046201?tab=catalog)，2020