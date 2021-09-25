---
title: Scala学习笔记
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/scala-programming-language-logo-vector.png
mathjax: true
date: 2021-09-24 23:48:32
tags: Scala
categories: 程序设计
---

## 前言

Scala是一个多范式语言，其支持面向对象与函数式编程。

Scala不要求以`;`结尾。

```Scala
object HelloWorld{
  def main(args: Array[String]){
    println("Hello World...")
  }
}
```

上面的HelloWorld代码与Java最大的不同在于：Scala版的HelloWorld程序不通过static关键字把main方法标记成静态方法，而是使用object关键字创建了单件。
