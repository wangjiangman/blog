---
layout:     post
title:      "Linux知识整理"
subtitle:   "CentOS在虚拟机中的安装、基本配置和基础软件安装"
date:       2017-11-13
author:     "Fiona"
header-img: "img/node.png"
tags:
    - Linux
    - CentOS
    - 原创
---

> 重拾Linux知识 !

## 在VMware上安装CentOS

在 **VMware® Workstation 12 Pro** 上安装 **CentOS 7**, 此处省略一千字...

## 一些开机配置

### 开机界面配置

CentOS默认开机启动图形界面，设置开机启动命令行模式。两种模式分别为：
- 命令行 multi-user.target
- 图形界面 graphical.target

```shell
[root@localhost fiona]# systemctl get-default
[root@localhost fiona]# systemctl set-default multi-user.target
```

### 开机网络配置

CentOS默认关闭wired,设置为开机开启。  

发现CentOS 7开机默认网卡名称是eno16777736,改为eth0（以下步骤皆为root权限）。
```shell
#step1. DEVICE=改为eth0
[root@localhost fiona]# vi /etc/sysconfig/network-scripts/ifcfg-eno16777728

#step2.更改网卡配置文件名称
[root@localhost fiona]# mv ifcfg-eno16777728 ifcfg-eth0

#step3.因 CentOS7 采用 grub2 引导，还需要对 grub2 进行修改
# 在GRUB_CMDLINE_LINUX 这个参数后面加入 net.ifnames=0 biosdevname=0
# GRUB_CMDLINE_LINUX="crashkernel=auto rhgb quiet net.ifnames=0 biosdevname=0"
[root@localhost fiona]# vi /etc/default/grub

#step4.用 grub2-mkconfig 命令重新生成GRUB配置并更新内核
[root@localhost fiona]# grub2-mkconfig -o /boot/grub2/grub.cfg

#step5.重启
[root@localhost fiona]# reboot
```

回到正题，开机开启wired网络：
```shell
# 将ONBOOT的值改为yes即可
[root@localhost fiona]# vim /etc/sysconfig/network-scripts/ifcfg-eth0 
```

### 关闭firewall防火墙（另外注意是否有iptables防火墙）
```shell
# 查看防火墙状态
[root@localhost demo]# firewall-cmd --state
# 关闭防火墙
[root@localhost demo]# systemctl stop firewalld.service
# 关闭开机启动
[root@localhost demo]# systemctl disable firewalld.service
```

### 新增环境变量
要永久设置环境变量（重启后仍然保存）
```shell
# 在该文件底部新增：
# export NODE_HOME="/home/fiona/node/node-v8.9.1-linux-x64"
# export PATH="$NODE_HOME/bin:$PATH"
[root@localhost demo]# vi /etc/profile
```

## CentOS中nginx安装
  [Centos下 Nginx安装与配置](http://www.jianshu.com/p/d5114a2a2052)  
  注意更改下载的版本。

## CentOS中wget、rpm、yum区别和联系
- **wget**  
  一种下载工具
- **rpm**  
  全称redhat pakage management,用于安装卸载.rpm软件
- **yum**  
  是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器基于RPM包管理，能够从指定的服务器自动下载rpm包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包。

果断使用yum有木有! yum下载太慢的解决方法：
```shell
# step1. 备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

# step2. 下载新的CentOS-Base.repo 到/etc/yum.repos.d/
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

// step3. 生成缓存
yum makecache
```
