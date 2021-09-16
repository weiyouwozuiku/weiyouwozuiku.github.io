---
title: Git学习
author: 不二
mathjax: true
date: 2021-05-19 13:44:48
tags: Git
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/themes/hexo-theme-matery/source/medias/featureimages/git.jpg
categories: 经验总结
---

## 原理

Git区别于其他版本控制系统的主要差别在于**Git对待数据的方式**。

其他版本控制系统以文件变更列表的方式存储信息，它们将存储的信息看作是一组基本文件和每个文件随时间逐步累积的差异，因此它们往往也叫做基于差异（delta-based）的版本控制。

![其他版本控制系统](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/Git学习/deltas.png)

Git 不按照以上方式对待或保存数据。反之，Git 更像是把数据看作是对小型文件系统的一系列快照。 在 Git 中，每当你提交更新或保存项目状态时，它基本上就会对当时的全部文件创建一个快照并保存这个快照的索引。 为了效率，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。 Git对待数据更像是一个**快照流**。

![Git](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/Git学习/snapshots.png)

Git 用以计算校验和的机制叫做 SHA-1 散列（hash，哈希）。 这是一个由 40 个十六进制字符（0-9 和 a-f）组成的字符串，基于 Git 中文件的内容或目录结构计算出来。

### 三种状态

- 已修改表示修改了文件，但还没保存到数据库中。
- 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。
- 已提交表示数据已经安全地保存在本地数据库中。

## Git配置

Git使用配置工具实现控制Git外观与行为。一共有三种配置位置：

- /etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果在执行 git config 时带上 --system 选项，那么它就会读写该文件中的配置变量。 （由于它是系统配置文件，因此你需要管理员或超级用户权限来修改它。）

- ~/.gitconfig 或 ~/.config/git/config 文件：只针对当前用户。 你可以传递 --global 选项让 Git 读写此文件，这会对你系统上 所有 的仓库生效。

- 当前使用仓库的 Git 目录中的 config 文件（即 .git/config）：针对该仓库。 你可以传递 --local 选项让 Git 强制读写此文件，虽然默认情况下用的就是它。。 （当然，你需要进入某个 Git 仓库中才能让该选项生效。）

## 常用命令

### 配置命令

配置用户信息：

```shell
 git config --global user.name "Your name"
 # github设置了email隐藏的话，需要将这里的邮箱设置成github的邮箱
 git cinfug --globel user.email "email@example.com"
```

查看当前所有配置：

```shell
git config --list
```

设置常用的文本编辑器：

```shell
git config --global core.editor {编辑器名字}
```

### 操作命令



创建仓库：

 ```shell
 git init
 ```

添加文件到Git仓库：

 ```shell
 git add <file>
 git commit -m "message"
 ```

 **`git add`即为精确的将内容添加到下一次提交中**

工作区状态：

 ```shell
 git status
 # 状态简浏览
 git status -s # git status --short
  M README # 在工作区已修改但尚未暂存
MM Rakefile # 文件已修，暂存后又作了修改
A  lib/git.rb # 新添加到暂存区中的文件前面有 A 标记
M  lib/simplegit.rb # 已修改且已暂存
?? LICENSE.txt # 新添加到暂存区中的文件
 ```

**修改过的文件前面有 M 标记。 输出中有两栏，左栏指明了暂存区的状态，右栏指明了工作区的状态。**

查看修改：

 ```shell
 # 查看工作目录中当前文件和暂存区域快照之间的差异
 git diff <file>
 # 查看已暂存的将要添加到下次提交里的内容
 git diff --staged
 ```

版本回退：

 ```shell
 git reset --hard commit_id(可以用HEAD~选择前第几个版本)
 ```

查看现在的提交历史：

 ```shell
 git log [--pretty=oneline]
 ```

查看以往的所有提交历史：

```shell
 git reflog
```

查看工作区与版本库中版本之间的区别：

 ```shell
 git diff HEAD -- <file>
 ```

将工作区文件恢复成最近一次git commit或者git add的状态:

```shell
 git checkout -- <file>
```

将暂存区的修改撤销，重新放回工作区：

 ```shell
 git reset HEAD <file>
 ```

创建新分支：

```shell
 git checkout -b <分支名>
```

创建ssh key：

```shell
 ssh-keygen -t rsa -C "youremail@example.com"
```

关联仓库：

 ```shell
 git remote add origin git@server-name:path/repo-name.git
 ```

第一次推送分支内容：

```shell
 git push -u origin master(以后不用加-u参数)
```

克隆一个本地库：

 ```shell
 # 默认配置下远程 Git 仓库中的每一个文件的每一个版本都将被拉取下来
 git clone <git仓库名> 自定义仓库名
 ```

创建分支：

```shell
 git checkout -b <branch-name>(可以拆分为 git branch <branch-name> 和 git checkout <branch-name>)
```

查看分支：

```shell
 git branch(当前分支前会有*作为标记)
```

切换分支：

 ```shell
 git checkout <file>
 ```

合并指定分支到当前分支：

 ```shell
 git merge <branch-name>
 ```

删除分支：

```shell
 git branch -d <branch-name>
```

查看分支情况：

 ```shell
 git log --graph --pretty=oneline --abbrev-commit(有分支合并图)
 ```

强制禁用Fast forward模式：

```shell
 git merge --no-ff -m "merge with no-ff" <branch-name>(会创建一个新的commit)
```

现场存储：

```shell
 git stash
```

查看现场存储列表：

```shell
 git stash list
```

恢复现场：

```shell
 git stash apply 和 git stash drop或 git stash pop
```

指定恢复现场：

```shell
 git stash apply stash@{<stash_list>}
```

丢弃未合并的分支：

```shell
 git branch -D <name>
```

查看远程库信息：

```shell
 git remote <-v>
```

抓取版本库：

```shell
 git pull
```

本地分支与远程分支链接：

 ```shell
 git branch --set-upstream branch-name origin/branch-name
 ```

推送修改：

```shell
 git push origin branch-name
```

创建标签：

```shell
 git tag <name>
```

查看标签：

```shell
 git tag
```

查看标签信息：

```shell
 git show <tagname>
```

用私钥给标签签名：

```shell
 git tag -s tag-name -m "message" commit-id
```

删除标签：

```shell
 git tag -d tag-name
```

推送某个标签到远程：

 ```shell
 git push origin <tagname>
 ```

推送所有标签：

 ```shell
 git push origin --tags
 ```

远程删除标签：

```shell
 git push origin :refs/tags/<tagname>
```

git 显示颜色：

```shell
 git config --global color.ui true
```

不想提交的文件放在文件夹下面的.gitignore目录中

`.gitignore`文件中可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。

其中glob的模式匹配是指shell所用的简化了的正则表达式。

- `*`匹配零个或多个人一字符。
- `[abc]`匹配任何一个列在方括号中的字符
- `? `只匹配任意一个字符
- `[0-9]` 表示匹配所有 0 到 9 的数字
- `**`匹配任意中中间目录

```shell
# 忽略所有的 .a 文件
*.a
# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a
# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO
# 忽略任何目录下名为 build 的文件夹
build/
# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt
# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```

在最简单的情况下，一个仓库可能只根目录下有一个 .gitignore 文件，它递归地应用到整个仓库中。 然而，子目录下也可以有额外的 .gitignore 文件。子目录中的 .gitignore 文件中的规则只作用于它所在的目录中。

强制添加文件：

```shell
 git add -f <file-name>
```

查看.gitignore的问题：

```shell
 git check-ignore -v <file-name>
```

命名别名：

```shell
 git config --global alias.<缩写> <全称>
```

查看提交信息：

```shell
git log -stat -l
```

检查提交散列值：

```shell
 git fsck
```

 将文件移出暂存区

```shell
 git reset head <filename>
```

无敌配置：

```shell
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```
