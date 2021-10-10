---
title: Python函数
author: 不二
date: 2019-07-13 10:38:31
mathjax: true
tags: Python
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/python2.jpg
categories: 程序设计
---

## Python函数

- 调用函数时，如果传入的参数或者类型不对，Python会报TypeError的错误。

- `int(参数)`将参数转换成整数，其他类型也用类似的函数进行转换。

- 函数名是指向一个函数对象的引用。可以将函数赋值给另一个对象，效果是给这个函数起了别名。

- 函数定义
  ```python
  def 函数名（参数列表）：
  	函数逻辑
  	return 返回值
  ```

- 没有返回值的函数，其实也会返回结果，是None。返回return None可以简写为return。

<!-- more -->

- 未实现的函数可以将函数体先写成`pass`。`pass`表示什么都不做，只有起占位符的作用。

- 定义函数时可以进行数据类型检查，使用`isinstance()`实现。

  ```python
  if not isinstance(需校验参数,(数据类型集合或单一数据类型)):
      raise TypeError('bad operand type')
  ```

- 函数return多个值，本质上是返回了一个tuple

- 默认参数同java。必选参数在前，默认参数在后。

- 当不按照顺序进行传参时，需要把参数名写上。

- **默认参数必须指向不变对象！**

- 在参数名前面加上\*，参数就变成了可变参数。如果传递的参数原本就是一个tuple或者list，那么需要在参数名前加上\*，使其编程可变参数。

- 关键字参数就是在参数名前面加上`**`。如果传入一个dict集合，在参数前加上`**`。

- 命名关键字参数，可以限制关键字参数中只接收需要的关键字参数。

  ```Python
  def 函数名(参数1,参数2,*,参数三,参数四)
  ```

  上面那个例子的参数三、参数四就是命名关键字，传入时必须要传入参数名。可以有缺省值。

- 通过尾递归来优化递归造成的栈溢出。尾递归是指，在函数返回的时候，调用自身本身，并且，`return`语句不能包含表达式。这样，编译器或者解释器就可以把尾递归做优化，使递归本身无论调用多少次，都只占用一个栈帧，不会出现栈溢出的情况。重点是把每一步计算的值传递到函数中去。如下所示：

	```python
def fact(n):
      return fact_iter(n, 1)
  def fact_iter(num, product):
      if num == 1:
          return product
      return fact_iter(num - 1, num * product)
  ```
