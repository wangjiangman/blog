---
layout:     post
title:      "Node.js in action (5)"
subtitle:   "Node.js框架之connect"
date:       2017-10-16
author:     "Fiona"
header-img: "img/node.png"
tags:
    - Node.js
    - 翻译
---

> 本文大部分翻译自：https://github.com/senchalabs/connect

## 前言  
connect是[TJ Holowaychuk](https://github.com/tj)给node.js社区贡献的一个热门的web基础框架。TJ Holowaychuk的另一力作express框架便是在它基础之上构建的。与express不同，connect更加短小精悍，是一个偏向基础设施的框架。  
正如名字所表达的一样，connect框架做的事情很简单，就是把一系列的组件连接到一起，然后让http的请求依次流过这些组件。这些被connect串联起来的组件被称为中间件（middlewire）。在connect中，http请求的处理流程被划分成一个个小片段，每一个小片段代表一项处理任务（如：请求body的解析，session的维护等），由一个中间件负责，前后片段之间靠request，response等对象传递中间数据。connect框架对这些处理细节并不关心，只知道将请求从一个中间件导向下一个中间件。
```javascript
var connect = require('connect');
var http = require('http');

var app = connect();

// gzip/deflate outgoing responses
var compression = require('compression');
app.use(compression());

// store session state in browser cookie
var cookieSession = require('cookie-session');
app.use(cookieSession({
    keys: ['secret1', 'secret2']
}));

// parse urlencoded request bodies into req.body
var bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({extended: false}));

// respond to all requests
app.use(function(req, res){
  res.end('Hello from Connect!\n');
});

//create node.js http server and listen on port
http.createServer(app).listen(3000);
```

## Getting Started
Connect是一个简单的框架，可以将各种“中间件”粘合在一起来处理请求。

### 安装connect
```shell
$ npm install connect
```

### 创建APP
主要组件是Connect "app",它将存储所有添加的中间件，且其本身就是一个函数。
```javascript
var app = connect();
```

### 使用中间件
Connect的核心是"use"中间件。中间件会被加入堆栈，传入的请求会逐个执行每个中间件直到某个中间件中没有调用next().

```javascript
app.use(function middleware1(req, res, next) {
  // middleware 1
  next();
});
app.use(function middleware2(req, res, next) {
  // middleware 2
  next();
});
app.use(function middleware2(req, res, next) {
  // middleware 3
  // 不调用next(),这个中间件将成为最后被执行的中间件
  // next();
});
```

### 挂载中间件
.use（）方法提供了与传入请求URL开头匹配的可选路径字符串。 这让基本路由成为可能。
```javascript
app.use('/foo', function fooMiddleware(req, res, next) {
  // req.url starts with "/foo"
  next();
});
app.use('/bar', function barMiddleware(req, res, next) {
  // req.url starts with "/bar"
  next();
});
```

### 错误处理中间件
有特殊的"异常处理"中间件。这些中间件需要4个参数。前面比正常处理函数多带一个err参数。通过传递给next传递一个err参数，告诉框架当前中间件处理出现异常。如果err为空，则会按顺序执行后面正常处理函数，忽略异常处理函数;相反，如果err非空，则会按顺序执行后续的异常处理函数，而忽略正常处理函数。

```javascript
// 正常处理函数
app.use(function (req, res, next) {
  // i had an error
  next(new Error('boom!'));
});

// 异常处理函数
app.use(function onerror(err, req, res, next) {
  // an error occurred!
});
```

### 使用connect()创建服务器
最后一步就是要使用Connect app创建一个server。.listen()方法是启动HTTP server的一个便捷的方法(且与Node.js核心API中的http.Server的listen方法是一样的)
```javascript
var server = app.listen(port);
```
app本身是只有三个参数的函数，所以它也可以传递给Node.js中的.createServer()。
```javascript
var server = http.createServer(app);
```

## Middleware
以下中间件和库是Connect/Express团队官方支持的
  - [body-parser](https://www.npmjs.com/package/body-parser) - previous `bodyParser`, `json`, 和 `urlencoded`. 你还可能对以下中间件感兴趣:
    - [body](https://www.npmjs.com/package/body)
    - [co-body](https://www.npmjs.com/package/co-body)
    - [raw-body](https://www.npmjs.com/package/raw-body)
  - [compression](https://www.npmjs.com/package/compression) - previously `compress`
  - [connect-timeout](https://www.npmjs.com/package/connect-timeout) - previously `timeout`
  - [cookie-parser](https://www.npmjs.com/package/cookie-parser) - previously `cookieParser`
  - [cookie-session](https://www.npmjs.com/package/cookie-session) - previously `cookieSession`
  - [csurf](https://www.npmjs.com/package/csurf) - previously `csrf`
  - [errorhandler](https://www.npmjs.com/package/errorhandler) - previously `error-handler`
  - [express-session](https://www.npmjs.com/package/express-session) - previously `session`
  - [method-override](https://www.npmjs.com/package/method-override) - previously `method-override`
  - [morgan](https://www.npmjs.com/package/morgan) - previously `logger`
  - [response-time](https://www.npmjs.com/package/response-time) - previously `response-time`
  - [serve-favicon](https://www.npmjs.com/package/serve-favicon) - previously `favicon`
  - [serve-index](https://www.npmjs.com/package/serve-index) - previously `directory`
  - [serve-static](https://www.npmjs.com/package/serve-static) - previously `static`
  - [vhost](https://www.npmjs.com/package/vhost) - previously `vhost`

大多数的中间件接口与Connect 2.x相同。`cookie-session`是一个例外。
一些曾经官方支持的中间件将会有一些替代的模块，或者被一些更好的模块取代。使用以下替代模块：

  - `cookieParser`
    - [cookies](https://www.npmjs.com/package/cookies) and [keygrip](https://www.npmjs.com/package/keygrip)
  - `limit`
    - [raw-body](https://www.npmjs.com/package/raw-body)
  - `multipart`
    - [connect-multiparty](https://www.npmjs.com/package/connect-multiparty)
    - [connect-busboy](https://www.npmjs.com/package/connect-busboy)
  - `query`
    - [qs](https://www.npmjs.com/package/qs)
  - `staticCache`
    - [st](https://www.npmjs.com/package/st)
    - [connect-static](https://www.npmjs.com/package/connect-static)

查看 [http-framework](https://github.com/Raynos/http-framework/wiki/Modules) 获取更多可兼容的中间件!

## API

Connect API非常简单，足以创建应用程序并添加链接的中间件。  
当需要`connect`模块时，会返回一个将会构造一个新的应用程序的函数以备调用。

```js
// require module
var connect = require('connect')

// create app
var app = connect()
```

### app(req, res[, next])
'app'本身是一个函数，它只是app.handle的别名。

### app.handle(req, res[, out])

调用该函数将根据给定的Node.js http请求（`req`）和响应（`res`）对象运行中间件堆栈。 可以提供一个可选的函数`out`，如果请求（或错误）未被中间件堆栈处理，则可以调用该函数。

### app.listen([...])

启动应用程序监听请求。 此方法将在内部创建一个Node.js HTTP服务器并调用.listen()。

这是Node.js版本中server.listen()方法的别名，因此请参考Node.js文档以获取所有不同的变体。 最常见的签名是
[`app.listen(port)`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_server_listen_port_hostname_backlog_callback).

### app.use(fn)
在"app"中使用一个函数，函数代表一个中间件。将按照“app.use”被调用的顺序为每个请求调用该函数。 该函数包含三个参数：

```js
app.use(function (req, res, next) {
  // req is the Node.js http request object
  // res is the Node.js http response object
  // next is a function to call to invoke the next middleware
})
```

除了计划功能之外，`fn`参数也可以是一个Node.js HTTP服务器实例或另一个Connect应用程序实例。

### app.use(route, fn)

在应用程序上使用fn，其中fn表示中间件。 对于URL（“req.url”属性）以“app.use”被调用的顺序以给定的“route”字符串开始的每个请求将调用该函数。 该函数有三个参数：

```js
app.use('/foo', function (req, res, next) {
  // req is the Node.js http request object
  // res is the Node.js http response object
  // next is a function to call to invoke the next middleware
})
```

除了计划功能之外，`fn`参数也可以是一个Node.js HTTP服务器实例或另一个Connect应用程序实例。

`route`总是在路径分隔符（`/`）或点（`.`）字符处终止。
这意味着给定的路由`/ foo /`和`/ foo`是相同的，并且都将匹配请求与URL`/ foo`，`/ foo /`，`/ foo / bar`和`/foo.bar `，但与URL`/ foobar`的请求不匹配。

`route`匹配不区分大小写。

为了使中间件更易于写入，不需要`route`，当`fn`被调用时，`req.url`将被改变以删除`route`部分（原来的可用`req.originalUrl`）。 例如，如果在路由`/ foo`使用`fn`，`/ foo / bar`的请求将使用`req.url ==='/ bar'`和`req.originalUrl = =='/ foo / bar'`。

## Node兼容性

  - Connect `< 1.x` - node `0.2`
  - Connect `1.x` - node `0.4`
  - Connect `< 2.8` - node `0.6`
  - Connect `>= 2.8 < 3` - node `0.8`
  - Connect `>= 3` - node `0.10`, `0.12`, `4.x`, `5.x`, `6.x`, `7.x`, `8.x`; io.js `1.x`, `2.x`, `3.x`

## 参考
 - [connect框架git仓库](https://github.com/senchalabs/connect)
 - [connect源码分析-基础架构](https://cnodejs.org/topic/4fb79b0e06f43b56112b292c)