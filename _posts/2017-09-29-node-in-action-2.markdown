---
layout:     post
title:      "Node.js in action (2)"
subtitle:   "Node.js模块化和异步编程"
date:       2017-09-29
author:     "Fiona"
header-img: "img/node.png"
tags:
    - Node.js
    - 原创
---


> 

## 前言  

Node.js模块系统基于[CommonJS规范](http://www.commonjs.org/specs/modules/1.0/),  
Node.js的异步编程方式有：回调、事件发射器和流程控制管理。

## Node.js模块化
### Node模块特点  
Node为了代码重用，会将代码进行打包,且**不会改变全局作用域**。实际上，模块代码在执行之前，Node.js会为其包裹一层类似如下的代码：
```javascript
(function(exports, require, module, __dirname, __filename) {
  // Your module code actually lives in here
})
```

### Node模块的形式  
文件或者目录，若模块是一个目录,Node通常会在这个目录下寻找index.js（可通过当前目录下的package.json文件中的main属性指定重写）文件作为入口。

### Node模块导出和引入语法  
导出模块可以选择暴露的函数、变量和对象。若返回的函数、变量和对象不止一个,可以通过设定**exports**对象属性来指明; 若返回的函数、变量和对象只有一个，则可以设定**module.exports**属性。  
导入模块使用**require**关键字。
> require是Node中少数几个同步I/O操作之一。因为经常用到模块，并且一般都是在文件顶端引入，所以把require做成同步的有助于保持代码的整洁、有序，还能增强可读性。但在程序中I/O密集的地方尽量不要用require。所有同步调用都会阻塞Node，直到调用完成才能做其他事情。比如你正在运行一个HTTP服务器，如果在每个进入的请求上都用了require，就会遇到性能问题。所以通常都只在程序最初加载时才使用require和其他同步操作。

### 用**node_modules**重用模块  
  Node中有一个独特的模块引入机制，可以不必知道模块在文件系统中的具体位置。这个机制就是使用node_modules目录。模块的查找步骤如下图：  
  ![find-node-module](/blog/img/in-post/post-node-in-action-2/find_node_module.png)  

## Node.js异步编程  
### 回调  
回调是一个函数，用来作为参数传递给异步函数。描述了异步操作完成后要做什么。通常用来定义一次性响应的逻辑。  
回调层数太多时，可以把每一次回调做成命名函数形式。也可以用if/else让程序尽早返回。
### 事件发射器  
事件监听本质也是一个回调，只不过跟具体事件相关联。通常使用on/emit对的形式完成事件监听和触发的整个过程：on监听事件，emit触发事件。  
一些重要的Node API组件，比如HTTP服务器、TCP服务器和流，都被做成了事件发射器。

```javascript
  const EventEmitter = require('events');
  const myEmitter = new EventEmitter();

  myEmitter.on('event', () => console.log('a') );
  myEmitter.on('event', () => console.log('b'));

  console.log(EventEmitter.listenerCount(myEmitter, 'event'));
  myEmitter.emit('event'); 
  // Prints:
  //   2
  //   a
  //   b
```
### 流程控制  
让一组异步任务顺序执行的概念被Node社区称为流程控制。分为串行和并行控制。可以自己写代码控制或者使用插件(Nimble、Step
或者Seq)。  

**串行控制的例子：**(按规定的顺序执行异步函数)
```javascript
function asyn1 (data) {
  setTimeout(() => {
    console.log('asyn1 called');
    console.log('get : ' + data);
    next(null, 'data1');
  }, 400);
}

function asyn2 (data) {
  setTimeout(() => {
    console.log('asyn2 called')
    console.log('get : ' + data);
    next(null, 'data2');
  }, 300);
}

function asyn3 (data) {
  setTimeout(() => {
    console.log('asyn3 called')
    console.log('get : ' + data);
    next(null, 'data3');
  }, 200);
}

function asyn4 (data) {
  setTimeout(() => {
    console.log('asyn4 called');
    console.log('get : ' + data);
    next(null, 'data4');
  }, 100);
}

var tasks = [asyn1, asyn2, asyn3, asyn4];

function next(error, result) {
  if (error) throw error;
  var currentTask = tasks.shift();

  if (currentTask) {
    currentTask(result)
  }
}

next();
```

**并行控制的例子：**(并行读取text目录下的文件，并在控制台展示其内容)
```javascript
const fs = require('fs');
const textDir = './text';
let fileNum = 0;
let tasks = [];
fs.readdir(textDir, (err, files) => {
  if(err) throw err;
  for (var index in files) {
    var task = (function(file) {
      return function() {
        fs.readFile(file, (err, data) => {
          console.log(data.toString());
          console.log(fileNum++);
        })
      }
    })(textDir + '/' + files[index]);
    tasks.push(task);
  }
  for (var index in tasks) {
    tasks[index]()
  }
})
```


