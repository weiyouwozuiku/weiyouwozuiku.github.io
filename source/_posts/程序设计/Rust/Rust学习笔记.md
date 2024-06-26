---
title: Rust学习笔记
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/Rust学习笔记.jpg
mathjax: true
date: 2023-07-02 23:11:23
tags: Rust
categories: 程序设计
---

- 在编译时，一切无法确定大小或者大小可以改变的数据，都无法安全地放在栈上，最好放在堆上。
- 虽然在栈上内存分配非常高效（预留和释放只需要操作寄存器，不涉及额外计算、不涉及系统调用），但是需要考虑调用栈的大小，避免栈溢出。
- 栈溢出：一旦当前程序的调用栈超出了系统允许的最大栈空间，无法创建新的栈来运行下一个要执行的函数，就会发生栈溢出，这时系统会被系统终止，产生崩溃信息。
- 当需要动态大小的内存时，只能使用堆。
- 当在堆上分配内存时，一般都会预留一些空间。堆上内存分配使用libc提供的malloc函数。
- 除了动态大小的内存，动态生命周期的内存也分配在堆上。
- 

# Tip

## 下载依赖修改镜像地址

字节跳动镜像仓库：https://rsproxy.cn/
