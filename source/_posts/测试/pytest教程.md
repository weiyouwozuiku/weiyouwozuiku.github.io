---
title: pytest教程
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/测试/pytest教程.png
mathjax: true
date: 2022-03-04 18:48:02
tags:
- Python
- 测试开发
categories: 测试
---

## 背景

pytest是动态编程语言Python专用的测试框架，它具有易于上手、功能强大、第三方插件丰富、效率高、可扩展性好、兼容性强等特点。

本博客不介绍相对基础及其所见即所得的内容，只记录学习过程中自认为相对重要的点或步骤，依据《pytest测试实战》整理，推荐阅读原书。

## 测试概念定义

- 单元测试：检查一小段代码（如一个函数、或一个类）的测试。
- 集成测试：检查大段代码（如多个类，或一个子系统）的测试。集成测试的规模介于单元测试与系统测试之间。
- 系统测试：检查整个系统的测试，通常要求测试环境竟可能接近最终用户的使用环境。
- 功能测试：检查单个系统功能的测试。
- 皮下测试：不针对最终用户界面，而是针对用户界面以下的接口的测试。
- 冒烟测试：可以了解当前系统中是否存在大的缺陷。通常冒烟测试不会包含全套测试，只选择可以快速出结果的测试子集 。

## 基础

### 安装

```shell
pip3 install pytest
```

### 命令行参数

可以在pytest命令后加上`-v`和`--verbose`查看更加详细的测试信息。

`--collect-only`选项展示哪些测试用例会被运行。

`-k`选项允许使用表达式来指定运行的测试用例，如`pytest -k "fun1 or fun2"`

`-m`选项用来标记测试并分组。使用什么标记名自行决定，假设想要使用run_these_please，则可以使用@pytest.mark.run_these_please这样的装饰器来标记。同时这个标记类似`k`选项支持表达式，如`-m "mark1 and not mark2"`。

`-x`选项在遇错误后全局停止后续测试。

`--maxfail=num`选项表示明确指定可以失败几次。

`–-tb=no`选项表示关系错误堆栈回溯。

`-s`表示允许终端在测试运行时输出某些结果。-s  shortcut for --capture=no.

`--capture=method` per-test capturing method: one of fd|sys|no|tee-sys. 

`--capture=fd`表示若文件描述符为1或2[^1]，则会被输出至临时文件中。

`–capture==sys`表示`sys.stdout/stderr`输出至内存。

`-l`或`--showlocals`表示在测试失败时打印局部变量名和其值。

`--lf, --last-failed `执行上次失败的case。

`--ff, --failed-first`表示先执行最后一次失败的case再执行其他case。

`-v, --verbose  `表示详细输出信息，每个文件中的每个测试用例都占一行（默认每个文件占一行）。

`-q, --quiet `表示简化输出信息，与上一个命令相反。

`--tb=style`   traceback print mode (auto/long/short/line/native/no).

- `short`表示进输出assert这一行以及系统判定内容（不显示上下文）
- `line`模式只使用一行输出所有的错误信息 
- `no`则直接屏蔽全部回溯信息
- `long`输出最为详尽的回溯信息
- `auto`默认值，如果多个测试用例失败，仅打印最后一个和第一个测试用例的回溯信息，long格式展示
- `native`只输出Python标准库的回溯信息

`--durations=N`  show N slowest setup/test durations (N=0 for all).

`-rs`显示跳过原因，需要之前设置了跳过原因

-r chars              show extra test summary info as specified by chars:
                        (p/P), or (A)ll. (w)arnings are enabled by default (see

### 命名规则

只要遵循pytest的命名规则就可以自动搜索所有待执行的测试用例。主要的命名规则如下：

- 测试文件应当命名为`test_<something>.py`或者`<something>_test.py`
- 测试函数、测试类方法应当命名为`test_<something>`
- 测试类应当命名为`Test<Something>`

测试输出的`inifile`表示配置文件。

### 结果表示

执行结果的表示：

- `.`表示PASSED通过
- `F`表示Failure（失败），也有可能是XPASS状态与strict选项冲突造成的失败
- `E`表示error（测试用例之外的代码触发的异常），可能是fixture引起，也可能是因为hook函数
- `s`表示skip（跳过），`@pytest.mark.skip()`或`@pytest。Mark。skipf()`指定跳过测试的条件
- `x`表示xfail（预期失败，并且确实失败），使用`@pytest.mark.xfail()`指定你认为会失败的用例
- `X`表示xpass（预期失败但通过，不符合预期）

## 编写测试函数

推荐将test目录放在src文件夹下，其中存放所有与pytest相关的代码。

`assert`语法会判断后面的表达式，将其值转变为布尔值。

`with pytest.raises(TypeError):`表示无论with中的内容是什么都发生了预期中的`TypeError`异常。

`with pytest.raises(ValueError) as a:`表示with中执行后返回TypeError异常后，这里捕获并将参数传递给a变量。通过a变量判断。

marker机制是支持一对多或多对一。

marker的装饰器是`@pytest.mark.<marker名称>`

pytest内置了一些标记：

- `skip`和`skipif`允许跳过不希望运行的测试。
  - `@pytest.mark.skip(reason='...')`
  - `@pytest.mark.skipif(task.__version__<'0.2.0',reason='..')`当task版本小于0.2.0时跳过测试，这里支持任意表达式
  - 上述两种跳过方式都支持reason传递
- `sfail`表示运行此测试，预期其失败

### 批量测试

使用`@pytest.mark.parametrize(argnames,argvalues)`

可以使用完整的测试标识来重新指定需要的测试，形如`pytest -v "test_add_variety.py::test_add_3[eat eggs-BrIaN-False]"`

![参数化kv.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/测试/pytest教程/参数化kv.png)

![参数化运行.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/测试/pytest教程/参数化运行.png)

在给`@pytest.mark.parametrize()`装饰器传入列表参数时，还可以在参数旁定义一个id来作为标识，语法`pytest.param(<value>,id="something")`

### 测试方法

- 单个测试用例：可以使用`pytest ****.py::test_name`执行
- 单个目录：以目录作为pytest参数
- 单个文件/模块：pytest后跟路径+文件名
- 单个测试类：可以将测试一个接口的测试用例放在一个类中，运行时在文件名后加上::以及类名
- 单个测试类中的方法：文件名后加上::以及类名加上::加上方法名

## pytest Fixture

`pytest`是在测试函数运行前后，由pytest执行的外壳函数。

`@pytest.fixture()`装饰器用于声明函数是一个`fixture`。如果测试函数的参数列表包含`fixture`名，那么pytest会检测到，并在测试函数执行之前执行该`fixture`。

pytest优先搜索该测试所在模块，然后搜索`conftest.py`。

在外层目录编写`conftest.py`可以让内层测试用例使用。

`conftest.py`不能被import引入，对于pytest来说只是一个插件库。

可以借助`yield`的用法特性，在执行完`fixture`中前置逻辑后到达yield，等测试函数执行完毕，执行fixture的后续逻辑。

使用`--setup-show`回溯fixture的执行过程。 回溯信息中的F和S标识fixture的作用域分别是函数级别以及会话级别。

fixture中`scope`为可选参数，其有四个待选值，默认为function，：

- function：每个测试函数运行一遍
- class：每个测试类运行一遍，测试类中的所有类方法共享这个fixture
- module：模块级别的fixture运行一遍，无论模块中有多少测试函数、类方法或其他fixture都共享这个fixture
- session：每个会话运行一次

scope参数是在定义fixture定义的，而不是在调用fixture时定义的。因此使用fixture的测试函数是无法改变fixture的作用范围的。

**fixture只能使用同级别或比自己级别更好的fixture**。

单个函数使用fixture是在传参中用fixture定义的名字，类使用fixture需要用装饰器实现`@pytest.mark.usefixtures(<fixture名字>)`。

`tmpdir_factory`的作用范围是会话级别，`tmpdir`的作用范围是函数级别。

## 内置Fixtrue



## 插件

## 配置

## pytest与其他工具的搭配使用

## Tip

[^1]:在Linux系统中，一切设备都看作文件。而每打开一个文件，就有一个代表该打开文件的文件描述符。程序启动时默认打开三个I/O设备文件：标准输入文件stdin，标准输出文件stdout，标准错误输出文件stderr，分别得到文件描述符 0, 1, 2。
