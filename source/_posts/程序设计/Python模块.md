---
title: Python模块
author: 不二
date: 2019-07-17 10:28:27
mathjax: true
tags: 
- Python
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/python.png
categories: 程序设计
---

## Python模块

- 在Python中，一个.py文件就是一个模块。好处是**提高了代码的可维护性，避免函数名和变量名的冲突，便于使用**。模块名与文件名相同。

- 利用包名来避免模块名相同的情况。**每个包下面都必须要有一个`__int__.py`文件**，否则就是只是一个普通文件夹，而非一个包。`__int__.py`文件本身就是一个模块，模块名为当前文件夹的名字。

- 任何模块代码的第一个字符串都被视为模块的文档注释。

- 进行模块测试时，使用以下代码：

 ```python
  if __name__=='__main__':
      test()
 ```

`if __name__ == '__main__'` 的意思是：当 `.py` 文件被直接运行时，`if __name__ =='__main__'` 之下的代码块将被运行；当 `.py` 文件以模块形式被导入时，`if __name__ =='__main__'`之下的代码块不会被运行。

- `__author__`、`__name__`等是特殊变量，模块定义的文档注释可以用特殊变量`__doc__`访问。变量名前面加上下划线的变量不应该直接被引用。
- 默认情况下，Python解释器会搜索当前目录、所有已安装的内置模块和第三方模块，搜索路径存放在`sys`模块的`path`变量中。
