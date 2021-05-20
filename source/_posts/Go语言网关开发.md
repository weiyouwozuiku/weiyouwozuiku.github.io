---
title: Go语言网关开发
author: 不二
mathjax: true
date: 2020-12-18 14:18:44
tags: 
- Go
img:
categories: 程序设计
---

## 网络基础

### 协议模型

![OSI七层网络协议.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/Go%E8%AF%AD%E8%A8%80%E7%BD%91%E5%85%B3%E5%BC%80%E5%8F%91/OSI%E4%B8%83%E5%B1%82%E7%BD%91%E7%BB%9C%E5%8D%8F%E8%AE%AE.png)

![经典协议与数据包.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/Go%E8%AF%AD%E8%A8%80%E7%BD%91%E5%85%B3%E5%BC%80%E5%8F%91/%E7%BB%8F%E5%85%B8%E5%8D%8F%E8%AE%AE%E4%B8%8E%E6%95%B0%E6%8D%AE%E5%8C%85.png)

### TCP连接

TCP的三次握手最主要目的是保证连接是双工的，可靠更多的是通过重传机制来保证。因为连接是全双工的，双方必须都收到对方的FIN包及确认才可关闭。

![三次握手连接.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/Go%E8%AF%AD%E8%A8%80%E7%BD%91%E5%85%B3%E5%BC%80%E5%8F%91/%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B%E8%BF%9E%E6%8E%A5.png)

![四次回收关闭.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/Go%E8%AF%AD%E8%A8%80%E7%BD%91%E5%85%B3%E5%BC%80%E5%8F%91/%E5%9B%9B%E6%AC%A1%E5%9B%9E%E6%94%B6%E5%85%B3%E9%97%AD.png)

MSL：Maximum Segment Lifetime,30秒到1分钟。

TIME-WAIT等待2MSL是保证TCP协议的全双工连接能够可靠关闭。保证这次连接的重复数据段从网络中消失。

大量出现close_wait的原因：

1. 一般出现在被动关闭方
2. 并发请求太多导致
3. 被动关闭方未及时释放端口资源导致

### TCP流量控制

由于通讯双方，网速不同。通讯方任一方发送过快都会导致对方消息处理不过来，所以就需要把数据放在缓冲区中。如果缓冲区满了，发送方还在发送，那接收方只能把数据包丢弃。因此需要控制发送速率。

缓冲区大小称之为接收端口，用变量win表示。如果win=0，则发送方停止发送。

### TCP拥塞控制

拥塞控制是调节网络的负载。接收方网络资源繁忙，因未及时响应ACK导致发送方重传大量数据，这样将会导致网络更加拥堵。**拥塞控制是动态调整win大小，不知是依赖缓冲区大小确定窗口大小**。

#### 慢开始和拥塞避免

![慢开始与拥塞避免.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/Go%E8%AF%AD%E8%A8%80%E7%BD%91%E5%85%B3%E5%BC%80%E5%8F%91/%E6%85%A2%E5%BC%80%E5%A7%8B%E4%B8%8E%E6%8B%A5%E5%A1%9E%E9%81%BF%E5%85%8D.png)

#### 快速重传和快速恢复

![快重传和快恢复.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/Go%E8%AF%AD%E8%A8%80%E7%BD%91%E5%85%B3%E5%BC%80%E5%8F%91/%E5%BF%AB%E9%87%8D%E4%BC%A0%E5%92%8C%E5%BF%AB%E6%81%A2%E5%A4%8D.png)

### 粘包和拆包

发生的情况：

- 应用程序写入的数据大于套接字缓冲区大小，这将会发生拆包。
- 应用程序写入数据小于套接字缓冲区大小，网卡将应用多次写入的数据发送到网络上，这将会发生粘包。