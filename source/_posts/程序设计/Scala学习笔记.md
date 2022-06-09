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

## 安装

```shell
brew install coursier/formulas/coursier && cs setup
```

## 探索Scala

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

### val vs var

val：值

final修饰

val 值名称：类型= xxx

var：变量

可以使用lazy关键字描述 定义惰性加载，实现懒加载。不加lazy是立即实例化，但是使用lazy则在使用时实例化。

**使用lazy只有在使用时才执行，可能导致错误不易被发现。**需要注意。

### 常见基本类型

| 数据类型 | 描述                                                         |
| :------- | :----------------------------------------------------------- |
| Byte     | 8位有符号补码整数。数值区间为 -128 到 127                    |
| Short    | 16位有符号补码整数。数值区间为 -32768 到 32767               |
| Int      | 32位有符号补码整数。数值区间为 -2147483648 到 2147483647     |
| Long     | 64位有符号补码整数。数值区间为 -9223372036854775808 到 9223372036854775807 |
| Float    | 32 位, IEEE 754 标准的单精度浮点数                           |
| Double   | 64 位 IEEE 754 标准的双精度浮点数                            |
| Char     | 16位无符号Unicode字符, 区间值为 U+0000 到 U+FFFF             |
| String   | 字符序列                                                     |
| Boolean  | true或false                                                  |
| Unit     | 表示无值，和其他语言中void等同。用作不返回任何结果的方法的结果类型。Unit只有一个实例值，写成()。 |
| Null     | null 或空引用                                                |
| Nothing  | Nothing类型在Scala的类层级的最底端；它是任何其他类型的子类型。 |
| Any      | Any是所有其他类的超类                                        |
| AnyRef   | AnyRef类是Scala里所有引用类(reference class)的基类           |

## Tip

本博客为《Scala实用指南》一书的整理与总结，仅供个人学习复习使用。