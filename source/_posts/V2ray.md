---
title: V2ray
date: 2020-02-15 21:10:31
mathjax: true
tags:
- Linux
- V2ray
- ios
- Windows
---

## V2ray

多入口多出口: 一个 V2Ray 进程可并发支持多个入站和出站协议，每个协议可独立工作。
可定制化路由: 入站流量可按配置由不同的出口发出。轻松实现按区域或按域名分流，以达到最优的网络性能。
多协议支持: V2Ray 可同时开启多个协议支持，包括 Socks、HTTP、Shadowsocks、VMess 等。每个协议可单独设置传输载体，比如 TCP、mKCP、WebSocket 等。
隐蔽性: V2Ray 的节点可以伪装成正常的网站（HTTPS），将其流量与正常的网页流量混淆，以避开第三方干扰。
反向代理: 通用的反向代理支持，可实现内网穿透功能。
多平台支持: 原生支持所有常见平台，如 Windows、Mac OS、Linux，并已有第三方支持移动平台。

### Linux终端环境

1. 获取安装脚本

    ```shell
    curl -L-s https://install.direct/go.sh
    ```

2. 由于该脚本默认执行时，需要访问国外的服务器下载`v2ray-linux-64.zip`文件，可以提前下载下来，地址`https://github.com/v2ray/v2ray-core/releases/download/${NEW_VER}/v2ray-linux-${VDIS}.zip`。

3. 下载完之后，将`go.sh`和`v2ray-linux-64.zip`放在同一个文件夹中。

4. 使用命令`sudo bash go.sh --local ./v2ray-linux-64.zip`安装。

5. 配置文件在`/etc/v2ray/config.json`中。可以先用其他平台生成的`config.json`文件复制覆盖这个文件。

6. 使用命令`sudo service v2ray restart`，重启服务。

7. 使用命令`export http_proxy="socks5://127.0.0.1:1088"`，将终端中的流量都走代理。

8. 使用命令`curl www.cip.cc `查看当前ip所在位置。

9. 可以将以上命令写入`.zshrc`文件中

    ```shell
    alias setproxy='export http_proxy="socks5://127.0.0.1:1088"'
    alias ipaddr='curl www.cip.cc'
    ```

### Linux桌面环境

```shell
sudo pacman -S qv2ray v2ray v2ray-geoip v2ray-domain-list-community
```

使用qv2ray即可。

### Windows

去`https://github.com/2dust/v2rayN/releases`上下载最新的v2rayN程序。

### ios

去美区APP Store下载Shadowrocket软件。

### 树莓派

树莓派3B的架构是armv7，无法正常使用x86架构下打包的appiamge软件。解决方案是使用
https://github.com/mzz2017/V2RayA。 这个项目的GUI是由网页提供的。语言是GO。直接在它的arm二进制包。运行。可以写入`/etc/rc.local`中作为自启服务。作为网关还行。