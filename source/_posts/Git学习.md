---
title: Git学习
author: 不二
mathjax: true
date: 2021-05-19 13:44:48
tags: Git
img:
categories: 经验总结
---

创建仓库：

 ```shell
 git config --global user.name "Your name"
 git cinfug --globel user.email "email@example.com"
 git init
 ```

添加文件到Git仓库：

 ```shell
 git add <file>
 git commit -m "message"
 ```

工作区状态：

 ```shell
 git status
 ```
查看修改：

 ```shell
 git diff <file>
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
 git clone git@github.com:weiyouwozuiku/repo-name.git
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

