---
title: Clash使用教程
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/经验总结/Clash使用教程.png
mathjax: true
date: 2022-04-24 01:06:00
tags: Clash
categories: 经验总结
---

## 安装

### 下载

下载[clash](https://github.com/Dreamacro/clash/releases/tag/premium)的二进制文件到`~/clash`这个也可以放在/usr/local/bin中，赋予运行权限。

在`~/.config/clash`中执行`git clone https://github.com/Dreamacro/clash-dashboard.git && cd clash-dashboard && git checkout -b gh-pages origin/gh-pages`，这个是clash的页面。

### 开机自启

```shell
sudo vim /etc/systemd/system/clash.service

[Unit]
Description=clash daemon

[Service]
Type=simple
User=root
ExecStart=clash -d /home/pi/.config/clash
Restart=on-failure

[Install]
WantedBy=multi-user.target

sudo systemctl enable clash.service  #设置开机自启动
sudo systemctl start clash.service  #手动启动
```

## 自动更新

### 更新脚本及其配置

```shell
#!/bin/bash

# 设置clash路径
clash_path="/home/pi/.config/clash"

# 停止clash
sudo systemctl stop clash.service

wget -O $clash_path/config.yaml <订阅地址>
wget -O $clash_path/Country.mmdb https://www.sub-speeder.com/client-download/Country.mmdb
sed -i "s/# external-ui: folder/external-ui: \/home\/pi\/.config\/clash\/clash-dashboard/;s/secret: ''/secret: '登录秘钥'/g" $clash_path/config.yaml

# 重启clash
sudo systemctl restart clash.service
```

### crontab执行

`crontab -e`

```shell
0 */8 * * * /home/pi/.config/clash/update.sh
```

