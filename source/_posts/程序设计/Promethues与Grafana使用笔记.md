---
title: Promethues与Grafana使用笔记
author: 不二
img: >-
  https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/Promethues与Grafana使用笔记.png
mathjax: true
date: 2022-05-05 18:28:03
tags: 
- Promethues
- Grafana
categories: 程序设计
---

## 需求背景

为了避免因为服务器资源或者温度导致的服务不可访问等问题的出现，需要监测各台服务器在一定时间间隔内的运行状态，这样即使出现了问题，也能更好的定位问题发生的原因，进而避免机器本身访问不可达。

## 整体思路

在监控机上安装监控服务，并在受监控机器上安装相应的监控程序，以满足在一台指定的机器上检查所有机器运行状态的问题。

## 难点解决方案

这一整个流程中的方案选型，有两个方案可以采用：

### 探针

探针可以采集机器运行信息，但是相对比较简单。

缺点：往往只能采集到cpu、内存、网络、硬盘的利用率，且数据只能实时展示，不落盘。对于之后问题的定位没啥帮助。

优点：部署简单，没有难度，端口不好设置加密。

### Promethues+Grafana

Promethues是一个成熟的监控开源软件。

优点：能够获取极为详细的运行信息，并且能针对不同的服务以服务的粒度去监控其各项指标。每次监控的数据都会存在相应的主机的时序数据库中，便于之后的问题定位与查找。能设置更好的面板，支持类sql的查询方式。

缺点：因为采集的数据过于详细，频繁的采集可能导致服务本身的能力受到影响。并且其体量相较于探针大得多，部署等工作相对复杂。

### 总结

对于之前的需求而言，并不在乎其实时状态，只需要监控一定时间间隔内的运行状态，并且现在有一个相对空置的机器可以部署Promethues服务。因此选择Promethues+Grafana作为技术方案。

其中，Promethues进行数据收集，Grafana负责数据展示 

## 详细设计

### 安装node_exporter

1. 在从机上安装[node_exporter](https://github.com/prometheus/node_exporter)监控从机的运行状态

   1. 树莓派选择arm7l的下载包或者使用`lscpu `查看cpu信息
   2. 服务器选择相应的版本下载

2. 将下载下来的文件解压后，将二进制文件放在`/usr/local/bin`文件下，注意有没有可执行权限，主要是其他用户有没有执行权限。因为之后会注册成系统服务，会使用名为`node_exporter`用户启动

3. 创建`node_exporter`用户，并授予权限

      ```shell
      sudo groupadd --system node_exporter
      sudo useradd -s /sbin/nologin --system -g prometheus node_exporter
      ```
      
4. 创建node_exporter运行时的配置文件，一般放在`/etc/node_exporter/config.yml`

      1. 这个文件默认没有需要自己去创建

      2. httpwd生成密码：`htpasswd -nBC 12 '' | tr -d ':\n'`

      3. ```yaml
            basic_auth_users:
              用户名: httpwd生成的密码
            ```

5. 编写系统服务

      1. 创建`/etc/systemd/system/node_exporter.service`文件

      2. 写入如下内容

            ```shell
            [Unit]
            Description=Node Exporter
            Wants=network-online.target
            After=network-online.target
            
            [Service]
            User=node_exporter
            Group=node_exporter
            Type=simple
            ExecStart=/usr/local/bin/node_exporter --web.config=/etc/node_exporter/config.yml
            
            [Install]
            WantedBy=multi-user.target
            ```

6. `sudo systemctl daemon-reload`刷新daemon服务

7. 此时可以使用`sudo systemctl start node_exporter`等命令控制node_exporter程序的运行

8. node_exporter默认运行在9100端口，开启9100端口，并查看该端口下是否有正常输出

9. 如果确认没有问题则可以开机自启动服务`sudo systemctl enable node_exporter`

### 安装Promethues

1. 创建相应用户

   ```shell
   sudo groupadd --system prometheus
   sudo useradd -s /sbin/nologin --system -g prometheus prometheus
   ```

2. 创建配置目录

   ```shell
   sudo mkdir /var/lib/prometheus
   for i in rules rules.d files_sd; do sudo mkdir -p /etc/prometheus/${i}; done
   ```

3. 下载Promethues

   ```shell
   mkdir -p /tmp/prometheus && cd /tmp/prometheus
   curl -s https://api.github.com/repos/prometheus/prometheus/releases/latest \
     | grep browser_download_url \
     | grep linux-amd64 \
     | cut -d '"' -f 4 \
     | wget -qi -
     
   tar xvf prometheus*.tar.gz
   cd prometheus*/
   mv prometheus promtool /usr/local/bin/
   mv prometheus.yml  /etc/prometheus/prometheus.yml
   mv consoles/ console_libraries/ /etc/prometheus/
   
   cd ~/
   rm -rf /tmp/prometheus
   ```
   
3. 创建系统服务

   ```shell
   sudo tee /etc/systemd/system/prometheus.service<<EOF
   
   [Unit]
   Description=Prometheus
   Documentation=https://prometheus.io/docs/introduction/overview/
   Wants=network-online.target
   After=network-online.target
   
   [Service]
   Type=simple
   User=prometheus
   Group=prometheus
   ExecReload=/bin/kill -HUP $MAINPID
   ExecStart=/usr/local/bin/prometheus \
     --config.file=/etc/prometheus/prometheus.yml \
     --storage.tsdb.path=/var/lib/prometheus \
     --web.console.templates=/etc/prometheus/consoles \
     --web.console.libraries=/etc/prometheus/console_libraries \
     --web.listen-address=0.0.0.0:9090 \
     --web.external-url=
   
   SyslogIdentifier=prometheus
   Restart=always
   
   [Install]
   WantedBy=multi-user.target
   EOF
   ```
   
3. 配置相应权限

   ```shell
   for i in rules rules.d files_sd; do sudo chown -R prometheus:prometheus /etc/prometheus/${i}; done
   for i in rules rules.d files_sd; do sudo chmod -R 775 /etc/prometheus/${i}; done
   sudo chown -R prometheus:prometheus /var/lib/prometheus/
   ```
   
6. 配置节点job

   1. 在Promethues中的`scrape_configs`下添加配置

        ```yam
           - job_name: "raspberry pi"
             basic_auth:
               username: <name>
               password: <password>
             static_configs:
               - targets: ["localhost:9100"]
        ```

7. 启动

   ```shell
   sudo systemctl daemon-reload
   sudo systemctl start prometheus
   sudo systemctl enable prometheus
   ```

### 安装Grafana

参考[官方教程](https://grafana.com/grafana/download?platform=linux)

```shell
sudo yum install grafana-enterprise
sudo systemctl enable --now grafana-server
systemctl status grafana-server.service
```

默认端口是3000，用户名和密码都是admin。

通过yum安装的Grafana的配置文件在`/etc/grafana/grafana.ini`中，修改端口就是改成如下

```yaml
# The http port  to use
http_port = 8028
```

删掉前面的分号，分号在其中表示注释。

重启Grafana即可。

在Grafana中首先创建Data Source，之后去import面板，可以用1860或者8919。

## 参考文献

1. [使用 Prometheus 和 Grafana 监控 Proxmox 服务器](https://einverne.github.io/post/2020/05/use-prometheus-and-grafana-monitor-proxmox.html)，2020
2. 简书，[让普罗米修斯替我监测树莓派集群](https://www.jianshu.com/p/821bf7d2bbef)，2020
