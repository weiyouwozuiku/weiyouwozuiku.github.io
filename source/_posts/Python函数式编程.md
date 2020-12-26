---
title: Python函数式编程
author: 不二
date: 2019-07-16 10:02:18
mathjax: true
tags:
- Python
- 函数式编程
categories: 程序设计
---

## Python函数式编程

- 函数式编程没有变量，在确定输入时，输出就已经确定了，没有因为变量的不确定导致的副作用。允许将番薯本身作为参数传入另一个函数，允许返回一个函数。

- 一个函数可以接收另一个函数作为参数，称之为高阶函数。

- `map()`函数接收两个参数，一个是函数，一个是Iterable。`map()`将传入的函数传入到序列的每一个元素，并将结果作为新的`Iterator`返回。

  <!-- more -->

- `reduce()`函数接收两个参数，将结果继续和序列的下一个元素做累计运算。

  ```python
  reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
  ```

- ```python
  from functools import reduce
  DIGITS = {'0': 0, '1': 1, '2': 2, '3': 3, '4': 4, '5': 5, '6': 6, '7': 7, '8': 8, '9': 9}
  def char2num(s):
      return DIGITS[s]
  def str2int(s):
      return reduce(lambda x, y: x * 10 + y, map(char2num, s))
  ```

- `filter()`函数用于过滤序列，接收一个函数和一个序列。`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素，返回的是一个惰性序列。需要用`list()`强制取出。

- 埃式筛法实现获取素数，思想是通过了除去所有数字的倍数，获得素数。实现代码如下：

  ```python
  def _odd_iter():
      n = 1
      while True:
          n = n + 2
          yield n
  def _not_divisible(n):
      return lambda x: x % n > 0
  def primes():
      yield 2
      it = _odd_iter() # 初始序列
      while True:
          n = next(it) # 返回序列的第一个数
          yield n
          it = filter(_not_divisible(n), it) # 构造新序列
  for x in primes():
      print(x)
  ```

- `sorted()`函数可以对list进行排序，接收一个list和一个key来定义自定义的序列，如`key=abs`，还有一个参数`reverse=True`。

- 当返回值为一个函数时，调用时需要加上参数后再加上()才能调用。参数会传入函数内部的函数，返回一个装有数据的函数闭包，调用外函数时，每次调用都会返回一个新的函数。返回的函数并不会立即执行，而是知道调用了f()才执行。**返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。**

- 如果一定要引用循环变量怎么办？方法是再创建一个函数，用该函数的参数绑定循环变量当前的值，无论该循环变量后续如何更改，已绑定到函数参数的值不变：

  ```python
  def count():
      def f(j):
          def g():
              return j*j
          return g
      fs = []
      for i in range(1, 4):
          fs.append(f(i)) # f(i)立刻被执行，因此i的当前值被传入f()
      return fs
  ```

- 关键字`lambda`表示匿名函数，冒号前面的`x`表示函数参数。匿名函数有个限制，就是只能有一个表达式，不用写`return`，返回值就是该表达式的结果。

- 函数对象有一个`_name_`属性，可以获取到函数的名字。

- 在代码运行期间动态增加功能的方式，称之为装饰器。

  ```python
  def setTag(tag):        #由于此装饰器需要参数，所以要再套一层
      def myDecorator(func):    #装饰器的核心，接受函数对象做参数，返回包装后的函数对象
          @functools.wraps(func)
          def myWrapper(name):    #包装的具体过程
              sign = "<" + tag + ">"
              return sign + func(name) + sign
          return myWrapper
      return myDecorator
  @setTag("div")    #用@标签在定义函数时套上装饰器
  def hello(name):
      return 'hello' + name
  print(hello('wang'))
  ```
```
  
  此时被装饰的函数的属性会被修改，改成装饰器的包装的具体函数的名字。可以使用`@functools.wraps(func)`，放在第二层嵌套后面。
  
- 偏函数可以降低函数的使用难度，由`functools`提供。作用是把一个函数的某些参数固定住（设置默认值），返回一个新的函数。可以接收函数对象、`*args`、`**kw`。如下所示：

  ```python
  int2=functools.partial(int,base=2)
```

  
