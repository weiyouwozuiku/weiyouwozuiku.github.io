---
title: powerlevel10k设置及客制化
date: 2020-02-15 20:32:48
tags:
- Linux
---

## Powerlevel10k

**Powerlevel10k is a theme for Zsh. It emphasizes speed, flexibility and out-of-the-box experience.**

### 安装

使用操作系统软件管理器安装Zsh。

### 安装Oh-My-Zsh主题

```shell
 sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

wget不能走sock代理，推荐使用curl下载。

### 基于Oh-My-Zsh的安装

```shell
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
```

### 设置主题

在`.zshrc`文件中设置主题

```shell
ZSH_THEME="powerlevel10k/powerlevel10k"
```

### 生成配置文件

使用`p10k configure`配置Powerlevel10k。会自动根据选项生成`.p10k.zsh`文件。根据喜好设置配置文件。

### 我的配置

```shell
 # The list of segments shown on the left. Fill it with the most important segments.
  typeset -g POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(
    # =========================[ Line #1 ]=========================
    context
    os_icon                 # os identifier
    Greeting
    public_ip
    disk_usage
    dir                     # current directory
    vcs                     # git status
    # =========================[ Line #2 ]=========================
    newline                 # \n
    prompt_char             # prompt symbol
  )

  # The list of segments shown on the right. Fill it with less important segments.
  # Right prompt on the last prompt line (where you are typing your commands) gets
  # automatically hidden when the input line reaches it. Right prompt above the
  # last prompt line gets hidden if it would overlap with left prompt.
  typeset -g POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(
    # =========================[ Line #1 ]=========================
    status                  # exit code of the last command
    command_execution_time  # duration of the last command
    background_jobs         # presence of background jobs
    #direnv                  # direnv status (https://direnv.net/)
    #asdf                    # asdf version manager (https://github.com/asdf-vm/asdf)
    #virtualenv              # python virtual environment (https://docs.python.org/3/library/venv.html)
    #anaconda                # conda environment (https://conda.io/)
    #pyenv                   # python environment (https://github.com/pyenv/pyenv)
    #goenv                   # go environment (https://github.com/syndbg/goenv)
    #nodenv                  # node.js version from nodenv (https://github.com/nodenv/nodenv)
    nvm                     # node.js version from nvm (https://github.com/nvm-sh/nvm)
    nodeenv                 # node.js environment (https://github.com/ekalinin/nodeenv)
    # node_version          # node.js version
    # go_version            # go version (https://golang.org)
    # rust_version          # rustc version (https://www.rust-lang.org)
    # dotnet_version        # .NET version (https://dotnet.microsoft.com)
    # php_version           # php version (https://www.php.net/)
    # laravel_version       # laravel php framework version (https://laravel.com/)
    #rbenv                   # ruby version from rbenv (https://github.com/rbenv/rbenv)
    #rvm                     # ruby version from rvm (https://rvm.io)
    #fvm                     # flutter version management (https://github.com/leoafarias/fvm)
    #luaenv                  # lua version from luaenv (https://github.com/cehoffman/luaenv)
    #jenv                    # java version from jenv (https://github.com/jenv/jenv)
    #plenv                   # perl version from plenv (https://github.com/tokuhirom/plenv)
    #phpenv                  # php version from phpenv (https://github.com/phpenv/phpenv)
    kubecontext             # current kubernetes context (https://kubernetes.io/)
    #terraform               # terraform workspace (https://www.terraform.io)
    #aws                     # aws profile (https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-profiles.h
    #aws_eb_env              # aws elastic beanstalk environment (https://aws.amazon.com/elasticbeanstalk/)
   # azure                   # azure account name (https://docs.microsoft.com/en-us/cli/azure)
    #gcloud                  # google cloud cli account and project (https://cloud.google.com/)
    #google_app_cred         # google application credentials (https://cloud.google.com/docs/authentication/productio
    #context                 # user@hostname
    #nordvpn                 # nordvpn connection status, linux only (https://nordvpn.com/)
    ranger                  # ranger shell (https://github.com/ranger/ranger)
    #nnn                     # nnn shell (https://github.com/jarun/nnn)
    vim_shell               # vim shell indicator (:sh)
    #midnight_commander      # midnight commander shell (https://midnight-commander.org/)
    #nix_shell               # nix shell (https://nixos.org/nixos/nix-pills/developing-with-nix-shell.html)
    vi_mode                 # vi mode (you don't need this if you've enabled prompt_char)
    # vpn_ip                # virtual private network indicator
     load                  # CPU load
     disk_usage            # disk usage
     ram                   # free RAM
     swap                  # used swap
    #todo                    # todo items (https://github.com/todotxt/todo.txt-cli)
    timewarrior             # timewarrior tracking status (https://timewarrior.net/)
    time                    # current time
    #my_weather
    # =========================[ Line #2 ]=========================
    newline                 # \n
     ip                    # ip address and bandwidth usage for a specified network interface
    # public_ip             # public IP address
    # proxy                 # system-wide http/https/ftp proxy
     battery               # internal battery
    # wifi                  # wifi speed
    # example               # example user-defined segment (see prompt_example function below)
  )
```

默认情况下`context`组件不显示，需要修改才能显示：

```shell
##################################[ context: user@hostname ]##################################
  # Context color when running with privileges.
  typeset -g POWERLEVEL9K_CONTEXT_ROOT_FOREGROUND=178
  # Context color in SSH without privileges.
  typeset -g POWERLEVEL9K_CONTEXT_{REMOTE,REMOTE_SUDO}_FOREGROUND=180
  # Default context color (no privileges, no SSH).
  typeset -g POWERLEVEL9K_CONTEXT_FOREGROUND=180

  # Context format when running with privileges: bold user@hostname.
  typeset -g POWERLEVEL9K_CONTEXT_ROOT_TEMPLATE='%B%n@%m'
  # Context format when in SSH without privileges: user@hostname.
  typeset -g POWERLEVEL9K_CONTEXT_{REMOTE,REMOTE_SUDO}_TEMPLATE='%n@%m'
  # Default context format (no privileges, no SSH): user@hostname.
  typeset -g POWERLEVEL9K_CONTEXT_TEMPLATE='%n@%m'

  # Don't show context unless running with privileges or in SSH.
  # Tip: Remove the next line to always show context.
  # 在这里开启了context一直显示的选项
  #typeset -g POWERLEVEL9K_CONTEXT_{DEFAULT,SUDO}_{CONTENT,VISUAL_IDENTIFIER}_EXPANSION=

  # Custom icon.
  # typeset -g POWERLEVEL9K_CONTEXT_VISUAL_IDENTIFIER_EXPANSION='⭐'
  # Custom prefix.
  # typeset -g POWERLEVEL9K_CONTEXT_PREFIX='%246Fwith '
```

默认情况的IP组件无法显示，需要配置相应的网卡信息：

```shell
 # Show information for the first network interface whose name matches this regular expression.
  # Run `ifconfig` or `ip -4 a show` to see the names of all network interfaces.
  typeset -g POWERLEVEL9K_IP_INTERFACE='wlp2s0*'
```

  自己扩展功能的方式就是以shell中函数的方式定义。函数名需要以`prompt_my_`作为前缀。在前面的设置中直接写之后的函数就行。显示必须依托`p10k segment`才能显示在主题中。可以使用`p10k help segment`获取使用方法。

修改显示的系统图标：

```shell
  #################################[ os_icon: os identifier ]##################################
  # OS identifier color.
  typeset -g POWERLEVEL9K_OS_ICON_FOREGROUND=255
  # Make the icon bold.
  #typeset -g POWERLEVEL9K_OS_ICON_CONTENT_EXPANSION='%B${P9K_CONTENT}'
  # 修改显示的系统图标为Arch
  typeset -g POWERLEVEL9K_OS_ICON_CONTENT_EXPANSION=%B$'\uF303 '     
```

`'\uF303'`为Arch图标，`'\uF17c'`为Linux图标。后面如果显示字符位不足的时候在编码后添加适量空格，`%B`表示加粗。

### 自定义的问候语程序

实现思路：通过`date`程序获取当前小时数。通过小时数判断当前是哪一个时间段。

```shell
function prompt_Greeting() {
    local hour=`date "+%H"`
    local name=",king"
    if [ $hour -lt 12 -a $hour -gt 4  ]
    then 
        local str="Morning"
    elif [ $hour -eq 12 ]
    then
        local str="Noon"
    elif [ $hour -gt 12 -a $hour -lt 19 ]                                                                            
    then
        local str="Afternoon"
    else
        local str="Evening"
    fi
    p10k segment -f 208 -i '㋡' -t $str$name 
  }
```

### 自定义的显示当前天气的程序

实现思路：先查询电脑是否连接网络。若连接，去`/tmp`路径中查找是否有一年中相同天数相同小时的文件。这个路径下的文件在关机后自动删除，所以在这不考虑重复的可能。每小时去获取一次当前天气。避免频繁获取造成的终端响应缓慢。获取后写入文件中，同一小时内，天气信息从文件中获取。

```shell
function prompt_my_weather() {
    ping -c1 114.114.114.114 &> /dev/null
    if [ $? -eq 0 ]
    then
        local weatherDay=`date "+%j"`
        local weatherHour=`date "+%H"`
        local weatherPath="/tmp/"
        local weatherName="my_weather"
        local weatherFileType=".txt"
        local weatherFile=$weatherPath$weatherName$weatherDay$weatherHour$weatherFileType
        if [ ! -e $weatherFile -o ! -s $weatherFile ]
        then
            touch "$weatherFile"
            local weather=`curl -s "wttr.in/?format=3 "`
            echo $weather > $weatherFile
        else
            local weather=$(cat $weatherFile)
        fi
    else
        local weather="无网络"
    fi
    p10k segment -f '#ffb44b' -t $weather

 }
```



