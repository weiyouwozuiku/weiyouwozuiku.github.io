---
title: 《Go语言高级编程》笔记
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/《Go语言高级编程》笔记.png
mathjax: true
date: 2023-04-02 16:23:39
tags: Go
categories: 程序设计
---

## Go和Web

设计路由的时候需要规避一些会导致路由冲突的情况。

考虑到字典树的深度，可以在初始化时对参数的数量进行限制，httprouter中限制路由中的参数数目不超过255。
