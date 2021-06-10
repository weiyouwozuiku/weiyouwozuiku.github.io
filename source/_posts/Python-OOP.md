---
title: Python OOP
author: 不二
date: 2019-07-17 10:59:38
mathjax: true
tags: 
- Python
- OOP
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/python3.jpg
categories: 程序设计
---

## Python面向对象编程

```Python
class ClassName(继承的类名):
    def __init__(变量):
        self.类内变量=变量
```

- 没有合适的继承类，就使用`object`类，这是所有类最终都会继承的类。创建实例是通过类名+()实现的。可以自由的给一个实例变量绑定一个属性。

- **注意到`__init__`方法的第一个参数永远是`self`，表示创建的实例本身，因此，在`__init__`方法内部，就可以把各种属性绑定到`self`，因为`self`就指向创建的实例本身。**

  <!-- more -->

- **在类中定义的函数只有一点不同，就是第一个参数永远是实例变量`self`。并且，调用时，不用传递该参数。**

- 类里面定义的数据成员可以将变量名通过加上`__`变成私有变量。如果硬是要访问，可以通过`_类名__变量名`

- 单`_`表示的变量意思是，虽然我可以被访问，但请不要访问我。

- 相较于一般的静态语言中的多态，python不强制要求传入的参数必须属于同一父类，而是只要你有所调用的方法，那么你就可以传入，都被视为“file-like object“。

- 通过`type()`函数判断对象所属类型。判断函数类型时通过`type()`的内置常量。如：`FunctionType`、`BuiltinFunctionType`、`LambdaType`、`GeneratorType`。

- `isinstance()`判断的是一个对象是否是该类型本身，或者位于该类型的父继承链上。**总是优先使用`isinstance()`判断类型，可以将指定类型及其子类“一网打尽”。**

- 如果要获得一个对象的所有属性和方法，可以使用`dir()`函数，它返回一个包含字符串的list。

- 我们自己写的类，如果也想用`len(myObj)`的话，就自己写一个`__len__()`方法。

- 配合`getattr()`、`setattr()`以及`hasattr()`，我们可以直接操作一个对象的状态。

- 直接在class中定义属性，此时的属性就是类属性，所有对象都可以访问到类属性。但是如果给对象的类属性重新赋值，会覆盖这个对象的类属性，但其他对象依然可以访问到。当你删除实例属性后，再使用相同的名称，访问到的将是类属性。

- 因为动态语言的特殊性，可以在创建实例后，动态的给对象添加属性或者方法。动态给对象绑定方法的代码如下：

  ```python
  >>> def set_age(self, age): # 定义一个函数作为实例方法
  ...     self.age = age
  ...
  >>> from types import MethodType
  >>> s.set_age = MethodType(set_age, s) # 给实例绑定一个方法
  >>> s.set_age(25) # 调用实例方法
  >>> s.age # 测试结果
  25
  ```

- 如果想给所有类对象绑定函数使用如下代码:

  ```python
  >>> def set_score(self, score):
  ...     self.score = score
  ...
  >>> Student.set_score = set_score
  ```

- **Python允许在定义class的时候，定义一个特殊的`__slots__`变量，来限制该class实例能添加的属性，对子类不起作用。**

  ```Python
  >>> class Student(object):
  ...     __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称
  ...
  ```

- Python内置的`@property`装饰器就是负责把一个方法变成属性调用的。把一个getter方法变成属性，只需要加上`@property`就可以了，此时，`@property`本身又创建了另一个装饰`@变量名.setter`，负责把一个setter方法变成属性赋值。

- 只定义getter方法，不定义setter方法就是一个只读属性。

- Python支持多继承，可以将共有的方法定义成一个方法类。基于Python的动态特性，只要拥有相同方法名的类，即可体现出类的多态性。

- `MixIn`设计模式是主线通过单一继承实现，但是需要“混入”额外的功能，可以通过多重继承来实现。

- 直接打印一个对象的信息，会调用类中的`__str__()`。直接显示变量调用，会调用类中的`__repr__()`，两者的区别是`__str__()`返回用户看到的字符串，而`__repr__()`返回程序开发者看到的字符串，也就是说，`__repr__()`是为调试服务的。样例代码如下：

  ```python
  class Student(object):
      def __init__(self, name):
          self.name = name
      def __str__(self):
          return 'Student object (name=%s)' % self.name
      __repr__ = __str__
  ```

- 如果一个类想被用于`for ... in`循环，类似list或tuple那样，就必须实现一个`__iter__()`方法，该方法返回一个迭代对象，然后，Python的for循环就会不断调用该迭代对象的`__next__()`方法拿到循环的下一个值，直到遇到`StopIteration`错误时退出循环。

  ```python
  class Fib(object):
      def __init__(self):
          self.a, self.b = 0, 1 # 初始化两个计数器a，b
  
      def __iter__(self):
          return self # 实例本身就是迭代对象，故返回自己
  
      def __next__(self):
          self.a, self.b = self.b, self.a + self.b # 计算下一个值
          if self.a > 100000: # 退出循环的条件
              raise StopIteration()
          return self.a # 返回下一个值
  ```

- 要表现得像list那样按照下标取出元素，需要实现`__getitem__()`方法：

  ```python
  class Fib(object):
      def __getitem__(self, n):
          a, b = 1, 1
          for x in range(n):
              a, b = b, a + b
          return a
  ```

  如果要使用切片功能，需要在`__getitem__()`函数中对传入的参数类型进行判断。使用`if isinstance(参数，slice)：`，可以获取切片的start参数和stop参数。

- 对于在类中找不到的属性，python会调用`__getattr__`函数。默认返回None。可以用于url的动态调用，如：

  ```python
  class Chain(object):
  
      def __init__(self, path=''):
          self._path = path
  
      def __getattr__(self, path):
          return Chain('%s/%s' % (self._path, path))
  
      def __str__(self):
          return self._path
  
      __repr__ = __str__
  ```

- 任何类，只需要定义一个`__call__()`方法，就可以直接对实例进行调用。你完全可以把对象看成函数，把函数看成对象，因为这两者之间本来就没啥根本的区别。

- 通过`callable()`函数，我们就可以判断一个对象是否是“可调用”对象。

- 枚举类，`value`属性则是自动赋给成员的`int`常量，默认从`1`开始计数。

  ```python
  from enum import Enum
  Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
  for name, member in Month.__members__.items():
      print(name, '=>', member, ',', member.value)
  ```

- `@unique`装饰器可以帮助我们检查保证没有重复值。自定义枚举类：

  ```python
  @unique
  class Weekday(Enum):
      Sun = 0 # Sun的value被设定为0
      Mon = 1
      Tue = 2
      Wed = 3
      Thu = 4
      Fri = 5
      Sat = 6
  ```

- `type()`函数可以查看一个类型或变量的类型，还可以创建class。

- 要创建一个class对象，`type()`函数依次传入3个参数：

  1. class的名称；

  2. 继承的父类集合，注意Python支持多重继承，如果只有一个父类，别忘了tuple的单元素写法；

  3. class的方法名称与函数绑定，这里我们把函数`fn`绑定到方法名`hello`上。

     ```python
     >>> def fn(self, name='world'): # 先定义函数
     ...     print('Hello, %s.' % name)
     ...
     >>> Hello = type('Hello', (object,), dict(hello=fn)) # 创建Hello class
     ```

- 可以使用metaclass控制类的创建行为，你可以把类看成是metaclass创建出来的“实例”。按照默认习惯，metaclass的类名总是以Metaclass结尾，以便清楚地表示这是一个metaclass。

  ```python
  
  ```

- 在子类中调用父类方法时，可以通过如下语句进行编写。

  ```python
  super(child_class,child_object).parent_attribute(arg)
  ```

  第一个参数表示调用父类的起始处，也就是子类的名字。

  第二个参数表示类实例（一般使用self）。

  第三个参数表示需要传入父类函数的参数，只有self是可以不用传入。

  
