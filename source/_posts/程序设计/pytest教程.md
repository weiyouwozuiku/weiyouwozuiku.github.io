---
title: pytest教程
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/pytest教程.png
mathjax: true
date: 2022-03-04 18:48:02
tags:
- Python
- 测试开发
categories: 程序设计
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

## 基础

### 安装

```shell
pip3 install pytest
```

### 命令行参数

可以在pytest命令后加上`-v`和`--verbose`查看更加详细的测试信息。

单个测试用例可以使用`pytest ****.py::test_name`执行。

`--collect-only`选项展示哪些测试用例会被运行。

`-k`选项允许使用表达式来指定运行的测试用例，如`pytest -k "fun1 or fun2"`

`-m`选项用来标记测试并分组。使用什么标记名自行决定，假设想要使用run_these_please，则可以使用@pytest.mark.run_these_please这样的装饰器来标记。同时这个标记类似`k`选项支持表达式，如`-m "mark1 and not mark2"`。

`-x`选项在遇错误后全局停止后续测试。

`--maxfail=num`选项表示明确指定可以失败几次。

`–-tb=no`选项表示关系错误堆栈回溯。

`-s`表示允许终端在测试运行时输出某些结果。

`--capture=method`

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

## pytest Fixture

## 内置Fixtrue

## 插件

## 配置

## pytest与其他工具的搭配使用
