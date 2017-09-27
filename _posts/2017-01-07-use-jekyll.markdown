---
layout:     post
title:      "jekyll本地调试环境搭建"
subtitle:   ""
date:       2017-01-07 12:00:00
author:     "Fiona"
header-img: "img/post-bg-2015.jpg"
tags:
    - 环境搭建
    - 原创
---

> “跟着我左手右手一个慢动作~ ”

背景
------------

使用jekyll在github上建立个人博客有段时间了，为了减少向github服务器上传代码的次数，决定在本地搭建调试服务器。

安装ruby
------------

1、下载地址：[http://rubyinstaller.org/downloads](http://rubyinstaller.org/downloads) (安装了最新版本)
<br />
2、安装过程中可以勾选加入本地path环境变量中，也可以安装后自行设置环境变量
<br />
3、安装到D: \Ruby23

安装ruby dev-kit
------------

1、下载地址同[ruby下载地址](http://rubyinstaller.org/downloads) **注意版本要与ruby版本对应**
<br />
2、安装到D:\rubydevkit
<pre><code>cd D:\rubydevkit
ruby dk.rb init
ruby dk.rb install
</code></pre>
3、**ruby dk.rb init**会创建一个config.yml文件，请在文件内部配置好ruby安装目录。
<pre><code># Example:
#
# ---
# - C:/ruby19trunk
# - C:/ruby192dev
#
---
- D:/Ruby23
</code></pre>
4、更改gem镜像到 taobao网，可以改善国内Ruby安装的速度
<pre><code>gem sources --remove https://rubygems.org/
gem sources -a https://ruby.taobao.org/
gem sources -l         #查看是否只有taobao镜像
gem update --system    #更新RubyGems软件
</code></pre>
可能会不稳定，所以我仍然用默认的gem镜像
<pre><code>D:\rubydevkit>gem sources
*** CURRENT SOURCES ***

http://rubygems.org/

</code></pre>

安装jekyll
------------

<pre><code>gem install jekyll
</code></pre>

博客本地跑起来
------------

<pre><code>jekyll server
</code></pre>

遇到的问题
------------

- Deprecation: The 'gems' configuration option has been renamed to 'plugins'. Please update your config file accordingly.  
	解决：  
	配置文件_config.yml中，使用了 plugins 的配置项，应该是用plugins替换掉gems
- 插件（如：jekyll-paginate）没有安装问题  
  解决：  
  <pre><code>gem install jekyll-paginate</code></pre>
