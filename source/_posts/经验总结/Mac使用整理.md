---
title: Mac使用整理
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/经验总结/mbp.jpg
mathjax: true
date: 2021-11-06 15:04:13
tags:
- Mac
categories: 经验总结
---
## 摘要

本文旨在记录和汇总自己在工作以及学习过程中所使用的软件及其所遇问题的解决方案,以便日后所需.

## 可视化

- Cloud Battery
- Charles
- Viusal Studio Code
- QQ音乐
- 微信
- 网易云音乐
- Chrome
- 飞书
- XMind
- Dash
- Navicat
- Wireshark
- Postman
- Telegram
- 百度网盘
- 阿里云盘
- Trello
- Termius
- Motrix
- Downie
- EasyConnect
- Logi Options
- 滴答清单
- PDF Expert
- IINA
- Jetbrains家族
- Office家族
- Sensei
- iTerm2
- Pock
- Bandizip
- Barrier
- fing
- Typora
- OpenInTerminal
- Countdown Timer Plus
- Mos
- HHKB Keymap Tool
- Alfred
- Kronos Time Tra
- Karabiner-Elements
- Karabiner-EventViewer
- Karabiner-VirtualHIDDevice-Manager
- Magnet
- iShot
- Alacritty
- [stats](https://github.com/exelban/stats)
- One Switch 
- Dash
- BetterTouchTool
- Transmit
- Mountain Duck
- Moment
- Progressive Downloader
- Pika
- [another-redis-desktop-manager](https://github.com/qishibo/AnotherRedisDesktopManager)
- OmniGraffle
- MonitorControl
- NeatDownloadManager
- Swish
- App Cleaner & Uninstaller Pro
- licecap

## 终端

- brew
- ranger
- fzf
- htop
- git
- fd
- ripgrep
- htop
- glances(M1暂时没有适配)
- ctop
- lazydocker
- colordiff
- bat
- httpie
- ncdu
- axel

## zsh配置

### 安装zsh插件

- git-open: git clone https://github.com/paulirish/git-open.git $ZSH_CUSTOM/plugins/git-open
- zsh-syntax-highlighting: git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
- zsh-autosuggestions: git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

### .zshrc

```shell
# Enable Powerlevel10k instant prompt. Should stay close to the top of ~/.zshrc.
# Initialization code that may require console input (password prompts, [y/n]
# confirmations, etc.) must go above this block; everything else may go below.
if [[ -r "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-$HOME/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

# If you come from bash you might have to change your $PATH.
# export PATH=$HOME/bin:/usr/local/bin:$PATH

# Path to your oh-my-zsh installation.
export ZSH="/Users/robertwang/.oh-my-zsh"

# Set name of the theme to load --- if set to "random", it will
# load a random theme each time oh-my-zsh is loaded, in which case,
# to know which specific one was loaded, run: echo $RANDOM_THEME
# See https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
ZSH_THEME="powerlevel10k/powerlevel10k"

# Set list of themes to pick from when loading at random
# Setting this variable when ZSH_THEME=random will cause zsh to load
# a theme from this variable instead of looking in $ZSH/themes/
# If set to an empty array, this variable will have no effect.
# ZSH_THEME_RANDOM_CANDIDATES=( "robbyrussell" "agnoster" )

# Uncomment the following line to use case-sensitive completion.
# CASE_SENSITIVE="true"

# Uncomment the following line to use hyphen-insensitive completion.
# Case-sensitive completion must be off. _ and - will be interchangeable.
# HYPHEN_INSENSITIVE="true"

# Uncomment one of the following lines to change the auto-update behavior
# zstyle ':omz:update' mode disabled  # disable automatic updates
# zstyle ':omz:update' mode auto      # update automatically without asking
# zstyle ':omz:update' mode reminder  # just remind me to update when it's time

# Uncomment the following line to change how often to auto-update (in days).
# zstyle ':omz:update' frequency 13

# Uncomment the following line if pasting URLs and other text is messed up.
# DISABLE_MAGIC_FUNCTIONS="true"

# Uncomment the following line to disable colors in ls.
# DISABLE_LS_COLORS="true"

# Uncomment the following line to disable auto-setting terminal title.
# DISABLE_AUTO_TITLE="true"

# Uncomment the following line to enable command auto-correction.
# ENABLE_CORRECTION="true"

# Uncomment the following line to display red dots whilst waiting for completion.
# You can also set it to another string to have that shown instead of the default red dots.
# e.g. COMPLETION_WAITING_DOTS="%F{yellow}waiting...%f"
# Caution: this setting can cause issues with multiline prompts in zsh < 5.7.1 (see #5765)
# COMPLETION_WAITING_DOTS="true"

# Uncomment the following line if you want to disable marking untracked files
# under VCS as dirty. This makes repository status check for large repositories
# much, much faster.
# DISABLE_UNTRACKED_FILES_DIRTY="true"

# Uncomment the following line if you want to change the command execution time
# stamp shown in the history command output.
# You can set one of the optional three formats:
# "mm/dd/yyyy"|"dd.mm.yyyy"|"yyyy-mm-dd"
# or set a custom format using the strftime function format specifications,
# see 'man strftime' for details.
# HIST_STAMPS="mm/dd/yyyy"

# Would you like to use another custom folder than $ZSH/custom?
# ZSH_CUSTOM=/path/to/new-custom-folder

# Which plugins would you like to load?
# Standard plugins can be found in $ZSH/plugins/
# Custom plugins may be added to $ZSH_CUSTOM/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(git
    z
    extract
    hitokoto
    gitignore
    cp
    git-open
    #safe-paste
    sudo
    zsh-syntax-highlighting
    zsh-autosuggestions
    history
    tmuxinator
    tmux
    )

source $ZSH/oh-my-zsh.sh

# User configuration

# export MANPATH="/usr/local/man:$MANPATH"

# You may need to manually set your language environment
# export LANG=en_US.UTF-8

# Preferred editor for local and remote sessions
# if [[ -n $SSH_CONNECTION ]]; then
#   export EDITOR='vim'
# else
#   export EDITOR='mvim'
# fi

# Compilation flags
# export ARCHFLAGS="-arch x86_64"

# Set personal aliases, overriding those provided by oh-my-zsh libs,
# plugins, and themes. Aliases can be placed here, though oh-my-zsh
# users are encouraged to define aliases within the ZSH_CUSTOM folder.
# For a full list of active aliases, run `alias`.
#
# Example aliases
# alias zshconfig="mate ~/.zshrc"
# alias ohmyzsh="mate ~/.oh-my-zsh"

# To customize prompt, run `p10k configure` or edit ~/.p10k.zsh.
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh
export FZF_COMPLETION_TRIGGER='\'

[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh

export FZF_DEFAULT_OPTS="--height 80% --layout=reverse --preview '(highlight -O ansi {} || cat {}) 2> /dev/null | head -500'"
alias setproxy='export https_proxy=http://192.168.50.77:20171 http_proxy=http://192.168.50.77:20171 all_proxy=socks5://192.168.50.77:20170'
```

### ohmyzsh本地化

#### 安装ohmyzsh

下载 

```shell
wget https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh
```

编辑

```json
# Default settings
ZSH=${ZSH:-~/.oh-my-zsh}
REPO=${REPO:-mirrors/oh-my-zsh}
REMOTE=${REMOTE:-https://gitee.com/${REPO}.git}
BRANCH=${BRANCH:-master}
```

#### 修改仓库地址

```json
d ~/.oh-my-zsh
git remote set-url origin https://gitee.com/mirrors/oh-my-zsh.git
git pull
omz update //oh my zsh 更新
```

### powerline10k

## 修改iTerm样式

`iterm->preferences->Profiles->Session->Status bar enabled->Configure Status Bar`可以设置Iterm上方展示系统信息等内容。

## Mac内置键盘屏蔽

借助Karabiner可以实现在检测到外置键盘或鼠标存在的时候，底层屏蔽内置键盘的输入。

## 键盘映射

同样是借助Karabiner实现键盘映射的功能，单个按键简单映射直接在软件本身设置即可。针对组合键的映射，可以通过在`.config/karabiner/assets/complex_modifications`文件中编写任意文件名的json文件来实现。

```json
{
  "title": "vim",
  "rules": [
    {
      "description": "option+hjkl;",
      "manipulators": [
        {
          "description": "option+h",
          "type": "basic",
          "from": {
            "key_code": "h",
            "modifiers": {
              "mandatory": [
                "left_option"
              ]
            }
          },
          "to": [
            {
              "key_code": "left_arrow"
            }
          ]
        },
        {
          "description": "option+j",
          "type": "basic",
          "from": {
            "key_code": "j",
            "modifiers": {
              "mandatory": [
                "left_option"
              ]
            }
          },
          "to": [
            {
              "key_code": "down_arrow"
            }
          ]
        },
        {
          "description": "option+k",
          "type": "basic",
          "from": {
            "key_code": "k",
            "modifiers": {
              "mandatory": [
                "left_option"
              ]
            }
          },
          "to": [
            {
              "key_code": "up_arrow"
            }
          ]
        },
        {
          "description": "option+l",
          "type": "basic",
          "from": {
            "key_code": "l",
            "modifiers": {
              "mandatory": [
                "left_option"
              ]
            }
          },
          "to": [
            {
              "key_code": "right_arrow"
            }
          ]
        },
        {
          "description": "option+;",
          "type": "basic",
          "from": {
            "key_code": "semicolon",
            "modifiers": {
              "mandatory": [
                "left_option"
              ]
            }
          },
          "to": [
            {
              "key_code": "return_or_enter"
            }
          ]
        }
      ]
    }
  ]
}
```

## SSH

为了保持会话的长久链接可以在本地通过新增 `.ssh/config`文件中如下字段：

```shell
Host *
  ServerAliveInterval 60
```

## vim

安装 `vimplus`

```shell
git clone https://github.com/chxuan/vimplus.git ~/.vimplus
cd ~/.vimplus
./install.sh
```

直接安装完成直接会因为apple自研芯片的原因导致安装YouCompleteMe的过程中其实安装失败.可以使用 `python3 install.py --all --system-libclang`在YouCompleteMe文件夹中安装.

安装YouCompleteMe中可能出现`Python headers are missing in /Applications/Xcode.app/Contents/Developer/Library/Frameworks/Python3.framework/Versions/3.7/Headers.`的错误，只需要执行`echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.zshrc`即可。这个问题的原因可以通过执行`brew doctor`发现。

## ranger

### 安装

`brew install ranger`

### 插件

- ranger图标
  
```shell
git clone https://github.com/alexanderjeurissen/ranger_devicons ~/.config/ranger/plugins/ranger_devicons
echo "default_linemode devicons" >> $HOME/.config/ranger/rc.conf
```

- 图片预览

Add the following lines to your ~/.config/ranger/rc.conf:

```shell
set preview_images true
set preview_images_method iterm2
```

- highlight

```shell
brew install highlight
```

## fzf

### 安装

```shell
brew install fzf
#安装按键绑定与模糊补全
$(brew --prefix)/opt/fzf/install
```

### 配置

修改`.zshrc`

```shell
# 修改fzf的触发符号
export FZF_COMPLETION_TRIGGER='\'
# 修改fzf默认展示
export FZF_DEFAULT_OPTS="--height 80% --layout=reverse --preview '(highlight -O ansi {} || cat {}) 2> /dev/null | head -500'"
```

## lrzsz

### 安装

```shell
brew install lrzsz
```

### 安装执行脚本

将[iterm2-send-zmodem.sh](https://raw.githubusercontent.com/RobberPhex/iterm2-zmodem/master/iterm2-send-zmodem.sh)和[iterm2-recv-zmodem.sh](https://raw.githubusercontent.com/RobberPhex/iterm2-zmodem/master/iterm2-recv-zmodem.sh)保存到`/usr/local/bin`目录下。

`iterm2-send-zmodem.sh`

```shell
#!/bin/bash
# Author: Matt Mastracci (matthew@mastracci.com)
# AppleScript from http://stackoverflow.com/questions/4309087/cancel-button-on-osascript-in-a-bash-script
# licensed under cc-wiki with attribution required
# Remainder of script public domain

osascript -e 'tell application "iTerm2" to version' > /dev/null 2>&1 && NAME=iTerm2 || NAME=iTerm
if [[ $NAME = "iTerm" ]]; then
    FILE=$(osascript -e 'tell application "iTerm" to activate' -e 'tell application "iTerm" to set thefile to choose file with prompt "Choose a file to send"' -e "do shell script (\"echo \"&(quoted form of POSIX path of thefile as Unicode
else
    FILE=$(osascript -e 'tell application "iTerm2" to activate' -e 'tell application "iTerm2" to set thefile to choose file with prompt "Choose a file to send"' -e "do shell script (\"echo \"&(quoted form of POSIX path of thefile as Unico
fi
if [[ $FILE = "" ]]; then
    echo Cancelled.
    # Send ZModem cancel
    echo -e \\x18\\x18\\x18\\x18\\x18
    sleep 1
    echo
    echo \# Cancelled transfer
else
    /opt/homebrew/bin/sz "$FILE" --escape --binary --bufsize 4096
    sleep 1
    echo
    echo \# Received "$FILE"
fi
```

`iterm2-recv-zmodem.sh`

```shell
#!/bin/bash
# Author: Matt Mastracci (matthew@mastracci.com)
# AppleScript from http://stackoverflow.com/questions/4309087/cancel-button-on-osascript-in-a-bash-script
# licensed under cc-wiki with attribution required
# Remainder of script public domain

osascript -e 'tell application "iTerm2" to version' > /dev/null 2>&1 && NAME=iTerm2 || NAME=iTerm
if [[ $NAME = "iTerm" ]]; then
    FILE=$(osascript -e 'tell application "iTerm" to activate' -e 'tell application "iTerm" to set thefile to choose folder with prompt "Choose a folder to place received files in"' -e "do shell script (\"echo \"&(quoted form of POSIX pat
else
    FILE=$(osascript -e 'tell application "iTerm2" to activate' -e 'tell application "iTerm2" to set thefile to choose folder with prompt "Choose a folder to place received files in"' -e "do shell script (\"echo \"&(quoted form of POSIX p
fi

if [[ $FILE = "" ]]; then
    echo Cancelled.
    # Send ZModem cancel
    echo -e \\x18\\x18\\x18\\x18\\x18
    sleep 1
    echo
    echo \# Cancelled transfer
else
    cd "$FILE"
    /opt/homebrew/bin/rz --rename --escape --binary --bufsize 4096
    sleep 1
    echo
    echo
    echo \# Sent \-\> $FILE
fi
```

### 授予执行权限

```shell
sudo chmod +x /usr/local/bin/iterm2-*
```

### 设置iterm的trigger

设置Iterm2的Tirgger特性，`Preferences->Profiles->Default->Advanced->Trigged->Edit`

```shell
Regular expression: rz waiting to receive.\*\*B0100
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-send-zmodem.sh
Instant: checked

Regular expression: \*\*B00000000000000
Action: Run Silent Coprocess
Parameters: /usr/local/bin/iterm2-recv-zmodem.sh
Instant: checked
```

![lrzsz设置iterm的trigger.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/经验总结/Mac使用整理/lrzsz设置iterm的trigger.png)

### 使用

- 上传文件：rz
- 下载文件：sz + filename

## Alfred

[YoudaoTranslate](https://github.com/wensonsmith/YoudaoTranslator)

[Alfred-codezm-workflows-timestamp-convert](https://github.com/codezm/Alfred-codezm-workflows-timestamp-convert)

[alfred-terminalfinder](https://github.com/LeEnno/alfred-terminalfinder)

[coversion](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/经验总结/Mac使用整理/coversion.alfredworkflow)

[IP Address](https://github.com/zenorocha/alfred-workflows/raw/master/ip-address/ip-address.alfredworkflow)

[Alfred-BaseConversion](https://github.com/weiyouwozuiku/ToolBox/blob/main/Alfred-BaseConversion/Base%20Conversion.alfredworkflow)

[Alfred-Workflow](https://github.com/noogel/Alfred-Workflow)

## gh-md-toc

这是一个生成README文件目录的工具，https://github.com/ekalinin/github-markdown-toc。

在想要增加目录的地方加上如下标志符

```
<!--ts-->
<!--te-->
```

使用命令`gh-md-toc --insert <文件名>`即可。

## 参考资料

1. [Fuzzy finder(fzf+vim) 使用全指南](https://keelii.com/2018/08/12/fuzzy-finder-full-guide/),2018
1. [iterm2 rz与sz的功能](https://www.huweihuang.com/linux-notes/keymap/iterm2-rzsz.html),2019
