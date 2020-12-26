---
title: Python高级特性
author: 不二
date: 2019-07-15 12:32:34
mathjax: true
tags: 
- Python
categories: 程序设计
---

## Python语法糖

---

- 对于取制定索引范围的操作，Python提供了切片(Slice)实现。例如：`L[0:3]`，这里表示的是从索引0开始取，知道索引3为止，**左闭右开的区间**。*如果第一个数是0，还可以省略*。`[:10:2]`其中的2表示每两位取一个。`[:]`表示原样复制一个list。

- 默认情况下，dict迭代的是key。如果要迭代value，可以使用`for value ind.values()`。如果需要同事迭代key和value，可以使用`for k,v in d.items()`。

  <!-- more -->

- 通过collections模块的Iterable类型判断是否是一个可迭代对象。`isinstance(变量,Iterable)`，这会返回一个布尔值。

- `enumerate`函数可以将一个list变成一个索引-元素树。

- 使用列表生成器来生成有一定复杂度的list。

  ```Python
  >>> [x * x for x in range(1, 11)]
  [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
  ```

  其中还可以加入if判断

  ```Python
  >>> [x * x for x in range(1, 11) if x % 2 == 0]
  [4, 16, 36, 64, 100]
  ```

  还可以嵌套使用

  ```Python
  >>> [m + n for m in 'ABC' for n in 'XYZ']
  ['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
  ```

- 要善于使用生成器，避免存有大量有规律的数组存在。启用方法就是将列表生成器的`[]`替换成`()`。可以通过`next()`函数来获取生成器的下一个返回值。generator是一个可迭代对象。直接调用返回的是一个生成器对象。

- 可以使用`yield`定义generation。每次调用`next()`时执行generation，遇到`yield`语句返回，再次执行从上次返回`yield`处继续执行。

- 用`for`循环调用generator时，发现拿不到generator的`return`语句的返回值。如果想要拿到返回值，必须捕获`StopIteration`错误，返回值包含在`StopIteration`的`value`中。

- `a, b = b, a + b`本质上等于a=b，同时b=a+b。先赋值，后改变a、b值。

- 生成器是`Iterator`对象，而`list`、`dict`、`str`虽然是`Iterable`，但不是`Iterator`。若想将其变成`Iterator`可以使用`iter()`函数。本质上**`Iterator`是一个数据流，可以被`next()`函数调用并不断返回下一个数据，直至抛出`StopIteration`错误。期间无法获知序列长度，惰性计算。**凡是可作用于`for`循环的对象都是`Iterable`类型，凡是可作用于`next()`函数的对象都是`Iterator`类型，它们表示一个惰性计算的序列。

- Python的`for`循环本质上就是通过不断调用`next()`函数实现的。
