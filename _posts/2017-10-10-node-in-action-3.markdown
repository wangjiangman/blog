---
layout:     post
title:      "Node.js in action (3)"
subtitle:   "构建Node Web程序"
date:       2017-10-10
author:     "Fiona"
header-img: "img/node.png"
tags:
    - Node.js
    - 原创
---


> 

## 前言  

Node.js的API相对都比较底层，用于构建Web程序需要用到的核心模块是http(s)，其它常用的模块有fs、url、path、querystring、Buffer等。


## 用Node的API处理http请求

以下例子构建了一个RESTful Web服务，要点：
- 创建http server: createServer
- 获取请求头：回调参数req
- 监听请求到的数据：req.on('data', (trunk) => {});req.on('end', () => {});
- 设置响应头：res.setHeader()
- 设置响应状态：res.statusCode = 200
- 设置响应体：res.end(body)
- 监听的服务器端口：server.listen(port)

```javascript
const http = require('http');
var url = require('url');
var items = [];

var server = http.createServer((req, res) => {
  switch(req.method) {
    case 'POST':
      var item = '';
      req.setEncoding('utf-8');
      req.on('data', function(trunk) {
        item += trunk;
      });
      req.on('end', function() {
        items.push(item);
        res.end('OK\n');
      });
      break;
    case 'GET':
      var body = items.map(function(item, i) {
         return i + ') ' + item + '\n';
      }).join('\n');
      res.setHeader('Content-Length', Buffer.byteLength(body));
      res.setHeader('Content-Type', 'text/plain;charset="utf-8"');
      res.end(body);
      break;
    case 'DELETE':
      var path = url.parse(req.url).pathname;
      var i = parseInt(path.slice(1), 10);

      if (isNaN(i)) {
        res.statusCode = 404;
        res.end('Invalid item id');
      } else if (!items[i]) {
        res.statusCode = 404;
        res.end('Item not found');
      } else {
        items.splice(i, 1);
        res.end('OK\n');
      }
      break;
    case 'PUT':
      var path = url.parse(req.url).pathname;
      var item = '';
      var i = parseInt(path.slice(1), 10);
      req.setEncoding('utf-8');

      req.on('data', function(trunk) {
        item += trunk;
      });
      req.on('end', function() {
        if (isNaN(i)) {
          res.statusCode = 404;
          res.end('Invalid item id');
        } else if (!items[i]) {
          res.statusCode = 404;
          res.end('Item not found');
        } else {
          items.splice(i, 1, item);
          res.end('OK\n');
        }
      });
      break;
    default:
      res.end('unkown method \n');
      break;
  }
});
server.listen(8000);
```

## Node提供静态文件服务

以下例子构建了一个简单的静态文件服务器，要点：
- __dirname指的是该文件所在目录的路径
- 提前检测文件是否存在：fs.stat
- 用管道的方式传输文件：ReadableStream.pipe(WritableStream); 注意案例中：res.end()会在stream.pipe(res)中自动执行
- 在Node中，所有继承了EventEmitter的类都可能会发出error事件。像fs.ReadStream这样的流只是专用的EventEmitter，有预先定义的data和end等事件,见举例注释部分；而默认情况下，如果没有设置监听器，error事件会被抛出。也就是说如果你不监听这些错误，那它们就会搞垮你的服务器。

```javascript
const http = require('http');
const fs = require('fs');
const parse = require('url').parse;
const join = require('path').join;
let root = __dirname;

var server = http.createServer((req, res) => {
  var path = join(root, decodeURI(parse(req.url).pathname));
  fs.stat(path, (err, stat) => {
    if (err) {
      if('ENOENT' == err.code) {
        res.statusCode = 404;
        res.end('File not found!');
      } else {
        res.statusCode = 500;
        res.end("Internal server error1!")
      }
    } else {
      res.setHeader('Content-Length', stat.size);
      var stream = fs.createReadStream(path);
      // stream.on('data', (chunk) => {
      //   res.write(chunk);
      // });
      // stream.on('end', (err) => {
      //   res.end();
      // });
      stream.pipe(res);
      stream.on('error', () => {
        res.statusCode = 500;
        res.end("Internal server error2!");
      })
    }
  })
});

server.listen(8000);
```

## Node处理form表单

以下是一个Node处理表单的简单示例，要点：
- 表单提交请求带的Content-Type值通常有两种：
  - application/x-www-form-urlencoded：这是HTML表单的默认值；
  - multipart/form-data：在表单中含有文件或非ASCII或二进制数据时使用。
- Web程序通常会通过表单收集用户的输入。Node不会承担处理工作（比如验证或文件上传），它只能交出请求主体数据。
- 使用formidable插件处理表单（需要npm install formidable来安装），formidable.IncomingForm()实例的parse方法解析出上传的fields和files，监听progess事件，获取上传进度。

```javascript
var http = require('http');
var formidable = require('formidable');
var items = [];

var server = http.createServer(function(req, res){
  if ('/' == req.url) {
    switch (req.method) {
      case 'GET':
        show(res);
        break;
      case 'POST':
        upload(req, res);
        break;
      default:
        badRequest(res);
    }
  } else {
    notFound(res);
  }
});

server.listen(3000);

function show(res) {
  var html = '<html lang="en"><head><meta charset="UTF-8"><title>Todo List</title></head><body>'
          + '<h1>Upload File</h1>'
          + '<form method="post" action="/" enctype="multipart/form-data">'
          + '<p><label>姓名：</label><input type="text" name="name" /></p>'
          + '<p><label>年龄：</label><input type="text" name="age" /></p>'
          + '<p><label>文件1：</label><input type="file" name="file1" /></p>'
          + '<p><label>文件2：</label><input type="file" name="file2" /></p>'
          + '<p><input type="submit" value="Add Item" /></p>'
          + '</form></body></html>';
  res.setHeader('Content-Type', 'text/html');
  res.setHeader('Content-Length', Buffer.byteLength(html));
  res.end(html);
}

function upload(req, res) {
  if (!isFormData(req)) {
    res.statusCode = 400;
    res.end('Bad Request: expecting multipart/form-data');
    return;
  }

  var form = new formidable.IncomingForm();
  // form.on('field', (field, value) => {
  //   console.log(field);
  //   console.log(value);
  // })
  // form.on('file', (name, file) => {
  //   console.log(name);
  //   console.log(file);
  // })
  // form.on('end', () => {
  //   res.end('upload complete!')
  // })
  form.parse(req, (err, fields, files) => {
    console.log(fields);
    console.log(files);
    res.end('upload complete!')
  });
  form.on('progress', (bytesReceived, bytesExpected) => {
    var percent = Math.floor(bytesReceived/bytesExpected * 100);
    console.log(percent);
  })
}

function isFormData(req) {
  var type = (req.headers['content-type'] || '');
  return 0 == type.indexOf('multipart/form-data');
}

function badRequest(res) {
  res.statusCode = 400;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Bad Request');
}

function notFound(res) {
  res.statusCode = 404;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Not Found');
}
```

## 使用https建立安全的web服务器

安全的超文本传输协议（HTTPS）提供了一种保证Web会话私密性的方法。HTTPS将HTTP和TLS/SSL传输层结合到一起。用HTTPS发送的数据是经过加密的，因此更难窃听。  
在Node程序里使用HTTPS，第一件事就是取得一个私钥和一份证书。私钥本质上是个“秘钥”，可以用它来解密客户端发给服务器的数据。私钥保存在服务器上的一个文件里，放在一个不可信用户无法轻易访问到的地方。  
开发测试过程中可以使用一下命令生成私钥和证书：

```shell
// 需在linux环境下执行
openssl genrsa 1024 > key.pem
openssl req -x509 -new -key key.pem > key-cert.pem
```

例子中所用的证书不是由证书颁发机构颁发的，所以会显示一个警告信息。这里可以忽略这个警告，但如果要把网站部署到公网上，你就应该找个证书颁发机构（CA）进行注册，并为你的服务器取得一份真实的、受信的证书。
```javascript
var https = require('https');
var fs = require('fs');

var options = {
  key: fs.readFileSync('./key.pem'),
  cert: fs.readFileSync('./key-cert.pem')
};

https.createServer(options, (req, res) => {
  res.writeHead(200);
  res.end('hello https!')
}).listen(3000);
```
