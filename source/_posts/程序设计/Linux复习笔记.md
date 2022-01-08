---
title: Linux复习笔记
author: 不二
mathjax: true
date: 2021-01-25 18:55:28
tags: Linux
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/linux.jpg
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

### find

语法：`find path [options] params`

作用：在指定目录下查找文件

`-name`表示通过名称查找文件，其支持通配符

`-iname`表示忽略大小写

### grep

语法：`grep [options] pattern file`

全称：Global Regular Expression Print

作用：查找文件里符合条件的字符串

### `|`管道操作符

可将指令连接起来，前一个指令的输出作为后一个指令的输入

使用管道主要的要点：

- 只处理前一个命令正确输出，不处理错误输出
- 右边的命令必须能够接收标准输入流，否则传递过程中数据会被抛弃
- sed,awk,grep,cat,head,top,less,more,wc,join,sort,split等

### awk

语法`awk [options] 'cmd' file`

作用：一次读取一行文件，按输入分隔符进行切片，切成多个组成部分。将切片直接保存在内建的变量中，`$1,$2...`（$0表示行的全部）。支持对单个切片的判断，支持循环判断，默认分隔符为空格。

例如：`awk '{print $1,$4}' netstat.txt` 或`awk '($1=="tcp" && $2==1)||NR==1 {print $0}' netstat.txt`或`awk -F "," '{print $2}' test.txt`或`grep 'patial\[true\]' a.info | grep -o 'engine\[[0-9a-z]*\]' | awk '{engineaar[$1]++END{for(i in enginearr) print i "\t" enginearr[i]}'`

### sed

语法：`sed [options] 'sed command' filename`

全称：stream editor，流编辑器

适合用于对文本的行内容进行处理

例如：`sed -i 's/^Str/String/' a.txt`默认不加`-i`则只是将替换后的内容打印出来，并不修改文件

`sed -i 's/Jack/me/g a.txt`sed命令默认只处理一行中第一个符合条件的字符，只有在其后加上`g`才能将一行中所有符合的字符进行替换  

`sed -i '/^ *$/d' a.txt`表示删除多余的空行

### nohup

nohup 英文全称 no hang up（不挂起），用于在系统后台不挂断地运行命令，退出终端不会影响程序的运行。

nohup 命令，在默认情况下（非重定向时），会输出一个名叫 nohup.out 的文件到当前目录下，如果当前目录的 nohup.out 文件不可写，输出重定向到 $HOME/nohup.out 文件中。

```shell
 nohup Command [ Arg … ] [　& ]
 ```

 参数说明：

- Command：要执行的命令。

- Arg：一些参数，可以指定输出文件。

- &：让命令在后台执行，终端退出后命令仍旧执行。

如果要停止运行，你需要使用以下命令查找到 nohup 运行脚本到 PID，然后使用 kill 命令来删除：`ps -aux | grep "<执行脚本名称>"`。

参数说明：

- a : 显示所有程序
- u : 以用户为主的格式来显示
- x : 显示所有程序，不区分终端机

2>&1 解释：

将标准错误 2 重定向到标准输出 &1 ，标准输出 &1 再被重定向输入到 runoob.log 文件中。

- 0 – stdin (standard input，标准输入)
- 1 – stdout (standard output，标准输出)
- 2 – stderr (standard error，标准错误输出)


### 查看文件夹之间的不同文件

直接使用过diff可能会导致差异文件过多，导致的不可查。可以使用`vimdiff <(cd 文件夹1; find . | sort) <(cd 文件夹2; find . | sort)`

## Shell编写

