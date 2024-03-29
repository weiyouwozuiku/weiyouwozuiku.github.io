---
title: Docker与K8s
author: 不二
mathjax: true
date: 2021-04-02 10:44:11
tags: 
- Docker
- Kubernetes
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/云计算/docker_k8s.png
categories: 云计算
---
## Docker基础

docker相较于别的paas技术核心优势在于镜像，其是一套操作系统+应用程序，保证了环境的Ç一致性，无需根据不同的开发语言选择不同打包方式。

在Docker中打包镜像使用Dockerfile技术，即编写dockerfile即可。这其中有几个重要的参数：

- From：选择操作系统镜像
- COPY：表示将本地的可执行文件拷贝进镜像中
- EXPOSE：设置端口
- USER：设置用户
- ENTRYPOINT：设置应用启动入口

启动docker `service docker start`或 `systemctl start docker.service`。

如果使用rpm下载安装，需要下载 `containerd`和 `docker-ce-cli`和 `docker-ce`，其中前两项为 `docker-ce`的前置条件，且 `docker-ce`和 `docker-ce-cli`版本一致，`containerd`尽可能新就行。

docker与虚拟机的不同主要在于：

- 所有dcoker应用共享一个宿主机操作系统，每个虚拟机有自己的操作系统
- 每个dcoker应用通过Docker层和宿主层的操作系统交互，而虚拟机直接与操作系统交互
- 对环境负责的只是宿主机的操作系统本身，而虚拟机的Hypervisor对应用进程的隔离环境负责，docker自己不会创建任何实体的容器

通过Docker启动的容器本质上和操作系统中运行的进程本质上没有区别。

### Docker架构

Docker的主要组成部分可以大致分为：

- Docker Client：与Docker daemon 进行交互，与用户交互的客户端
- Docker Daemon：docker后台运行的守护进程

  - 启用：`service docker start`或 `systemctl start docker.service`
  - 通过 `/var/run/docker.sock`与client之间连接
- Docker Registry：用来存储Docker镜像

  - 拉取镜像：docker pull `<image-name>`
  - 最底层的镜像为scratch镜像
  - 镜像的构建通过DSL编写，叫dockerfile。
  - 容器是镜像的运行实例

  容器默认与其他容器与其宿主机相隔离开。具体的隔离策略可以进行自定义设置。

![Docker与K8s_Docker架构.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/云计算/Docker与K8s/Docker与K8s_Docker架构.png)

docker本身是在多层的只读层上添加了一个read-write层，即container layer。

### Docker镜像

镜像中包含的操作系统文件最好之后一个精简版的，而非全量。在启动容器之后，容器进程还是去和宿主机的操作系统进行交互。

对于多个应用使用同一基础镜像，推荐使用官方的镜像或将基础环境配置为镜像，复用基础镜像。

#### 镜像介绍

##### Alpine镜像

apline linux借助fs的文件格式将整个镜像包的大小压缩到很小，但是存在诸多坑，例如alphine使用的是musl libc,而一般程序使用的都是glibc,会导致找不到运行的动态链接库等问题。且因为使用musl libc使用的人较少，产生的问题也相对难以解决。

##### Busybox

简单来说，busybox是一个集成了一百多个最常用的Linux命令和工具的软件工具箱。

##### OpenJDK

Docker官方提供的openjdk镜像。

#### 镜像构建

镜像构建是基于Dockerfile来构建的，具体来说就是只需要按照容器便准写好Dockerfile文件，然后通过创建命令就可以构建出所需镜像了。

1. 先将源代码文件编译成可执行文件
2. 编写Dockerfile
3. 镜像build：`docker build -t <镜像名称>:<版本名称>`
4. 启动镜像：`docker run -p 8080:8080 <镜像名称>:<版本名称>`

##### Dockerfile

| 关键字     | 含义                       |
| ---------- | -------------------------- |
| FROM       | 表示构建镜像所基于的镜像   |
| COPY       | 拷贝文件                   |
| EXPOSE     | 暴露端口                   |
| ENTRYPOINT | 用来指定镜像的默认启动脚本 |

#### 容器操作命令

##### docker run

例如：`docker run -ti centos:latest /bin/bash`

| 参数          | 含义                                  |
| ------------- | ------------------------------------- |
| --interactive | 等同于-i，接受stdin的输入             |
| --tty         | 等同于-t，分配一个tty,一般和i一起使用 |
| --name        | 给容器设置名字                        |
| --add-host    | 给容器设置hosts文件，格式 host:ip     |
| --env         | 环境变量设置                          |
| --expose      | 暴露端口                              |
| --hostname    | 设置容器的主机名                      |
| --link        | 容器网络相关，和其他的container连接   |
| --cpu-quota   | 设置CPU限制                           |
| --memory      | 设置容器可以使用的内存限制            |
| --detach(-d)  | 后台运行docker                     ｜ |

##### docker attach

通过给一个正在运行的容器分配一个stdin、stdout、stderr，从而使得我们可以进入一个运行着的容器内部。

**注意：使用 `docker attach`进入之后，如果使用exit会导致原容器也退出。推荐使用 `Ctrl+C`退出。**

##### docker exec

该命令使得一个运行着的容器里面执行一个命令。其原理是在Linux内核层面，相当于fork了一个进程，然后这个进程设置和容器相同的NameSpace。如果在OPTIONS中指定 `-ti`，则会进入到容器内。因为这是fork出来的进程，可以exit。

##### docker ps

用来列出所有在运行容器的信息。使用 `-a`参数展示所有容器包括停止运行的。

##### docker kill

杀死一个或一组container。

##### docker log

用来获取docker的log信息。

##### docker top

查看这个容器在宿主机上的进程，毕竟本质上container只是一个进程。

docker container stop id/name

一次性关闭所有容器：`docker container stop $(docker container ps -aq)`

#### 镜像操作命令

##### docker images

显示本地所有非隐藏镜像，默认会将中间依赖镜像隐藏

##### docker build

##### docker commit

##### docker history

##### docker save

##### docker import

##### docker load

##### docker rmi

##### docker tag

docker container run 执行步骤

当输入 `docker cintainer run -d --pulish 80:80 --name webhost nginx`

1. 在本地查找是否有nginx的镜像。如果没有去远程拉取nginx镜像（默认从Docker Hub）
2. 基于nginx镜像创建一个新的容器，并准备执行
3. docker engine分配这个容器一个虚拟ip地址
4. 在宿主机上打来80端口并将容器的80端口转发到宿主机上
5. 启动容器，运行指定的命令

## Docker核心技术

## Docker最佳实践

## Kubernetes

pod是一组容器的集合，类似进程组，是K8s中一个最小调度单位。属于同一个pod的不同容器一定会被调度至同一台机器上，共享NameSpace。

K8s采用声明式API,而不是命令式API。SQL就是典型的声明式API，其不关心具体实现如何，只在乎我们所需的状态或结果。而命令式API就是告诉他每一步怎么做。

![Docker与K8s_k8s架构.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/云计算/Docker与K8s/Docker与K8s_k8s架构.png)

## 云原生监控方案
