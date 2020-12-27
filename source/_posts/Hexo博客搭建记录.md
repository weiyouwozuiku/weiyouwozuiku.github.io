---
title: Hexo博客搭建记录
author: 不二
mathjax: false
date: 2020-12-27 09:42:46
tags: Hexo
cover: true
img:
categories: 经验总结
---

本文记录博客搭建的过程，以便以后进行查阅和分享经验。

## Hexo搭建

### 安装组件

Manjaro：`sudo pacman -S nodejs`

Windows：下载安装一直下一步

#### Node

##### 安装包

` npm install packageName`这个命令会将包安装在当前目录下`node_modules`目录内，可执行命令（如果有）安装在`node_modules/.bin`目录下。

作为系统级的全局安装使用 -g 选项:`npm -g install packageName`，默认情形下这个命令会将包安装至 `/usr/lib/node_modules/npm`，需要管理员权限。

##### 更新包

更新包只需要执行` npm update packageName`。

对于全局环境安装的包 ( -g )`npm update -g packageName`。

更新所有包，去掉包名将试图更新所有包：`npm update`。

添加 -g 标记更新全局环境安装的包：`npm update -g`。

##### 删除包

删除使用 -g 标记安装的包只须：`npm -g uninstall packageName`。

若删除个人用户目录下的包去掉标记执行：`npm uninstall packageName`。

##### 列出所有包

若要显示已安装的包的树形视图执行：`npm -g list`。

仅显示顶层树：`npm list --depth=0`。

要显示需要更新的过期软件包：`npm outdated`。

##### npm-check

安装npm-check(全局安装)：`npm install -g npm-check`。

npm全局更新包 (全局目录)：`npm-check -u -g`。

npm更新某个项目的包 (项目目录)：`npm-check -u`。

##### package.json

```json
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "build": "hexo generate",
    "clean": "hexo clean",
    "deploy": "hexo deploy",
    "server": "hexo server"
  },
  "hexo": {
    "version": "5.3.0"
  },
  "dependencies": {
    "hexo": "^5.3.0",
    "hexo-deployer-git": "^2.1.0",
    "hexo-filter-github-emojis": "^3.0.4",
    "hexo-filter-mermaid-diagrams": "^1.0.5",
    "hexo-generator-archive": "^1.0.0",
    "hexo-generator-category": "^1.0.0",
    "hexo-generator-index": "^2.0.0",
    "hexo-generator-search": "^2.4.1",
    "hexo-generator-tag": "^1.0.0",
    "hexo-permalink-pinyin": "^1.1.0",
    "hexo-renderer-ejs": "^1.0.0",
    "hexo-renderer-marked": "^3.0.0",
    "hexo-renderer-stylus": "^2.0.0",
    "hexo-server": "^2.0.0",
    "hexo-wordcount": "^6.0.1"
  }
}

```

#### Git

##### 安装Git

Manjaro：`sudo pacman -S git`

Windows：下载安装一直下一步

##### 配置信息

```shell
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
$ ssh-keygen -t rsa -C "youremail@example.com"
```

将`id_rsa.pub`文件中的信息添加到Github->Account settings->SSH Keys页面。

如果想同时长传至coding,也是去coding添加上述信息。

测试是否成功使用`ssh -T git@github.com`。

### Hexo

#### 安装

`npm install -g hexo-cli `，安装Hexo主程序。

#### 初始化

`hexo init <博客文件夹名称>`，创建博客所在的文件夹并生成最基本的框架文件。

进入该目录，使用`npm install`安装所需的node包。

在本地启动hexo：`hexo server`或`hexo s`。

#### 常用命令

```shell
npm install hexo -g #安装Hexo
npm update hexo -g #升级
hexo init #初始化博客
hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo g == hexo generate #生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy #部署
hexo server #Hexo会监视文件变动并自动更新，无须重启服务器
hexo server -s #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP
hexo clean #清除缓存，若是网页正常情况下可以忽略这条命令
```

#### 部署至Github

1. 创建一个公开项目，名称是自己用户名+.github.io。

2. 选择项目的Settings,选择Github pages，点击Choose a theme选择一个主题。

3. 使用网址`用户名.github.io`查看是否能正常打开。

4. 安装node包`hexo-deployer-git`。

5. 在博客根目录下的`_config.yml`中添加字段：

   ```yaml
   deploy:
     - type: git
       repository: git@github.com:<用户名>/<用户名>.github.io.git
       branch: master
       
     - type: git
       repository: <另选的项目地址>
       branch: master
   ```

   **这里注意，必须是master分支，Github page只能是master分支。**

6. 使用`hexo d -g`将渲染好的网页上传至Github。此时就可以使用`<用户名>.github.io`进行访问了。

#### 博客基本信息

博客的基本信息存储在博客根目录下的`_config.yml`文件中。

## 主题选择与优化

我使用的主题是

## 访问加速



## 分支存储源码

