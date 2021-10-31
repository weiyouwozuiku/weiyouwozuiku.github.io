---
title: Home Assistant
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/经验总结/Home-Assistant.png
mathjax: true
date: 2021-07-07 00:34:25
tags: HomeAssistant
categories: 经验总结
---

## 介绍

Home Assistant是一款用于家庭自动化的免费开源软件，旨在成为智能家居设备的中央控制系统，重点关注本地控制和隐私。可以通过基于Web的用户界面，Android和iOS的配套应用程序或通过受支持的虚拟助手（如Google Assistant或Amazon Alexa）使用语音命令来访问它。

## 安装

我选择安装的平台是树莓派，而不是选择NAS作为容器的宿主，因为树莓派上可以使用`NPS`进行内网穿透。采用了Docker的安装方式，直接使用Python的方式pip安装的话会失败。因为Home Assistant需要Python3.8以上的版本。而RaspberryOS默认apt源中最新只有Python3.7。

1. 首先给树莓派安装上一个系统，我这里为了方便以后做硬件的扩展性，采用官方的raspberryOS。**记得安装完毕后，不要先更新系统，先扩展系统使用整个SD空间。**

2. 安装Docker,这里使用的是Docker的一键化安装脚本`sudo curl -sSL https://get.docker.com | sh`。

3. 安装容器

   ```shell
   docker run --init -d \
     --name homeassistant \
     --privileged \
     --restart=unless-stopped \
     -v /etc/localtime:/etc/localtime:ro \
     -v /PATH_TO_YOUR_CONFIG:/config \
     --network=host \
     ghcr.io/home-assistant/raspberrypi3-homeassistant:stable
   ```

   更新容器

   ```shell
   # if this returns "Image is up to date" then you can stop here
   docker pull ghcr.io/home-assistant/raspberrypi3-homeassistant
   ```

   ```shell
   # stop the running container
   docker stop homeassistant
   ```

   ```shell
   # finally, start a new one
   docker run --init -d \
     --name homeassistant \
     --restart=unless-stopped \
     --privileged \
     -v /PATH_TO_YOUR_CONFIG:/config \
     -v /etc/localtime:/etc/localtime:ro \
     --network=host \
     ghcr.io/home-assistant/raspberrypi3-homeassistant:stable
   ```

4. 此时本机上已经安装好了装有Home Assistant的容器，并且已经运行。访问该设备的`IP:8123`即可访问。

5. 为了能在离家后控制设备，需要做内网穿透。这里我采用闲置的云服务器，借助`NPS`实现TCP映射。

6. APP端的Home Assistant的设置中可以设置内网访问地址和外部访问地址。

## Tip

Home assistant中的小米设备需要安装HACS中的集合`Miot Auto`和`Miot`。

由于HACS中许多插件都是从github进行下载，又因为国内的网络环境。可以通过进入docker中修改`/etc/hosts`中的域名加ip进行访问。
