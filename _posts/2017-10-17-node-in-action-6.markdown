---
layout:     post
title:      "Node.js in action (6)"
subtitle:   "Node.js框架之Express"
date:       2017-10-17
author:     "Fiona"
header-img: "img/node.png"
tags:
    - Node.js
    - 原创
---

> Express框架是基于connect构建

## 前言  
Express是一个基于Node.js平台的web应用框架，自身功能极简，完全是由 **路由** 和 **中间件** 构成一个的 web 开发框架：从本质上来说，一个 Express 应用就是在调用各种中间件。对于Express中间件的理解与Connect框架一致。

## Getting Started
### 安装
```shell
npm install express -g
```

### express应用生成器
```shell
$ express -h
$ express -e myapp //使用ejs模板引擎
$ express --pug myapp //使用pug模板引擎
$ express --hbs myapp //使用handlebars模板引擎
```

### 支持的模板引擎
支持任何符合 (path, locals, callback) 接口规范的模板引擎。参考 **[consolidate.js](https://github.com/tj/consolidate.js)**（模板引擎整合库）。常用的有ejs，handlebars，jade, pug。

### 托管静态文件
```javascript
app.use(express.static('public'));
// public 目录下面的文件就可以访问了。
// http://localhost:3000/images/kitten.jpg
// http://localhost:3000/css/style.css
// http://localhost:3000/js/app.js

app.use('/static', express.static('public'));
// 托管到static目录
// http://localhost:3000/static/images/kitten.jpg
// http://localhost:3000/static/css/style.css
// http://localhost:3000/static/js/app.js
```

### 基本路由
基本路由语法：**app.METHOD(PATH, HANDLER)** PATH支持正则匹配。可使用 app.route() 创建路由路径的链式路由句柄。
```shell
// 对网站首页的访问返回 "Hello World!" 字样
app.get('/', function (req, res) {
  res.send('Hello World!');
});

// 网站首页接受 POST 请求
app.post('/', function (req, res) {
  res.send('Got a POST request');
});

// /user 节点接受 PUT 请求
app.put('/user', function (req, res) {
  res.send('Got a PUT request at /user');
});

// /user 节点接受 DELETE 请求
app.delete('/user', function (req, res) {
  res.send('Got a DELETE request at /user');
});
```

### 中间件 
中间件的功能包括
- 执行任何代码，
- 修改请求和响应对象，
- 终结请求-响应循环，
- 调用堆栈中的下一个中间件（next）。

中间件分类
- **应用级中间件**  
  应用级中间件绑定到 app 对象, 使用 app.use() 和 app.method()， 其中， method 是需要处理的 HTTP 请求的方法
```javascript
var app = express()
```

- **路由级中间件**  
  路由级中间件和应用级中间件使用上是一样，只是它绑定的对象为 express.Router()。
```javascript
var router = express.Router()
```

- **错误处理中间件**  
错误处理中间件和其他中间件定义类似，只是要使用 4 个参数，而不是 3 个。与express错误处理中间件一样。
```javascript
app.use(function(err, req, res, next) {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

- **内置中间件**  
express.static 是 Express 唯一内置的中间件,基于[serve-static](https://github.com/expressjs/serve-static)。使用方式见 托管静态文件。

- **第三方中间件**  
通过npm install安装的中间件.可以在应用级加载，也可以在路由级加载。  

```shell
$ npm install cookie-parser
```

```javascript
var express = require('express');
var app = express();
var cookieParser = require('cookie-parser');

// 加载用于解析 cookie 的中间件
app.use(cookieParser());
```
Express 中经常用到的[第三方中间件列表](http://www.expressjs.com.cn/resources/middleware.html)。

### 调试Express
以windows环境为例（Linux环境设置环境变量去掉set即可）
```shell
// 查看 Express 中用到的所有内部日志
$ set DEBUG=express:* & node index.js

// 仅查看路由部分日志
$ set DEBUG=express:router & node index.js

// 仅查看应用部分日志
$ set DEBUG=express:application & node index.js

// 仅查看视图部分日志
$ set DEBUG=express:view & node index.js

// debug命名空间限制在应用中
$ express myapp
$ set DEBUG=myapp node index.js
```

### Process managers for Express apps 

生产环境中，使用进程管理（Process manager）有以下好处：
- App崩溃，会自动重启
- 了解运行时性能和资源消耗
- 自动修改配置以提升性能
- 控制集群(clustering)  

在express和其它Node应用程序中，比较流行的进程管理工具有：
- **StrongLoop Process Manager** [官网](http://strong-pm.io/)  
唯一一个提供了全面的运行时和部署解决方案 ，为Node应用程序生产前后整个生命周期提供工具。
- **PM2**  [Git仓库](https://github.com/foreverjs/forever)  
内置负载均衡 永远保持应用程序 重新加载不会停机 使得系统管理任务变得方便 管理日志 监控 支持集群。  
PM2操作的是app注册时的ID。

```shell
// start
$ pm2 start app.js

// List all running processes:
$ pm2 list

// Stop an app:
$ pm2 stop 0

// Restart an app:
$ pm2 restart 0

// To view detailed information about an app:
$ pm2 show 0

// To remove an app from pm2’s registry:
$ pm2 delete 0

```
- **forever** [Git仓库](https://github.com/foreverjs/forever)  
一个简单的CLI工具，用于确保给定脚本永远执行。适用于部署较小Node应用程序。

```shell
// start background
$ forever start script.js
// start attached to terminal
$ forever script.js

// list
$ forever list

// log
$ forever start -l forever.log -o out.log -e err.log script.js

// stop
$ forever stop 1
$ forever stop script.js
$ forever stopall
```
