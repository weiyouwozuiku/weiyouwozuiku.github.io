---
title: Tmux
author: 不二
date: 2019-08-02 09:43:09
mathjax: true
tags: 
- Linux
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/经验总结/tmux.png
categories: 经验总结
---

## Tmux

### 安装

```shell
sudo pacman -S tmux
```

### 使用

创建session：tmux new -s `session_name`。

直接退出在终端输入`exit`即可。

<!-- more -->

暂时退出tmux，按ctrl+b+d。或者输入tmux detach。

`tmux ls`显示所有tmux的session。

使用`tmux kill-session -t session_name`kill掉不要的session。

当后台只有一个session时，直接输入`tmux attach`即可返回session。

当后台session数不唯一时，输入`tmux attach -t session_name`返回相应session。可以将`attach`简写为a。

如果刚好处于会话中怎么办？别担心，我们可以使用对应的tmux快捷键`Ctrl+b` + `s`，此时tmux将打开一个会话列表，按上下键(⬆︎⬇︎)或者鼠标滚轮，可选中目标会话，按左右键（⬅︎➜）可收起或展开会话的窗口，选中目标会话或窗口后，按回车键即可完成切换。

在进入tmux后：

- 系统指令

|   前缀   |   指令   |                   描述                   |
| :------: | :------: | :--------------------------------------: |
| `Ctrl+b` |   `?`    |            显示快捷键帮助文档            |
| `Ctrl+b` |   `d`    |               断开当前会话               |
| `Ctrl+b` |   `D`    |             选择要断开的会话             |
| `Ctrl+b` | `Ctrl+z` |               挂起当前会话               |
| `Ctrl+b` |   `r`    |             强制重载当前会话             |
| `Ctrl+b` |   `s`    |        显示会话列表用于选择并切换        |
| `Ctrl+b` |   `:`    | 进入命令行模式，此时可直接输入`ls`等命令 |
| `Ctrl+b` |   `[`    |         进入复制模式，按`q`退出          |
| `Ctrl+b` |   `]`    |         粘贴复制模式中复制的文本         |
| `Ctrl+b` |   `~`    |             列出提示信息缓存             |

- 窗口指令

|   前缀   | 指令  |                    描述                    |
| :------: | :---: | :----------------------------------------: |
| `Ctrl+b` |  `c`  |                  新建窗口                  |
| `Ctrl+b` |  `&`  | 关闭当前窗口（关闭前需输入`y` or `n`确认） |
| `Ctrl+b` | `0~9` |               切换到指定窗口               |
| `Ctrl+b` |  `p`  |               切换到上一窗口               |
| `Ctrl+b` |  `n`  |               切换到下一窗口               |
| `Ctrl+b` |  `w`  |        打开窗口列表，用于且切换窗口        |
| `Ctrl+b` |  `,`  |               重命名当前窗口               |
| `Ctrl+b` |  `.`  |   修改当前窗口编号（适用于窗口重新排序）   |
| `Ctrl+b` |  `f`  |  快速定位到窗口（输入关键字匹配窗口名称）  |

- 面板命令

|   前缀   |     指令      |                             描述                             |
| :------: | :-----------: | :----------------------------------------------------------: |
| `Ctrl+b` |      `"`      |              当前面板上下一分为二，下侧新建面板              |
| `Ctrl+b` |      `%`      |              当前面板左右一分为二，右侧新建面板              |
| `Ctrl+b` |      `x`      |          关闭当前面板（关闭前需输入`y` or `n`确认）          |
| `Ctrl+b` |      `z`      |   最大化当前面板，再重复一次按键后恢复正常（v1.8版本新增）   |
| `Ctrl+b` |      `!`      | 将当前面板移动到新的窗口打开（原窗口中存在两个及以上面板有效） |
| `Ctrl+b` |      `;`      |                   切换到最后一次使用的面板                   |
| `Ctrl+b` |      `q`      |  显示面板编号，在编号消失前输入对应的数字可切换到相应的面板  |
| `Ctrl+b` |      `{`      |                       向前置换当前面板                       |
| `Ctrl+b` |      `}`      |                       向后置换当前面板                       |
| `Ctrl+b` |   `Ctrl+o`    |                顺时针旋转当前窗口中的所有面板                |
| `Ctrl+b` |   `方向键`    |                       移动光标切换面板                       |
| `Ctrl+b` |      `o`      |                         选择下一面板                         |
| `Ctrl+b` |   `空格键`    |                  在自带的面板布局中循环切换                  |
| `Ctrl+b` | `Alt+方向键`  |              以5个单元格为单位调整当前面板边缘               |
| `Ctrl+b` | `Ctrl+方向键` |  以1个单元格为单位调整当前面板边缘（Mac下被系统快捷键覆盖）  |
| `Ctrl+b` |      `t`      |                           显示时钟                           |

默认的tmux风格比较朴素甚至有些丑陋。如果希望做一些美化和个性化配置的话，建议使用[gpakosz的tmux配置](https://github.com/gpakosz/.tmux)。它的本质是一个tmux配置文件。

### 开启Home和END键

在.zshrc中添加` export TERM="screen-256color"`。

### 在Tmux中复制

开启鼠标支持，配置后可以直接`Ctrl+a m`。

或者文件配置：

```shell
#开启鼠标支持
set-option -g mouse on
```

复制 按住`shift`键,然后拖动鼠标复制要选择的内容，然后按下`shift+ctrl+c`,复制到系统剪贴板
 粘贴 按下`shift+ctrl+v` 粘贴系统剪贴板中的内容到tmux中
 需要注意的是
 `shift+ctrl+v`是在终端设置的粘贴快捷键
 `shift+ctrl+c`是在终端设置的复制快捷键
