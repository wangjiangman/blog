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
Koa基于ES6的Promise和Generator

## 安装
node >=7.6.0 支持ES2015和async函数
```shell
npm install koa --save
node my-koa-app.js
```

## Hello world
```javascript
const Koa = require('koa');
const app = new Koa();

app.use(async (ctx, next) => {
  ctx.body = 'Koa';
  console.log('step1: 1');
  await next();
  console.log('step1: 2');
});

app.use(async (ctx, next) => {
  console.log('step2: 1');
  await next();
  console.log('step2: 2');
});

app.use(async (ctx, next) => {
  console.log('step3');
});

app.listen(3000);
// step1: 1
// step2: 1
// step3
// step2: 2
// step1: 2
```

## ES6之Promise
Promise是异步编程解决方案之一。语法上Promise是一个对象。特点是可以将异步操作以同步操作的流程表达出来，避免层层调用回调函数，对外提供统一的API。
```javascript
var promise = new Promise((resolve, reject) => {
  if (/* async operation success */) {
    resolve();
  } else {
    reject();
  }
})

promise.then(function() {
  // success
}, function(error) {
  // failure
})
```

