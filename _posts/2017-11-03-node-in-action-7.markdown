---
layout:     post
title:      "Node.js in action (7)"
subtitle:   "初始Node.js框架Koa"
date:       2017-11-03
author:     "Fiona"
header-img: "img/node.png"
tags:
    - Node.js
    - 原创
---

> Koa是更轻量、更富有表现力、更健壮的Node.js的web框架。

## 前言  
[Koa](http://koajs.com)应用是一个包含一系列中间件 **generator** 函数的对象。这些中间件函数基于 request 请求以一个类似于 **栈** 的结构组成并依次执行。核心思想：为中间件提供高级语法糖。Koa不绑定任何中间件。

## 安装
node >=7.6.0 支持ES2015和async函数
```shell
npm install koa --save
node my-koa-app.js
```