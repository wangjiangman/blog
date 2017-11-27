---
layout:     post
title:      "Linux知识整理 (1)"
subtitle:   "mongodb 3.4在CentOS及Ubuntu系统上的安装"
date:       2017-11-09
author:     "Fiona"
header-img: "img/linux-bg.jpg"
tags:
    - Node.js
    - 转载
---

> 原文链接：https://buzheng.org/2017/20170118-install-mongodb-on-ubuntu.html

# CentOS下使用yum安装mongodb 3.4
  [参考链接](https://www.cnblogs.com/acewhl/p/6638486.html)
  
# Ubuntu安装mongodb 3.4
今天参照 mongodb 的[官方文档](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/)在 Unbuntu Server 16.04 上安装了 Mongodb 3.4，步骤很简单，就顺手翻译了一下这个文档，这个文档是讲述了 Mongodb 3.4 在 Ubuntu 12.04, 14.04, 16.04 上的安装步骤。<!--more-->

## 概述

这个教程讲述了在长期支持版 Ubuntu Linux 系统上从 `.deb` 包安装 Mongodb 社区版的步骤。虽然 Ubuntu 软件仓库中已经包含了 MongoDB 的包，但并不是最新的版本。

> **平台支持：**
> MongoDB 提供的包只支持 64 位长期支持版本的 Ubuntu 发行版。比如 Ubuntu 12.04 LTS (precise), 14.04 LTS (trusty), 16.04 LTS (xenial) 等等。这些包可能在其他发行版上也能工作，但是并未被支持。

> **注意事项：**
> 3.4 不兼容 IBM Power Systems 上的 Ubuntu 16.04

## 包

MongoDB 在自己的仓库里提供了官方支持的安装包。仓库中包含了下面的包

| 包  | 功能  |
| ----|------|
| mongodb-org | 这个包会自动安装以下的 4 个组件包 |
| mongodb-org-server | 包含了 `mongod` 守护进程及其相关的配置和初始化脚本 |
| mongodb-org-mongos | 包含了 `mongos` 守护进程 |
| mongodb-org-shell | 包含了 `mongo` 客户端程序 |
| mongodb-org-tools | 包含了一下 MongoDB 工具: `mongoimport bsondump`, `mongodump`, `mongoexport`, `mongofiles`, `mongooplog`, `mongoperf`, `mongorestore`, `mongostat`, `mongotop`.  |


包 mongodb-org-server 提供的初始化脚本来启动 mongod，配置文件为： `/etc/mongod.conf`

这些安装包与 Ubuntu 提供的 `mongodb`, `mongodb-server`, `mongodb-clients` 包冲突。

安装包提供的配置文件 `/etc/mongod.conf` 默认配置 bind_ip 为 127.0.0.1 。在初始化一个[复制集群(replica set)](https://docs.mongodb.com/manual/reference/glossary/#term-replica-set)之前根据你的需要修改这个设置。

## 安装 MongoDB 社区版

MongoDB 提供的包只支持 64 位长期支持版本的 Ubuntu 发行版。比如 Ubuntu 12.04 LTS (precise), 14.04 LTS (trusty), 16.04 LTS (xenial) 等等。这些包可能在其他发行版上也能工作，但是并未被支持。

### 导入包管理系统使用的公钥

Ubuntu 的软件包管理工具（即dpkg和APT）要求软件包的发布者通过GPG密钥签名来确保软件包的一致性和真实性。通过以下命令导入MongoDB公共GPG密钥：

```bash
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```

### 为 MongoDB 创建 list file

根据 Ubuntu 的版本使用适当的命令创建 list file: `/etc/apt/sources.list.d/mongodb-org-3.4.list`

#### Ubuntu 12.04

```bash
echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu precise/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

#### Ubuntu 14.04

```bash
echo "deb [ arch=amd64 ] http://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

#### Ubuntu 16.04

```bash
echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

### 重新下载本地包数据库索引

```bash
sudo apt-get update
```

### 安装 MongoDB

通过以下命令安装最新的可靠版

```bash
sudo apt-get install -y mongodb-org
```

如果下载速度很慢，可以先安装apt-fast
```shell
sudo add-apt-repository ppa:apt-fast/stable
sudo apt-get update
sudo apt-get -y install apt-fast
```
弹出的对话框中选择apt-get。安装完成之后使用方法和apt-get 是一样的。

## 运行 MongoDB 社区版

默认情况下， MongoDB 实例的数据文件位于 `/var/lib/mongodb`，日志文件位于 `/var/log/mongodb`，并且通过用户 mongodb 来运行。你可以在配置文件 `/etc/mongod.conf` 指定不同的日志文件和数据文件目录，其对应的配置为：`systemLog.path` 和  `storage.dbPath` 。

如果你更改了运行 MongoDB 进程的用户，必须修改 `/var/lib/mongodb` 和 `/var/log/mongodb` 的访问权限来让用户能访问这些目录。

### 启动 MongoDB

执行如下命令来启动 `mongod` 进程

```bash
sudo service mongod start
# 或者
sudo systemctl start mongod
```

### 验证 MongoDB 启动成功

通过检查日志文件 /var/log/mongodb/mongod.log 来验证 mongod 进程是否启动成功，日志文件中应包含下面的信息：

```
[initandlisten] waiting for connections on port <port>
```

 `<port> ` 与配置文件 /etc/mongod.conf 的配置一致， 默认值是 27017

### 停止 MongoDB

 如果需要，你可通过下面的命令来停止 mongod 进程

```bash
sudo service mongod stop
# 或者
sudo systemctl stop mongod
```

### 重启 MongoDB 

```bash
sudo service mongod restart
```

### 查看 MongoDB 状态

```bash
sudo service mongod status
# 或者
sudo systemctl status mongod
```

## 卸载 MongoDB 社区版

为了彻底的从系统中移除 MongoDB，你需要移除 MongoDB 应用程序，配置文件，日志和数据文件目录。请参照下面的操作步骤进行：

### 停止 MongoDB

通过以下命令停止 mongod 进程

```bash
sudo service mongod stop
```

### 删除软件包

删除所有的 MongoDB 软件包

```bash
sudo apt-get purge mongodb-org*
```

### 删除数据和日志目录

删除 MongoDB 数据和日志目录

```bash
sudo rm -r /var/log/mongodb
sudo rm -r /var/lib/mongodb
```
