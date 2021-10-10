---
title: Django学习笔记
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/Django.jpg
mathjax: true
date: 2021-09-22 15:23:38
tags: Python
categories: 程序设计
---

## 前言

Django是一个开放源代码的Web应用框架。

检测是否安装Django模块：

```shell
python -m django --version
```

## 使用

初始化项目：`django-admin startproject mysite`。这样会在当前目录上生成名字为mysite的文件夹，并初始化整个项目。

### 项目目录结构

此时生成的文件夹下有以下内容：

- 最外层的 mysite/ 根目录只是你项目的容器， 根目录名称对 Django 没有影响，你可以将它重命名为任何你喜欢的名称。
- manage.py: 一个让你用各种方式管理 Django 项目的命令行工具。你可以阅读 django-admin 和 manage.py 获取所有 manage.py 的细节。
- 里面一层的 mysite/ 目录包含你的项目，它是一个纯 Python 包。它的名字就是当你引用它内部任何东西时需要用到的 Python 包名。 (比如 mysite.urls).
- mysite/\_\_init\_\_.py：一个空文件，告诉 Python 这个目录应该被认为是一个 Python 包。如果你是 Python 初学者，阅读官方文档中的 更多关于包的知识。
- mysite/settings.py：Django 项目的配置文件。如果你想知道这个文件是如何工作的，请查看 Django 配置 了解细节。
- mysite/urls.py：Django 项目的 URL 声明，就像你网站的“目录”。阅读 URL调度器 文档来获取更多关于 URL 的内容。
- mysite/asgi.py：作为你的项目的运行在 ASGI 兼容的 Web 服务器上的入口。阅读 如何使用 ASGI 来部署 了解更多细节。
- mysite/wsgi.py：作为你的项目的运行在 WSGI 兼容的Web服务器上的入口。阅读 如何使用 WSGI 进行部署 了解更多细节。