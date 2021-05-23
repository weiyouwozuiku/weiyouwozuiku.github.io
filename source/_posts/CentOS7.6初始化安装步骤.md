---
title: CentOS7.6初始化安装步骤
author: 不二
date: 2019-12-08 09:46:56
mathjax: true
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/centos.png
tags: 
- CentOS
- Python
- Linux
categories: 经验总结
---

## 安装python3.7和pip

### 下载python源码包

```shell
wget https://www.python.org/ftp/python/3.7.1/Python-3.7.1.tar.xz
```

<!-- more -->

### 安装所需依赖关系

```shell
sudo yum -y groupinstall "Development tools"
sudo yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel libffi-devel
```

### 解压进入相应目录

直接使用zsh的extract。

### 编译安装

```shell
/configure --prefix=/usr/local/python3  
make  
sudo make install
```

### 创建相应软连接命令

```shell
sudo ln -s /usr/local/python3/bin/python3 /usr/bin/python3
sudo ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```

### 升级相应的pip版本

```shell
sudo pip3 install --upgrade pip
```

### 安装python依赖包

```shell
sudo pip3 install -r requestment.txt
```

### python2与python3共存

将`/usr/bin/yum`和`/usr/libexec/urlgrabber-ext-down`文件的最首句最后的python改成python2。

### 可能出现的问题

（1）在执行 make 的时候没有出现问题，但是在 make install 的时候报错： ModuleNotFoundError: No module named ‘_ctypes’

原因：这是因为没有安装上 tk-devel、libffi-devel这两个包，可以重新使用 yum 安装。

（2）SyntaxError: invalid syntax  File "/usr/libexec/urlgrabber-ext-down" 

原因：没有修改/usr/libexec/urlgrabber-ext-down 文件中python的环境为python2，因为现在安装了python3，它不知道应该使用哪一个，所以会报错。

---

## 安装mysql5.7

### 安装YUM Repo

```shell
wget https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
rpm -ivh mysql57-community-release-el7-9.noarch.rpm
```

执行完成后会在`/etc/yum.repos.d/`目录下生成两个repo文件`mysql-community.repo` `mysql-community-source.repo`。

### 安装Mysql

```shell
sudo yum install mysql-server
sudo systemctl start mysqld #启动MySQL
```

### 配置Mysql

获取初始密码

```shell
grep 'temporary password' /var/log/mysqld.log
```

若是获取不到则删除原本的安装过的mysql残留数据。然后在启动mysql。

```shell
rm -rf /var/lib/mysql
```

### 登录

```shell
mysql -u root -p -P 3500 #这里之前设置了mysql的端口开放在3500端口
```

### 修改密码

```shell
set password=password("yourpassword");
flush privileges; #刷新设置好的设置
```

### Mysql状态设置

```shell
systemctl stop mysqld #关闭MySQL
systemctl restart mysqld #重启MySQL
systemctl status mysqld #查看MySQL运行状态
systemctl enable mysqld #设置开机启动
systemctl disable mysqld #关闭开机启动
```

### Mysql开启远程控制

首先创建一个远程访问用的用户身份。

```shell
grant all privileges on 数据库名.表名 to 创建的用户名(root)@"%" identified by "密码"; # 数据库名.表名 如果写成*.*代表授权所有的数据库 

flush privileges; #刷新刚才的内容

#如：
grant all privileges on *.* to root@"113.64.243.1" identified by "123456789";
```

### Mysql配置修改

```shell
vim /etc/my.cnf
#添加
[mysqld]
character_set_server=utf8
init_connect='SET NAMES utf8'
port=3500
```

---

### 配置终端

```shell
sudo yum install zsh git
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
git clone https://github.com/bhilburn/powerlevel9k.git ~/.oh-my-zsh/custom/themes/powerlevel9k
```

修改.zshrc文件

```shell
export TERM="screen-256color"
POWERLEVEL9K_MODE='nerdfont-complete'
ZSH_THEME="powerlevel9k/powerlevel9k"
#POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(context disk_usage dir vcs)
#POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(status root_indicator background_jobs history time)
POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(context os_icon disk_usage dir_writable dir vcs)
POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=(ip status root_indicator background_jobs load battery history date time)
POWERLEVEL9K_PROMPT_ON_NEWLINE=true
POWERLEVEL9K_RPROMPT_ON_NEWLINE=true
POWERLEVEL9K_MULTILINE_FIRST_PROMPT_PREFIX="↱"
POWERLEVEL9K_MULTILINE_LAST_PROMPT_PREFIX="↳ "
plugins=(
  git
  catimg
  encode64
  z
  zsh-autosuggestions
  history
  extract
)
```

---



