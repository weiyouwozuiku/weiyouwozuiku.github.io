---
title: Linux复习笔记
author: 不二
mathjax: true
date: 2021-01-25 18:55:28
tags: Linux
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/linux.jpg
categories: 程序设计
---

## Linux的体系结构

- 体系结构主要分为用户态（用户上层活动）和内核态
- 内核：本质是一段管理计算机硬件设备的程序
- 系统调用：内核的访问接口，是一种能再简化的操作
- 公用库函数：系统调用的组合拳
- Shell：命令解释器，可编程

在linux中使用`man`命令能查询到系统调用的接口，`man 2 syscalls`。其中2表示系统调用，3表示公共库函数。

## 常用命令

- find

  语法`find path [options] params`

  作用：在指定目录下查找文件

  `-name`表示通过名称查找文件，其支持通配符

  `-iname`表示忽略大小写

- grep

  

