---
title: Docker与K8s
author: 不二
mathjax: true
date: 2021-04-02 10:44:11
tags: 
- Docker
- Kubernetes
img:
categories: 云计算
---

## Docker基础

docker相较于别的paas技术核心优势在于镜像，其是一套操作系统+应用程序，保证了环境的一致性，无需根据不同的开发语言选择不同打包方式。

在Docker中打包镜像使用Dockerfile技术，即编写dockerfile即可。这其中有几个重要的参数：

- From：选择操作系统镜像
- COPY：表示将本地的可执行文件拷贝进镜像中
- EXPOSE：设置端口
- USER：设置用户
- ENTRYPOINT：设置应用启动入口

启动docker`service docker start`或`systemctl start docker.service`。

如果使用rpm下载安装，需要下载`containerd`和`docker-ce-cli`和`docker-ce`，其中前两项为`docker-ce`的前置条件，且`docker-ce`和`docker-ce-cli`版本一致，`containerd`尽可能新就行。

docker与虚拟机的不同主要在于：

- 所有dcoker应用共享一个宿主机操作系统，每个虚拟机有自己的操作系统
- 每个dcoker应用通过Docker层和宿主层的操作系统交互，而虚拟机直接与操作系统交互
- 对环境负责的只是宿主机的操作系统本身，而虚拟机的Hypervisor对应用进程的隔离环境负责，docker自己不会创建任何实体的容器

通过Docker启动的容器本质上和操作系统中运行的进程本质上没有区别。

### Docker架构

Docker的主要组成部分可以大致分为：

- Docker Client
- Docker Daemon
- Docker Registry

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Docker%E4%B8%8EK8s_Docker%E6%9E%B6%E6%9E%84.png)



## Docker核心技术

## Docker最佳实践

## Kubernetes

pod是一组容器的集合，类似进程组，是K8s中一个最小调度单位。属于同一个pod的不同容器一定会被调度至同一台机器上，共享NameSpace。

K8s采用声明式API,而不是命令式API。SQL就是典型的声明式API，其不关心具体实现如何，只在乎我们所需的状态或结果。而命令式API就是告诉他每一步怎么做。

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Docker%E4%B8%8EK8s_k8s%E6%9E%B6%E6%9E%84.png)



## 云原生监控方案

