---
title: 深入浅出Docker笔记
author: 不二
date: 2019-12-22 08:56:53
mathjax: true
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/docker.jpeg
tags:
- Docker
categories: 
- 云计算
---

## 第一章 容器发展之路

运营成本:OPEX

资金性支出:CAPEX

容器模型与虚拟机的区别：容器的运行不会独占操作系统。运行在相同宿主主机上的容器是共享一个操作系统，节省大量的系统资源，节省在许可证上的花销，以及为OS打补丁等运维成本。启动快，便于迁移。

$$对容器影响较大的技术\begin{cases} 内核命名空间(Kernel \quad Namespace) \\\ 控制组(Control \quad Group) \\\ 联合文件系统(Union \quad File \quad System) \\\ Docker  \end{cases}$$

运行中的容器共享主机的内核。

Kubernetes提供热插拔的容器运行时接口CRI。默认容器为Docker。

## 第二章 走进Docker

Docker是一种运行在Linux和Win上的软件，用于创建、管理和编排容器。

Docker引擎是用于运行和编排容器的基础设施工具，是运行容器的核心容器运行时。

Docker版本号遵循`YY.MM-xx`格式。

Docker公司的一个核心哲学通常称为"含电池，但可拆卸"(Batteries included but removable)。即许多Docker内置的组件都可以替换为第三方组件。

OCI(开放容器计划)是CoreOS和Docker共同成立的管理委员会，发布镜像规范和运行时规范。

## 第三章 Docker安装

#### Win版Docker

- 属于社区版，并不适用于生产环境。
- 通过启动一个独立的引擎来提供Docker环境。
- 某些版本特性可能延迟支持，稳定性第一，新特性其次。

#### Mac版Docker

- 属于社区版，并不是为了生产环境进行设计的。
- 运行在一个轻量级的Linux VM之上。

#### Linux版Docker

$$\begin{cases}\text{社区版(Community Edition)}\\\text{企业版(Enterprise Edition)}\end{cases}$$

Docker EE包含Docker CE中的全部功能，还包括了商业支持和其他Docker产品的集成。

使用shell脚本的安装步骤如下：

```shell
wget -qO- https://get.docker.com/ | sh
 # 将用户加入Docker Unix组，通过非root用户使用Docker
 sudo usermod -aG docker your-name
 # 查看是否添加到docker组成功，执行完毕需要重新登录，组权限才能生效
 cat /etc/group | grep docker
```

查看docker版本：

```shell
docker --version
```

查看docker信息：

```shell
docker system info
```

设置docker开机自启：

```shell
sudo systemctl enable docker
sudo systemctl is-enabled docker
```

检查确保docker的容器和服务都已经重启成功：

```shell
docker container ls
docker service ls
```

#### 升级Docker引擎

升级关键步骤：

1. 确保容器配置了正确的重启策略；在Swarm Mode模式下使用服务时，需要正确配置了draining node。
2. 停止Docker守护程序
3. 移除旧版本Docker
4. 安装新版本Docker
5. 配置新版本的Docker为开机自启动
6. 确保容器重启成功

#### Docker存储驱动的选择

每个Docker容器都有一个本地存储空间，用于保存层叠的镜像层(Image Layer)以及挂载的容器文件系统。默认情况下，容器的所有读写操作发生在其镜像层上或挂载的文件系统中。

存储驱动在上层抽象设计中都采用了栈式镜像层存储和写时复制(Copy-on-Write)的设计思想。

存储驱动对Docker的性能和稳定性至关重要。

在Linux上，Docker可选择的一些存储驱动包括AUFS(最原始也是最老的)、Overlay2(可能是未来最佳选择)、Device Mapper、Btrfs和ZFS。

在Win上，Docker只支持Windows Filter一种存储驱动。

存储驱动的选择是节点级别的。即，每个docker主机只能选择一种存储驱动。不能为每个容器选择不同的存储驱动。在Linux上，修改`/etc/docker/deamon.json`文件来修改存储引擎配置。*修改完之后需要重启Docker才能生效。*如：

```shell
{
	"storage-driver":"overlay2"
}
# 如果配置所在行不是最后一行，则需要在行尾添加逗号。
```

如果修改了正在运行的docker主机的存储引擎类型，则现有的镜像和容器在重启之后将不可用，因为每种存储驱动在主机上存储镜像层的位置不一样(通常在/var/lib/<storage-driver>/...目录下)。

修改了存储驱动的类型，docker就无法找到原有的镜像和容器。切换回原本的存储驱动能继续使用之前的镜像和容器。如果在切换存储引擎之后，还想使用之前的镜像和容器，则需要将镜像保存为Docker格式，上传到某个镜像仓库，修改本地Docker引擎并重启，之后从镜像仓库将镜像拉取到本地，最后重启容器。

检查Docker当前的存储驱动类型：`docker system info`。

版本推荐选择：

- Red Hat Enterprise Linux：4.x版本内核或更高版本+Docker 17.06版本或更高版本，建议使用Overlay2。
- Red Hat Enterprise Linux:低版本内核或低版本的Docker，建议使用Device Mapper。
- Ubuntu Linux:4.x版本内核或更高版本，建议使用Overlay2。
- Ubuntu Linux:更早的版本建议使用AUFS。
- SUSE Linux Enterprise Server:Btrfs。

#### Device Mapper配置

大部分Linux存储驱动不需要或需要很少的配置。

Device Mapper通常需要合理配置之后才能表现出良好的性能。

默认情况下，Device Mapper采用loopback mounted sparse file作为底层实现来为Docker提供存储支持。开箱即用，并不适用于生产环境，性能很差。

通过将底层修改为direct-lvm模式，可以获取更好的性能。这种规模下通过使用基于裸块设备(Raw Block Device)的LVM精简池(LVM thin pool)。暂时只能配置一个块设备，并且只有在第一次安装后才能设置生效。但是目前来说有一定的风险。

#### Docker自动设置direct-lvm

如下步骤将Docker配置存储驱动为Device Mapper，并使用direct-lvm模式。

1. 将下面的存储驱动配置添加到`/etc/docker/daemon.json`当中。

   ```shell
   {"storage-driver":"devicemapper"
   "storage-opts":[
   	"dm.directlvm_device=/dev/xdf", # 设置块设备的位置
   	"dm.thinp_percent=95", # 设置镜像和容器允许适应的最大存储空间占比
   	"dm.thinp_metapercent=1",# 设置元数据存储允许使用的存储空间大小
   	"dm.thinp_autoextend_threshold=80",# 设置LVM自动扩展精简池的阈值
   	"dm.thinp_autoextend_percent=20",# 当触发精简池自动扩容机制的时候，扩容大小应当占现有空间的比例
   	"dm.directlvm_device_force=false"# 是否将块设备格式化为新的文件系统
   ]
   }
   ```

2. 重启Docker。

3. 确认Docker已成功运行，并块设备配置被成功加载。

即使Docker在direct-lvm模式下只能设置单一块设备，其性能显著优于loopback模式。

#### Docker手动设置direct-lvm

前置知识：

- 块设备(Block Device)

  在使用direct-lvm模式的时候，需要有可用的块设备。这些块设备应该位于高性能的存储设备之上，如：SSD或外部LUN存储。

- LVM配置

  Docker的Docker Mapper存储驱动底层利用LVM(Logical Volume Manager)来实现，需要配置LVM所需的物理设备、卷组、逻辑卷和精简池。应当使用专用的物理卷并将其配置在相同的卷组中。这个卷组不应当被Docker之外的工作负载所使用。此外还需要配置额外两个逻辑卷，分别存储存储数据和源数据信息。另外要创建LVM配置文件、指定LVM自动扩容的触发阈值，以及自动扩容的大小，并且为自动扩容配置相应的监控，保证自动扩容会被触发。

- Docker配置

  修改Docker配置文件之前要先保存原始文件(etc/docker/daemon.json)，然后进行修改。

## 第四章 纵观Docker

 #### 运维视角

Docker安装主要涉及两个组件：Docker客户端、Docker daemon。

deamon实现了Docker引擎的API。

Linux默认安装时，客户端和daemon之间的通信是通过本地IPC/UNIX Socket完成的(/var/run/docker.sock)。Windows通过名为npipe:////./pipe/docker_engine的管道完成的。

Docker镜像可以理解为一个包含了OS文件系统和应用的对象。

虚拟机模板本质上是处于关机状态的虚拟机。

`docker image ls`：查看镜像。

拉取(pulling)：在Docker主机上获取镜像的操作。

Docker的每个镜像都有自己唯一的ID。用户可以通过引用镜像的ID或名称来使用镜像。如果用户选择使用镜像ID，通常只需要输入ID开头的几个字符即可。

`docker container run -it + 镜像名 运行的进程`：从镜像中来启动容器。例如：`docker container run -it ubuntu:latest /bin/bash`或`docker container run -it microsoft/powershell:nanoserver pwsh.exe`。`it`参数：开启容器的交互模式并将Shell链接到容器终端。

按`Ctrl-PQ`组合键，**可以在退出容器的同时还保持容器运行**。

`docker container ls`：查看系统内部处于运行状态的容器。-a参数可以列出所有容器，包括处于停止状态的。

`docker container stop + 容器对应名称或ID`：停止容器运行

`docker container rm + 容器对应名称或ID`：杀死容器

`docker container exec`：将shell链接到一个运行中的容器终端。例如：`docker container exec -it 容器名称或者id bash`。

`docker container start <镜像ID>`：启动镜像

#### 开发视角

每个仓库都包含一个名为Dockerfile文件，这是一个纯文本文件，描述了如何将应用构建到Docker镜像中。 每一行代表一个用于构建镜像的指令。

`docker image build -t 镜像名 .` ：使用当前目录中的Dockerfile文件和应用代码创建新的镜像。构建时间长短是由构建过程中要拉取的镜像大小和复杂度决定的。

## 第五章 Docker引擎

Docker引擎是用来运行和管理容器的核心软件，通常将其代指为Docker或Docker平台。

Docker引擎是模块化的。

$Docker引擎组件\begin{cases}Docker\quad Client\\Docker\quad daemon\\containerd\\runc\end{cases}$

![深入浅出Docker笔记_Docker引擎.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0_Docker%E5%BC%95%E6%93%8E.png)

![深入浅出Docker笔记_Docker引擎1.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0_Docker%E5%BC%95%E6%93%8E1.png)

runc是OCi容器运行时规范的参考实现。实质上是一个轻量级的、针对Libcontainer进行了包装的命令行交互工具(Libcontainer取代了早起的LXC)。

runc只有一个功能就是**创建容器**。本质是一个独立的容器运行时工具。

containerd的主要任务是容器的生命周期管理，也可以进行镜像管理等。containerd组件确保了Docker镜像能够以之后以正确的OCI Bundle的格式传递给runc。

containerd在linux和windows中以daemon的方式运行。Kubernetes可以通过cri-containerd使用containerd。

Daemon使用一种CRUD风格的API，通过gRPC与containerd进行通信。

Daemon的主要功能包括镜像管理、镜像构建、REST API、身份验证、安全、核心网络、编排。

一旦Daemon接收到创建新容器的命令，就会向containerd发出调用。containerd指挥runc去做。containerd将Docker镜像转换成OCI bundle，并让runc基于此创建一个新的容器。runc与操作系统内核接口进行通信，基于所有必要的工具(Namespace、CGroup等)来创建容器。容器进程作为runc的子进程启动。启动完毕后，runc退出。

![深入浅出Docker笔记_Docker启动.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0_Docker%E5%90%AF%E5%8A%A8.png)

将所有的用于启动、管理容器的逻辑和代码从daemon中移除，以为者容器运行时与Docker daemon是解耦的，称之为“无守护进程的容器”。因此，Docker daemon的维护和升级不会影响运行中的容器。

每次创建容器`shim`fork一个新的runc实例。一旦容器创建完毕，对应的runc进程退出。

一旦容器进程的父进程runc进程退出，相关联的containerd-shim进程就会成为容器的父进程。

shim的部分职责：1.保持所有STDIN和STDout流是开启的，从而当daemon重启时，容器不会因为管道的关闭而终止。2.将容器的退出状态反馈给daemon。

## 第六章 Docker镜像

镜像由多层构成。内部是一个精简的操作系统，包括应用运行所必须的文件和依赖包。

`docker container run`和`docker service create`命令从某个镜像启动一个或多个容器。

一旦容器从镜像启动后，二者之间变成相互依赖关系，并且在镜像上启动的容器全部停止之前，镜像是无法被删除的。

通常Docker镜像中只有一个精简的shell，甚至没有shell。镜像不包含内核，容器共享所在Docker主机的内核。

容器只包含必要的操作系统(通常只有操作系统文件和文件系统对象)。

Hyper-V容器运行在专业用的轻量级VM上，同时利用VM内部的操作系统内核。

Linux Docker主机本地镜像仓库通常位于`/var/lib/docker/\<stroage-drivewr>`，Windows Docker主机是`C:\ProgramData\docker\windowsfilter`。

`docker image pull <repository>:<tag>`：拉取镜像。如果用户没有指定具体的镜像标签，默认拉取标签为latest的镜像。Docker镜像存储在镜像仓库服务中。客户端中的镜像仓库服务默认是使用Docker Hub。

**标签为latest的镜像并不一定是最新的，但是使用的时候依然要谨慎。**

`docker image pull <所有者>/<repository>:<tag>`：拉取非官方的Docker hub仓库的镜像。

`docker pull <第三方镜像仓库服务的DNS名称>/<所有者>/<repository>:<tag>`：从第三方仓库拉取镜像

以上拉取镜像的命令可以加-a参数，下载仓库中的全部镜像。如果镜像仓库中同时包含用于多个平台或者架构的镜像，那么命令可能会失败。

镜像仓库服务包含多个镜像仓库。一个镜像仓库包含多个镜像。

标签是存放在镜像元数据中的任意数字或字符串。一个镜像可以根据用户需要设置多个标签。

悬虚镜像：没有标签的镜像，在列表时显示为\<none>:\<none>。通常出现这种情况，是因为构建了一个新的镜像，然后为这个镜像打了一个已经存在的标签。Docker创建新的镜像，并赋予这个镜像标签。之前拥有这个标签的镜像会被移除这个标签。

`docker image ls --filter dangling=true`：显示悬虚镜像。可以使用--format参数通过Go模板。--digest参数在本地查看镜像摘要。

`docker image prune`：移除全部悬虚镜像。-a参数删除所有没有被任何容器使用的镜像。

Docker目前支持如下的过滤器：

- dangling：可以指定true或者false，仅返回悬虚镜像(true)，或非悬虚镜像(false)。
- before：需要镜像名称或者ID作为参数，返回在之前被创建的全部镜像。
- since：与before类似，不过返回的是指定镜像之后创建的全部镜像。
- label：根据标注(label)的名称或者值，对镜像进行过滤。
- 其他过滤方式可以使用reference。例如：--filter=reference="\*:latest*"。

使用`docker image ls --format "{{.Size}}"`或`docker image ls --format "{{.Repository}: {{.Tag}: {{.Size}}"`这个的format参数是Go模板对输出内容进行格式化。

`docker search <特定字符串>`：搜索所有"NAME"字段中包含特定字符串的仓库。“NAME”字段是仓库名称，包含了Docker ID，或者非官方仓库的组织名称。--filter "is-official=true"参数使命令只显示官方镜像。--filter "is-automated=true"参数使命令只显示自动创建的仓库。--limit参数设置返回内容行数，默认25行，最多100行。

`docker image inspect <镜像名>`：查看镜像层数据和元数据。

`docker histoy`：显示镜像的构建历史记录，但其不是严格意义上的镜像分层。有些Dockerfile中的指令并不会创建新的镜像层。但是会在镜像中添加元数据。

所有的Docker镜像都起始于一个基础镜像层，当进行修改或增加新的内容时，就会在当前镜像之上，创建新的镜像层。

在添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合。

Docker采用快照机制实现镜像层堆栈，并保证多镜像层对外显示为统一的文件系统。

**多个镜像之间可以共享镜像层。**

**每个镜像都有一个基于其内容的密码散列值，也被称为摘要(digest)。**

`docker image pull <镜像名>@<sha256码>`：通过镜像摘要拉取镜像。

镜像的唯一标识是一个加密ID，即配置对象本身的散列值。每个镜像层也由一个加密ID区分，其值为镜像层本身内容的散列值。也就是内容散列。

**每个镜像层在拉取或推送时，包含一个分发散列值，这是一个压缩版镜像的散列值。该散列值用于校验拉取的镜像是否被篡改过。**

为了实现多架构的方便使用，Docker的镜像仓库服务API支持两种重要的结构：Manifest列表(新)和Manifest。Manifest列表是指某个镜像标签支持的架构列表。其支持的每种架构，都有自己的Manifest定义。

![深入浅出Docker笔记_Manifest列表.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0_Manifest%E5%88%97%E8%A1%A8.png)

Manifest列表是可选的。在没有Manifest列表的情况下，镜像仓库服务会返回普通的Manifest。

`docker image rm <镜像名或镜像ID>`：删除镜像。

某个镜像层被多个镜像共享，那只有当全部依赖该镜像层的镜像都被删除，该镜像层才会被删除。

`docker image ls -q `：返回当前docker主机中所有容器的ID。

`docker image rm $ (docker image ls -q) -f`

## 第七章 Docker容器















## 补充

镜像是一堆只读层的统一视角。

![深入浅出Docker笔记_image-container.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0_image-container.png)

统一文件系统：将多个只读层重叠在一起，并整合成一个文件系统，对用户隐藏。

在一个运行的容器内部，相应的文件层是不可见的。

**容器和镜像几乎一样，也是一堆层的统一视角。唯一区别在于容器最上面的一层是可读可写的。即，容器=镜像+可读写层**

运行态容器：可读写的统一文件系统加上隔离的进程空间和包含其中的进程。

![深入浅出Docker笔记_运行态容器.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0_%E8%BF%90%E8%A1%8C%E6%80%81%E5%AE%B9%E5%99%A8.png)

一个文件层包含多信息：1.元数据(metadata)：关于这个层的额外信息，它不仅能够让Docker获取运行和构建时的信息，还包括父层的层次信息。**只读层和读写层都包含元数据。**2.指向父层的指针。如果没有这个指针，说明它处于最底层。3.id。

![深入浅出Docker笔记_dockerrun.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0/%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BADocker%E7%AC%94%E8%AE%B0_dockerrun.png)

`docker ps `：；列出所有运行中的容器，隐藏非运行态的容器。-a参数可以列出所有容器。

Docker主要解决了应用隔离、应用部署、资源限制。

其中隔离技术只要依靠的是Namespace，资源限制使用的是Cgroup。

