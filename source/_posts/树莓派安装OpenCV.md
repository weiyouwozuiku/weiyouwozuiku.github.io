---
title: 树莓派安装OpenCV
author: 不二
date: 2019-07-10 17:54:52
mathjax: true
tags: 
- 树莓派
- OpenCV
categories:
- 嵌入式
---

## 树莓派安装OpenCV

### 系统更新

```shell
sudo apt update
sudo apt upgrade
sudo rpi-update
```

### 安装一些OpenCV相关工具

```shell
sudo apt install git cmake build-essential pkg-config
```

<!-- more -->

### 安装四个常用的图像工具包

```shell
sudo apt install libjpeg8-dev libtiff5-dev libjasper-dev libpng12-dev
```

### 安装视频IO包

```shell
sudo apt install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev  
```

### 安装gtk2.0

```shell
sudo apt install libgtk2.0-dev
```

### 安装优化函数包

```shell
sudo apt install libatlas-base-dev gfortran
```

### 安装Python开发包

```shell
sudo apt install python-dev python3-dev
```

*这一步在新版的树莓派上不需要*

### 下载OpenCV源码

```shell
wget -O opencv-3.4.6.zip https://github.com/opencv/opencv/archive/3.4.6.zip
```

### 下载OpenCV_contrib包

```shell
wget -O opencv_contrib.3.4.6.tar.gz https://github.com/opencv/opencv_contrib/archive/3.4.6.tar.gz
```

### 编译

将上面下载的两个OpenCV文件压缩包进行解压，并在OpenCV的主文件夹中新建一个release文件夹。进入release文件夹中，执行一下命令。

```shell
sudo cmake -D CMAKE_BUILD_TYPE=RELEASE \
	-D CMAKE_INSTALL_PREFIX=/usr/local \
	-D OPENCV_EXTRA+MODULES_PATH=~/opencv_contrib-3.4.6/modules \
	-D INSTALL_PYTHON_EXAMPLES=ON \
	-D BUILD_EXAMPLES=ON ..
screen -S opencv
sudo make
```

### 安装OpenCV

```shell
sudo make install
// 更新动态链接库
sudo ldconfig
```



### 树莓派连接隐藏网络

修改/etc/wpa_supplicant/wpa_supplicant.conf，在相应的wifi设置中添加上scan_ssid=1即可。

