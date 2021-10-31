---
title: QUIC协议整理
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/QUIC.jpg
mathjax: true
date: 2021-10-29 20:41:38
tags: 
- QUIC
- 计算机网络
categories: 程序设计
---

## 简介

众所周知，QUIC（Quick UDP Internet Connection）是谷歌制定的一种互联网传输层协议，它基于UDP传输层协议，同时兼具TCP、TLS、HTTP/2等协议的可靠性与安全性，可以有效减少连接与传输延迟，更好地应对当前传输层与应用层的挑战。

## 背景

