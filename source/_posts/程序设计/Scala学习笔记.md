---
title: Scala学习笔记
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/scala-programming-language-logo-vector.png
mathjax: true
date: 2021-09-24 23:48:32
tags: Scala
categories: 程序设计
---

## 前言

Scala是一个多范式语言，其支持面向对象与函数式编程。

Scala具有一些特性：

- 同时支持命令式风格和类型推断
- 纯面向对象
- 强制合理的静态类型和类型推断
- 简洁而富有表现力
- 能和Java无缝的交互
- 基于精小的内核构建
- 高度的伸缩性，仅用少量代码就可以创建高性能的应用程序
- 具有强大、易用的并发模型

## 1. 探索Scala



Scala不要求以`;`结尾。

```Scala
object HelloWorld{
  def main(args: Array[String]){
    println("Hello World...")
  }
}
```

上面的HelloWorld代码与Java最大的不同在于：Scala版的HelloWorld程序不通过static关键字把main方法标记成静态方法，而是使用object关键字创建了单件。

`<10>.asInstanceOf[Double]`：Int类型的10转换成Double类型

`10.isInstanceOf[Int]`：返回10是否为Int类型

## Tip

本博客为《Scala实用指南》一书的整理与总结，仅供个人学习复习使用。