---
title: BurpSuite使用笔记
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/经验总结/BurpSuite使用笔记.jpg
mathjax: true
date: 2022-05-18 22:32:54
tags: BurpSuite
categories: 经验总结
---

## 安装

1. 从[官网](https://portswigger.net/burp/releases/professional-community-2022-3-7?requestededition=professional&requestedplatform=)下载BurpSuite。

2. 下载[TrojanAZhen/BurpSuitePro-2.1](https://github.com/TrojanAZhen/BurpSuitePro-2.1)

3. 将上面仓库中的burp-loader-x-Ai.jar和burp-keygen-scz.jar放置在`/Applications/Burp\ Suite\ Professional.app/Contents/Resources/app/`中

4. 编辑`vmoptions.txt`，追加写

   ```json
   -Dfile.encoding=utf-8
   -noverify
   -javaagent:burp-loader-x-Ai.jar
    -Xmx2048m
   ```

5. 带着启动器启动BurpSuite

   ```shell
   java --add-opens=java.desktop/javax.swing=ALL-UNNAMED --add-opens=java.base/java.lang=ALL-UNNAMED -Dfile.encoding=utf-8 -noverify -javaagent:/Applications/Burp\ Suite\ Professional.app/Contents/Resources/app/burp-loader-x-Ai.jar -Xmx2048m -jar /Applications/Burp\ Suite\ Professional.app/Contents/Resources/app/burpsuite_pro.jar
   ```

6. 选择手动注册

7. 通过注册器获取注册码

   ```shell
   java -jar /Applications/Burp\ Suite\ Professional.app/Contents/Resources/app/burp-keygen-scz.jar
   ```

## 参考文献

1. [macOS 下如何优雅的使用 Burp Suite](https://www.sqlsec.com/2019/11/macbp.html)，2019
