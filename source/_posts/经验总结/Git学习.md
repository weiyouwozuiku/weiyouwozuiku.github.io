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

![其他版本控制系统](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/经验总结/Git学习/deltas.png)

Git 不按照以上方式对待或保存数据。反之，Git 更像是把数据看作是对小型文件系统的一系列快照。 在 Git 中，每当你提交更新或保存项目状态时，它基本上就会对当时的全部文件创建一个快照并保存这个快照的索引。 为了效率，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。 Git对待数据更像是一个**快照流**。

![Git](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/经验总结/Git学习/snapshots.png)

Git 用以计算校验和的机制叫做 SHA-1 散列（hash，哈希）。 这是一个由 40 个十六进制字符（0-9 和 a-f）组成的字符串，基于 Git 中文件的内容或目录结构计算出来。

Git存放文件的方式如图所示：

![Git存储原理.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/经验总结/Git学习/Git存储原理.png)

### .git解密

`.git`文件夹中存放着实现git的根本文件。以下来介绍其中主要的文件及其功能。

#### HEAD文件

这个文本文件中存放着当前指向的分支信息。

#### refs文件夹

refs文件夹中主要存放三个文件夹：heads、remotes、tags，分别标记本地的分支heads、远端分支remotes以及tags标记。

其中存放着相应分支的哈希值。

针对每个文件中的哈希值可以使用命令`git cat-file -t <哈希值>`来查看对应哈希类型。

#### config

config文件中存放的是当前git仓库的基础信息。

#### object

object文件夹中存放提交的记录，文件夹名称加上文件夹中文本的名称即为完成的哈希值。

查看哈希对应的文件使用命令`git cat-file -p <哈希值>`。

### 三种状态

- 已修改表示修改了文件，但还没保存到数据库中。
- 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。
- 已提交表示数据已经安全地保存在本地数据库中。

## 常见的传输协议

| 常用协议       | 语法格式                                     | 说明     |
| -------------- | -------------------------------------------- | -------- |
| 本地协议       | /path/to/repo.git                            | 哑协议   |
| 本地协议       | file:///path/to/repo.git                     | 智能协议 |
| http/https协议 | https://git-server.com:port/path/to/repo.git | 智能协议 |
| ssh协议        | user@git-server.com:path/to/repo.git         | 智能协议 |

哑协议传输速度不可见；智能协议传输可见。

智能协议比哑协议传输速度快。



## Git配置

Git使用配置工具实现控制Git外观与行为。一共有三种配置位置：

- /etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果在执行 git config 时带上 --system 选项，那么它就会读写该文件中的配置变量。 （由于它是系统配置文件，因此你需要管理员或超级用户权限来修改它。）
- ~/.gitconfig 或 ~/.config/git/config 文件：只针对当前用户。 你可以传递 --global 选项让 Git 读写此文件，这会对你系统上 所有 的仓库生效。
- 当前使用仓库的 Git 目录中的 config 文件（即 .git/config）：针对该仓库。 你可以传递 --local 选项让 Git 强制读写此文件，虽然默认情况下用的就是它。 （当然，你需要进入某个 Git 仓库中才能让该选项生效。）

## 分离头指针

Git允许将头指针移动到非head的节点，此时即为分离头指针的状态。在此之上的修改若是不绑定到某一分支上Git会默认理解为这是不重要的修改，Git本身并不会去记录。换句话说就是当进行切换分支的操作时，将会导致之前的修改丢失。因此，**当进行分离头指针的修改时需要记得绑定某一分支**。

## 常用命令

### 配置命令

配置用户信息：

```shell
 git config --global user.name "Your name"
 # github设置了email隐藏的话，需要将这里的邮箱设置成github的邮箱
 git cinfug --globel user.email "email@example.com"
```

上面的命令还可以使用`--local`和`--global`参数以区分不同的作用域。

`--local`表示只对某个仓库有效

`--global`表示对当前用户所有仓库有效

`--system`表示对系统所有登录的用户有效

查看当前所有配置：

```shell
git config --list [--local]
```

这个命令同样可以设置上述三种不同的作用域。

设置常用的文本编辑器：

```shell
git config --global core.editor {编辑器名字}
```

避免中文乱码：

在默认设置下，中文文件名在工作区状态输出，中文名不能正确显示，而是显示为八进制的字符编码。

将git配置文件 `core.quotepath`项设置为`false`。`quotepath`表示引用路径，加上`--global`表示全局配置。

```shell
git config --global core.quotepath false
```

### 操作命令

#### 创建仓库

- 已有的项目代码纳入Git管理

  ```shell
  cd 项目路径
  git init
  ```

- 新建的项目直接用Git管理

  ```shell
  git init 项目名 #会在当前路径下创建和项目名称相同的文件夹
  cd 项目名 
  ```

添加文件到Git仓库：

 ```shell
 git add <file>
 git commit -m "message"
 ```

 **`git add`即为精确的将内容添加到下一次提交中**

针对之前就Git跟踪过的文件直接使用`git add -u`即可。

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
 # 下面的命令效果同上
 git diff --cached
 ```

 **git diff 本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动.**

修改最近一次commit messgae：

```shell
git commit --amend
```

修改指定的commit message：

```shell
# 首先rabase到需要修改commit的上一个版本
git rebase -i <需修改的commit上一次提交的哈希值>
# 在跳出的界面中将需要修改的commit记录前面的pick之类的命令修改成r
# r 即reword 表示使用原本的提交，但修改commit message
# 在再一次弹出的界面中修改commit message
```

**修改完成之后原本的commit id 会发生改变**，因为messgae也是git结构体中的一员。

合并多次连续commit到一次commit中：

```shell
git rebase -i <需合并commit的前一次commit哈希>
# 在跳出的界面中将多次需要合并的commit前面的命令更改为s
# s 即squash 表示使用commit，但注册到上一次commit中
# 在再一次弹出的界面中修改总体的commit message
```

合并多次非连续commit到一次commit中：

```shell
git rebase -i <需合并commit的前一次commit哈希>
# 在跳出的界面中将多次需要合并的commit前面的命令更改为s
# s 即squash 表示使用commit，但注册到上一次commit中
# 修改commit之间的顺序，将被合并的commit挂在第一行commit之下
# 第一行的commit需要是一次被合并进的commit
# 如果是需要合并进第一次commit，可以自己写一行commit，拿第一次提交的commit的哈希作为标记
# 在再一次弹出的界面中修改总体的commit message
```

从暂存区和工作区删除文件:

```shell
git rm <file>
```

版本回退：

 ```shell
 git reset --hard commit_id(可以用HEAD~选择前第几个版本)
 ```

将存入暂存区中的指定文件撤销暂存：

```shell
git reset HEAD <-- file1 file2>
```

移动或重命名一个文件、目录或软连接：

```shell
git mv [file] [newfile]
```

查看现在的提交历史：

 ```shell
 git log [--pretty=oneline] [--all] [--graph]
 ```

可以使用`-n 2`查看前两条log，比如：`git log -n2 --oneline`

查看以往的所有提交历史：

```shell
 git reflog
```

查看工作区与暂存区的不同：

```shell
git diff [-- 文件名 ] # 不写指定的文件默认查看所有不同的文件，指定的话可以写多个文件名，用空格做分割
```

查看暂存区与HEAD之间的差异：

```shell
git diff --cached
```

查看工作区与版本库中版本之间的区别：

 ```shell
 git diff HEAD -- <file>
 ```

查看不同版本之间的diff：

```shell
git diff <commit哈希> <commit哈希> #这里可以使用HEAD进行标识，HEAD^1或者HEAD~1表示上一次提交 HEAD^^可以表示倒数第二次提交
```

查看不同分支之间的diff：

```shell 
git diff <分支1> <分支2> [-- <文件名>]# 可以通过指定文件名来查看不同分支两个文件的区别
```

将工作区文件恢复成最近一次git commit或者git add的状态:

```shell
 git checkout -- <file>
```

将暂存区的修改撤销：

 ```shell
 git reset HEAD [-- <file>] # 不指定文件默认撤销所有文件在暂存区的修改
 ```

删除之前的commit内容：（**危险，小心使用！暂存区和工作区都丢失**）

```shell 
git reset --hard <需要恢复到的commit哈希值>
```

**checkout影响工作区，reset影响暂存区**

创建ssh key：

```shell
 ssh-keygen -t rsa -C "youremail@example.com"
```

查看远端地址：

```shell
git remote -v
```

关联仓库：

 ```shell
 git remote add origin git@server-name:path/repo-name.git # 这里的origin只是给后面的地址起别名，但origin是默认名
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

使用指定分支创建分支：

```shell
git checkout -b <分支名> <指定分支名>
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
 #如果分支是none fast-forward的commit，使用--allow-unrelated-histories，即git merge --allow-unrelated-histories 分支名
 ```

删除分支（强制删除使用-D）：

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
 git stash apply 和 git stash drop或 git stash pop # 使用apply还会在stash中记录，但是使用pop会将这次修改直接从stash中清除
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
 git pull #这个命令包含了两步1. git fetch拉取代码 2. git merge将有上下游关系的分支做合并
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
- `?`只匹配任意一个字符
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
