---
title: 有趣Docker
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/经验总结/有趣Docker.jpg
mathjax: true
date: 2024-03-20 11:05:13
tags:
- Docker
categories: 经验总结
---

## 内网导航面板

``` compose
docker run -d --restart=always -p 3002:3002 \
-v /root/sun-panel/conf:/app/conf \
-v /root/sun-panel/uploads:/app/uploads \
-v /root/sun-panel/database:/app/database \
--name sun-panel \
hslr/sun-panel
```

## 