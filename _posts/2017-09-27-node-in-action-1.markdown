---
layout:     post
title:      "Node.js in action (1)"
subtitle:   "Node.js介绍、环境部署及资源"
date:       2017-09-27
author:     "Fiona"
header-img: "img/node.png"
tags:
    - Node.js
    - 原创
---



> It's great to write server code with javascript

## 前言  

今天开始系统学习Node.js，本文主要包含三个部分，Node.js介绍，Node.js在Linux平台上环境搭建及Node学习资源。

## Node.js介绍

1. Node.js发明  

    Node创始人Ryan Dahl在2009年柏林JSCONF上第一次介绍Node [视频](https://www.youtube.com/watch?v=ztspvPYybIY) [PPT](http://s3.amazonaws.com/four.livejournal/20091117/jsconf.pdf)  
    ![node-presentation](/blog/img/in-post/post-node-in-action-1/node_presentation.png)

2. Node.js官方定义  

    > 一个搭建在**Chrome JavaScript**运行时上的平台，用于构建**高速、可伸缩**的**网络程序**。Node.js采用的**事件驱动**、**非阻塞I/O模型**，使它既轻量又高效，并成为构建运行在分布式设备上的**数据密集型实时程序**的完美选择。

    * 构建于javascript上  
    一门语言横跨客户端服务器端；原生支持JSON格式；可以操作默写数据库，如MongoDB；紧跟ECMAScript标准。
    * 异步事件触发  
    Node把JavaScript带到服务端的方式跟浏览器把JavaScript带到客户端的方式一样。  
    都是采用事件驱动（用事件轮询方式）和非阻塞的I/O（异步I/O）。Linux C采用的是阻塞I/O,多线程处理。  
    这种差别类似于Nginx和Apache的差别，Nginx 带有异步I/O的事件轮询，能处理更多的请求和客户端连接，响应能力更强；Apache 由于带有阻塞I/O不得不采用多线程方式处理不同的任务，多线程的弊端：线程管理复杂，线程需要消耗额外的系统资源。
    * 为DIRT程序而生(data-intensive real-time)  
    因为Node在I/O上非常轻量，所以设计目标是保证响应能力。为了实现这一目的，Node重新实现了宿主环境（如浏览器）中常用的对象，如setTimeout和console.log,另外还有一组用来处理多种网络和文件I/O的小而且底层的核心模块，例如HTTP、TLS、HTTPS、文件系统（POSIX）、数据报（UDP）和NET（TCP）的模块。
    ![node-in-brief](/blog/img/in-post/post-node-in-action-1/node_in_brief.png)
    
## 在Linux环境上部署Node

Windows环境直接忽略，直接开始在Linux环境上搭建，主要两种方式:  
1. 下载编译好的文件（推荐安装方式，在虚拟机Ubuntu系统上安装成功）  
    简单说就是解压后，bin文件夹中存在node及npm，如果进入到对应文件中执行命令一点问题没有，不过不是全局的。所以设置为全局就可以了。
    <pre>
    wget https://nodejs.org/dist/v6.10.1/node-v6.10.1-linux-x64.tar.xz  // 下载
    xz -d node-v6.10.1-linux-x64.tar.xz // 解压为tar类型
    tar -xvf node-v6.10.1-linux-x64.tar  // -解压
    </pre>

    解压完成后pwd查看当前下载目录，并执行以下命令设置全局：

    <pre>
    ln -s /home/downloads/node-v6.10.1-linux-x64/bin/node /usr/local/bin/node
    ln -s /home/downloads/node-v6.10.1-linux-x64/bin/npm /usr/local/bin/npm
    </pre>
    其中/home/downloads/这个路径是下载nodejs存放的路径
    

2. 通过源码编译安装(云服务器CentOS上安装成功。编译速度极其慢。)
    执行以下命令：
    <pre>
    wget https://nodejs.org/dist/v6.10.1/node-v6.10.1.tar.gz // 该地址为Source Code下载地址
    tar -zxvf node-v6.10.1.tar.gz // 解压下载的Source Code
    </pre>
    解压完成后依次执行：
    <pre> 
    cd node-v6.10.1
    ./configure
    make
    sudo make install  //这里一定要加sudo
    </pre>

## Node学习资源

1. 网站
* [Node.js英文官网](https://nodejs.org/en/)
* [Node.js中文官网](http://nodejs.cn/)
* [Node.js中文社区](https://cnodejs.org/)
* [Express](http://www.expressjs.com.cn/)
* [Koa](http://koajs.com/)

2. 书籍
* 《Node.js实战》(Node.js in action)
* [《Node入门》](https://www.nodebeginner.org/index-zh-cn.html#structure)
* [《7-days-nodejs》](http://nqdeng.github.io/7-days-nodejs/)


