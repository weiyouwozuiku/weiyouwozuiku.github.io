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

![Gin_IRoutes.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Gin_IRoutes.png)

核心接口IRoutes：**提供的是注册路由的抽象**。它的实现类Engine类似于ControllerRegister。

Use方法**提供了用户接入自定义逻辑的能力**，这个一般情况下也被看做是插件机制。

还额外提供了静态文件的接口。

**Gin没有Controller的抽象。**

### Engine实现

Engine可以看做是Beego中HttpServer和ControllerRegister的合体。

- 实现了**路由树功能**，提供了注册和匹配路由的功能
- **它本身可以作为一个Handler传递到http包中**，用于启动服务器
- Engine的路由树功能本质上是依赖于**mehtodTree**

### methodTrees和methodTrees

![Gin_methodTrees.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Gin_methodTrees.png)

![Gin_methodTrees.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Gin_methodTrees.png)

methodTree才是真实的路由树。

Gin定义了mehtodTrees，它代表的是森林，即每个Http方法都对应到一棵树。

### HandlerFunc和HandlersChain

HandlerFunc定义了**核心抽象——处理逻辑**。

在默认情况下，它代表了注册路由的业务代码。HandlersChain则是构建了责任链模式。

![Gin_HandlerFunc.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Go/Go-Web学习/Gin_HandlerFunc.png)

### Context抽象

Context代表了执行的上下文，提供丰富的API。

- 处理请求的API，代理的是以Get和Bind为前缀的方法
- 处理相应的API，例如返回JSON或者XML响应的方法
- 渲染页面，如HTML方法

## Iris

Application是Iris的核心抽象，它代表的是“应用”。实际上这个语义更接近Beego的HttpServer和Gin的Engine。

它提供了：

- **生命周期控制功能**，如Shutdown等方法
- **注册路由的API**

### 路由相关

和处理路由相关的三个抽象：

- Route：直接代表了已经注册的路由。在Beego和Gin里面，对应的是路由树的节点
- APIBuilder：创建Route的Builder模式，Party也是它创建的
- repository：存储了所有的Routes，有点接近Gin的methodTrees的概念

### Context抽象

Context代表上下文，其本身也提供了各种处理请求和响应的方法。比较有特色的是它的Context**支持请求级别的添加Handler**，即AddHandler方法。

## Echo

Echo是其内部的一个结构体，类似于Beego的HttpServer和Gin的Engine：

- 暴露了注册路由的方法，但是他并不是路由树的载体
- 生命周期管理，如：Shutdown和Start方法

在Echo里面有两个相似的字段：

- router：代表路由树
- routers：根据Host来进行分组组织，可以看做是近似于namespace之类的概念，既是**一种组织方式也是一种隔离机制**。

### Route和node

Router代表的是路由树，node代表的是路由树上的节点，也就是实际存在的路由本身。

node里面存在三种匹配方式：

- staticChild
- paramChid
- anyChild

利用这种设计可以轻松实现路由优先级和路由冲突检测。
