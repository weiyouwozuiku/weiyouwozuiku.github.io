---
title: Hexo博客搭建记录
author: 不二
mathjax: false
date: 2020-12-27 09:42:46
tags: Hexo
cover: true
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/经验总结/hexo.jpg
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

扫描需要更新的包：`npm-check`

npm全局更新包 (全局目录)：`npm-check -u -g`。

npm更新某个项目的包 (项目目录)：`npm-check -u`。

##### npm-upgrade

升级系统中的相关插件。

安装`npm-upgrade`:`npm install -g npm-upgrade`

更新`npm-upgrade`

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
    "version": "6.1.0"
  },
  "dependencies": {
    "hexo": "^6.1.0",
    "hexo-deployer-git": "^3.0.0",
    "hexo-filter-github-emojis": "^3.0.4",
    "hexo-filter-mermaid-diagrams": "^1.0.5",
    "hexo-generator-archive": "^1.0.0",
    "hexo-generator-baidu-sitemap": "^0.1.9",
    "hexo-generator-category": "^1.0.0",
    "hexo-generator-feed": "^3.0.0",
    "hexo-generator-index": "^2.0.0",
    "hexo-generator-search": "^2.4.3",
    "hexo-generator-sitemap": "^3.0.1",
    "hexo-generator-tag": "^1.0.0",
    "hexo-permalink-pinyin": "^1.1.0",
    "hexo-renderer-ejs": "^2.0.0",
    "hexo-renderer-marked": "^5.0.0",
    "hexo-renderer-stylus": "^2.0.1",
    "hexo-server": "^3.0.0",
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

#### 模板设置

#### 文章排序自定义

hexo中默认是按照文章的创建时间显示，但是博客不是日记，好多步骤或者笔记需要进行更新记录，所以单纯的按照创建时间会导致自己较早创建但一直更新的笔记被放在最后。

hexo的`db.json`文件中包含了每篇博客的更新时间，字段是`db.json`，只需修改博客顶层文件夹中的`_config.yml`中的如下字段：

```yaml
index_generator:
	path: ''
	per_page: 18
	#默认是-date 符号表示顺序还是逆序
	order_by: -updated
```

### 更换解析器

原生使用的是`hexo-renderer-marked`，可以替换成`hexo-renderer-marked-it`这个解析器。

#### 安装

卸载原生解析器

```shell
$ npm un hexo-renderer-marked --save
```

安装解析器

```shell
$ npm i hexo-renderer-markdown-it --save
```

#### 配置

在整个Hexo项目下的`_config.yaml`文件下添加配置[^1]：

```yaml
markdown:
  preset: "default" # 渲染器默认预设 # "commonmark": 使用严格 CommandMark 规定. # "default": 默认配置, 类似于 GFM # "zero": 禁用所有预设.
# 以下为 render 选项
  render:
    html: true # 是否解析 HTML 内容, 如果为 true 则表示解析, false 表示将 HTML 转意为文本.
    xhtmlOut: false # 将 HTML 内容渲染为 XHTML 的形式 (XHTML 语法非常严格, 比如原 HTML 中的 <br> 标签必须要使用 <br/> 这样的形式进行 "自闭和") 可能会出现兼容性问题.
    langPrefix: 'language-' # 只适用 Hexo 默认的两个高亮插件禁用时 # 为围栏代码块中语法定义选项渲染后自动添加 class 名前缀, 如 ---html 渲染为 <class="language-html">
    breaks: false # true 则将所有换行渲染为 <br> 标签 # 这种行为不属于 CommandMark 和 GFM.
    linkify: false # true 则自动解析链接并添加为 <a> 标签, false 则将链接渲染为文本.
    typographer: true # 默认 true # 自动转义各种排版用字符, 如 ©
    quotes: '“”‘’' # 当 typographer 定义为 true 时的自动转换引号的行为, quotes: '“”‘’' 则表示将 "123" '123'转换为 “123” ‘123’
# render 选项到此结束
  enable_rules:
  disable_rules:
# 当 preset 定义时的渲染规则 # "zero" 表示禁用所有规则而能单独启用规则 # "default" 表示启用所有规则, 所以只能禁用特定规则. # "commandmark" 将严格遵循 CommandMark 因此不能额外定义规则的启闭.
# 使用单条规则
# disable_rules: 'link'
# 使用多条规则
# disable_rules:
#    - 'link'
#    - 'image'
  plugins: # 插件选项
    - markdown-it-abbr # https://github.com/markdown-it/markdown-it-abbr
    - markdown-it-cjk-breaks # https://github.com/markdown-it/markdown-it-cjk-breaks
    - markdown-it-container # https://github.com/markdown-it/markdown-it-container
#    - markdown-it-deflist # https://github.com/markdown-it/markdown-it-deflist
    - markdown-it-emoji # https://github.com/markdown-it/markdown-it-emoji
    - markdown-it-footnote # https://github.com/markdown-it/markdown-it-footnote
    - markdown-it-ins # https://github.com/markdown-it/markdown-it-ins
    - markdown-it-mark # https://github.com/markdown-it/markdown-it-mark
    - markdown-it-sub # https://github.com/markdown-it/markdown-it-sub
    - markdown-it-sup # https://github.com/markdown-it/markdown-it-sup
#以下为额外安装插件
#    - markdown-it-task-lists # https://github.com/revin/markdown-it-task-lists
  anchors: # 锚点的配置
    level: 1 # 开始创建锚点的等级 # 如 "2" 则表示从 H2 开始创建一直到 H6(最后).
    collisionSuffix: '' # 如果遇到重复的锚点 ID 为其添加数字编号时在这个数字后添加的后缀.
    # 以下为标题锚点配置
    permalink: true # false # 如果为 true 则创建一个除标题外带有固定地址的的锚点标签.
    permalinkClass: 'anchor' # 定义固定锚点的 class 名.
    permalinkSide: 'right' # 设定为 right 则会在标题后添加固定链接.
    permalinkSymbol: '·' # 定义固定链接使用的符号
    # 标题固定锚点到此结束
    case: 0 # 转换锚点 ID 中的字母为大写或小写 # "0" 不转换, "1" 为小写, "2" 为大写.
    separator: '-' # 用于替换空格的符号. # 默认为 "-"
```



## 主题选择与优化

我使用的主题是

### 首页添加Github状态

### 可选插件

### 主题的个性化

## 自动化部署

借助`Github Action`可以实现当我们在本地向Github提交代码的时候，自动创建并生成相应的静态网页代码，进而联动后面的`vercel`的部署任务。

目录结构如下：

```shell
.github
└── workflows
    └── deploy.yml
```

### 创建Action所需的环境变量

创建Action提交代码的秘钥。

```shell
ssh-keygen -f hexo-deploy-key -C "<博客网址>" #仅作为区分
```

上面的命令会在当前文件夹下生成名为`hexo-deploy-key`的私钥和`hexo-deploy-key.pub`的公钥。

将公钥设置进对应仓库的`setting->Deploy keys`，同时赋予写权限。

将私钥设置进对应仓库的`setting->Secrets->Actions`，对应的key名设置为`DEPLOY_KEY`，value就是私钥。

### 编写Action脚本

```yaml
name: Hexo Generate And Deploy

# 只监听 source 分支的改动
on:
  push:
    branches:
      - src

# 自定义环境变量
env:
  GIT_USER: weiyouwozuiku
  GIT_EMAIL: 19681022+weiyouwozuiku@users.noreply.github.com 
  DEPLOY_REPO: weiyouwozuiku/weiyouwozuiku.github.io
  DEPLOY_BRANCH: master
  NODE_VERSION: 17

jobs:
  Hexo-Generate-And-Deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          fetch-depth: 0

      - name: Checkout deploy branch
        uses: actions/checkout@v2
        with:
          repository: ${{ env.DEPLOY_REPO }}
          ref: ${{ env.DEPLOY_BRANCH }}
          path: .deploy_git
          fetch-depth: 0

      - name: Restore file modification time
        run: |
          find source/_posts -name '*.md' | while read file; do touch -d "$(git log -1 --format="@%ct" "$file")" "$file"; done

      - name: Set up Node.js
        uses: actions/setup-node@v1
        with:
          node-version: ${{ env.NODE_VERSION }}

      - name: Cache node modules
        uses: actions/cache@v2
        env:
          cache-name: cache-node-modules
        with:
          # npm cache files are stored in `~/.npm` on Linux/macOS
          path: ~/node_modules
          key: ${{ runner.os }}-build-${{ env.cache-name }}-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-build-${{ env.cache-name }}-
            ${{ runner.os }}-build-
            ${{ runner.os }}-

      - name: Set up environment
        env:
          DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
        run: |
          sudo timedatectl set-timezone "Asia/Shanghai"
          mkdir -p ~/.ssh
          echo "$DEPLOY_KEY" > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          git config --global user.name $GIT_USER
          git config --global user.email $GIT_EMAIL
      
      - name: Install dependencies
        run: |
          npm install -g hexo-cli
          npm install

      - name: Deploy
        run: |
          hexo deploy --generate

```

### Github Action过程中的一些坑

直接使用`Github Action`部署代码的时候，Github会在容器中拉下该仓库的代码。此时文件的创建时间和修改时间都是拉取时的时间。这会导致按照`updated`字段排序的博客在读取不到`.md`文件中的`updated`字段从而读取文件的修改时间变成统一的代码拉取时间。博客的文章展示就会变得混乱无章。

因此，需要在`Github Action`中单独进行一步操作，即修改文件的修改时间。

代码实现在上一节yaml文件的第35-37行。

实际上，`clone` 下来的文件的时间还是克隆时的时间，然后通过上面的命令，它将 `clone` 下来的文件的时间改成了该文件最近一次变动的推送时间（也即文件最后一次修改的 `push` 时间）。

> **注**：如果`github actions`中使用`actions/checkout@v2`，请设定它的参数`fetch-depth: 0`，因为`0`表示获取所有分支和标签的所有历史记录。默认值为`1`

小知识：

```shell
# 获取 git 仓库中所有文件的最新修改时间
git ls-tree -r --name-only HEAD | while read filename; do
  echo "$(git log -1 --format="%ad" -- $filename) $filename"
done
# 获取 git 仓库中所有文件的最初创建时间
git ls-tree -r --name-only HEAD | while read filename; do
	echo "$(git log --format="%ad" -- $filename | tail -1) $filename"
done
```

## 访问加速



## 分支存储源码

首先在Github的仓库中创建一个新的分支，因为Github Page默认是master分支，所以随便创建一个分支名即可。

在Hexo博客的顶层文件夹中执行如下命令：

1. 克隆Github中的当前项目，将克隆下载的文件夹中的.git文件夹移动到hexo博客的顶层目录。

2. 编写.gitignore：

   ```wiki
   .DS_Store
   Thumbs.db
   db.json
   *.log
   node_modules/
   public/
   .deploy*/
   ```

3. 创建本地分支并切换到该分支：`git checkout -b 分支名`

4. 添加文件：`git add .`

5. 添加注释信息：`git commit -m "hexo src update"`

6. 提交本地代码：`git push --set-upstream origin src`

7. 以后的上传代码则为：`git add . && git commit -m "hexo src update" && git push origin src && hexo d -g`

## SEO

站点地图即 sitemap，是一个页面，上面放置了网站上需要搜索引擎抓取的所有页面的链接。站点地图可以告诉搜索引擎网站上有哪些可供抓取的网页，以便搜索引擎可以更加智能地抓取网站。

### 安装插件

```shell
npm install hexo-generator-baidu-sitemap --save
npm install hexo-generator-sitemap --save
```

### 修改配置文件

在`_config.yml`中添加如下字段：

```json
# 自动生成sitemap
sitemap:
  path: sitemap.xml
baidusitemap:
  path: baidusitemap.xml
```

### 生成

执行`hexo g`。该命令会在public目录中生成`sitemap.xml`和`baidusitemap.xml`，这是生成的站点地图，里面包含了网站上所有页面的链接，搜索引擎通过这两个文件来抓取网站页面。

- sitemap.xml — 用来提交给 Google
- baidusitemap.xml — 用来提交给Baidu

### 百度站长平台

- 注册并登录百度站长平台：https://ziyuan.baidu.com/
- 验证网站所有权，有三种方式：文件验证、html标签、cname解析验证。推荐使用cname，感觉是最方便的。
- 添加sitemap

### Google站长平台

- 注册并登录Google站长平台：https://www.google.com/webmasters/verification/home
- 验证网站所有权，还是采用cname。默认是txt,需要选择备用方案。
- 添加sitemap

成功截图如下：

![Hexo博客搭建记录_Google收录成功.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/经验总结/Hexo博客搭建记录/Hexo博客搭建记录_Google收录成功.png)

## 参考文献

[^1]:[hexo-renderer-markdown-it 的配置与插件配置](https://blog.bugimg.com/works/hexo-renderer-markdown-it_and_plugins_config.html)
