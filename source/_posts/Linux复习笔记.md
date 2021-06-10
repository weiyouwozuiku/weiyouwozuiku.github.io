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

在Linux中使用`man`命令能查询到系统调用的接口，`man 2 syscalls`。其中2表示系统调用，3表示公共库函数。

## 常用命令

- find

  语法：`find path [options] params`

  作用：在指定目录下查找文件

  `-name`表示通过名称查找文件，其支持通配符

  `-iname`表示忽略大小写

- grep

  语法：`grep [options] pattern file`

  全称：Global Regular Expression Print

  作用：查找文件里符合条件的字符串
  
 - `|`管道操作符

   可将指令连接起来，前一个指令的输出作为后一个指令的输入

   使用管道主要的要点：

   - 只处理前一个命令正确输出，不处理错误输出
   - 右边的命令必须能够接收标准输入流，否则传递过程中数据会被抛弃
   - sed,awk,grep,cat,head,top,less,more,wc,join,sort,split等

- awk

  语法`awk [options] 'cmd' file`

  作用：一次读取一行文件，按输入分隔符进行切片，切成多个组成部分。将切片直接保存在内建的变量中，`$1,$2...`（$0表示行的全部）。支持对单个切片的判断，支持循环判断，默认分隔符为空格。

  例如：`awk '{print $1,$4}' netstat.txt` 或`awk '($1=="tcp" && $2==1)||NR==1 {print $0}' netstat.txt`或`awk -F "," '{print $2}' test.txt`或`grep 'patial\[true\]' a.info | grep -o 'engine\[[0-9a-z]*\]' | awk '{engineaar[$1]++END{for(i in enginearr) print i "\t" enginearr[i]}'`

- sed

  语法：`sed [options] 'sed command' filename`

  全称：stream editor，流编辑器

  适合用于对文本的行内容进行处理

  例如：`sed -i 's/^Str/String/' a.txt`默认不加`-i`则只是将替换后的内容打印出来，并不修改文件

  ​			`sed -i 's/Jack/me/g a.txt`sed命令默认只处理一行中第一个符合条件的字符，只有在其后加上`g`才能将一行中所有符合的字符进行替换  

