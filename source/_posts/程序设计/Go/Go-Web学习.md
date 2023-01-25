---
title: Go-Web学习
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/Go-Web学习.png
mathjax: true
date: 2023-01-23 18:40:46
tags: Go
categories: 程序设计
---

## Beego

### controller抽象

Beego是基于MVC的，所以它定义了一个核心接口ControllerInterface。ControllerInterface定义了一个控制器**必须要解决什么问题**。同时ControllerInterface的默认实现Controller**提供了实现自定义控制器的各种辅助方法**，所以在Beego中，一般都是组合Controller来实现自己的Controller。

![Beego中ControllerInterface.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Beego中ControllerInterface.png)

 注意到用户虽然被要求组合Controller，但是路由注册和服务器启动是通过另一套机制来完成的。

![Beego中的Controller.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Beego中的Controller.png)

![Beego路由.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Beego路由.png)

ControllerInterface虽然是用户核心的接入点，但是从功能特性上来说，HttpServer和ControllerRegister才是核心。

![Beego_HttpServer.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Beego_HttpServer.png)

![Beego_controllerRegister.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Beego_controllerRegister.png)

- HttpServer：代表一个“服务器”，大多数的时候就是一个进程。
- ControllerRegister：真正干活的人。注册路由，路由匹配和执行业务代码都是透过它来完成的。

### Context抽象

用户请求操作和响应是通过Ctx来完成的。它代表的是**整个请求执行过程中的上下文**。

![Beego_ctx.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Beego_ctx.png)

![Beego_ctx使用.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Beego_ctx使用.png)

进一步，Beego将Context细分了几个部分：

- Input：定义了很多的处理请求相关的方法
- Output：定义了很多的响应有关的方法
- Response：对http.ResponseWriter的二次封装

### 核心抽象总结

- ControllerRegister最为基础，它**解决了路由注册和路由匹配这个基础问题**。
- Context和Controller为用户提供了丰富的API，**用于辅助构建系统**。
- HttpServer作为服务器抽象，**用户管理应用生命周期和资源隔离单位**。

## Gin

### IRoutes接口

核心接口IRoutes：**提供的是注册路由的抽象**。它的实现类Engine类似于ControllerRegister。

Use方法**提供了用户接入自定义逻辑的能力**，这个一般情况下也被看做是插件机制。

还额外提供了静态文件的接口。

**Gin没有Controller的抽象。**

### Engine实现

Engine可以看做是Beego中HttpServer和ControllerRegister的合体。

- 实现了路由树功能，提供了注册和匹配路由的功能
- 它本身可以作为一个Handler传递到http包中，用户启动服务器
- Engine的路由树功能本质上是依赖于mehtodTree

### methodTrees和methodTrees

methodTree才是真实的路由树。

Gin定义了mehtodTrees，它代表的是森林，即每个Http方法都对应到一棵树。

##
