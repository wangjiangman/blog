---
layout:     post
title:      "Node.js in action (4)"
subtitle:   "Node.js数据存储"
date:       2017-10-11
author:     "Fiona"
header-img: "img/node.png"
tags:
    - Node.js
    - 原创
---


> 

## 前言  

Node.js数据存储方式有无服务器的数据存储、关系型数据库存储、NoSQL数据库存储。

## 无服务器数据存储

无服务器存储分为使用内存存储和基于文件的存储。
1. 内存存储 就是使用程序中的变量进行存储，读取写入快，服务器和程序重启后数据丢失;
2. 文件存储 这种方式经常用来保存程序的配置信息，但由于会导致并发问题，故不适用于多用户数据存储

以下示例展示了Node的基本文件读写操作：
- process.argv 是一个数组：  
process.argv[0](也可以写作process.argv0)是process.execPath;  
process.argv[1]是当前执行的脚本**文件路径**；  
剩余的元素为其他命令行参数。
- process cwd() 方法返回 Node.js 进程当前工作的**目录**。
- 主要文件读写方法fs.readFile() fs.writeFile()

```javascript
var fs = require('fs');
var path = require('path');
var args = process.argv.splice(2);
var command = args.shift();
var taskDescription = args.join(' ');
var file = path.join(process.cwd(), '/.tasks');

switch(command) {
  case 'list':
    listTasks(file);
    break;

  case 'add':
    addTask(file, taskDescription);
    break;

  default:
    console.log('Usage: ' + process.argv[0]
      + ' list|add [taskDescription]');
}

function loadOrInitializeTaskArray(file, cb) {
  fs.exists(file, function(exists) {
    var tasks = [];
    if (exists) {
      fs.readFile(file, 'utf8', function(err, data) {
        if (err) throw err;
        var data = data.toString();
        var tasks = JSON.parse(data || '[]');
        cb(tasks);
      });
    } else {
      cb([]); 
    }
  });
}

function listTasks(file) {
  loadOrInitializeTaskArray(file, function(tasks) {
    for(var i in tasks) {
      console.log(tasks[i]);
    }
  });
}

function storeTasks(file, tasks) {
  fs.writeFile(file, JSON.stringify(tasks), 'utf8', function(err) {
    if (err) throw err;
    console.log('Saved.');
  });
}

function addTask(file, taskDescription) {
  loadOrInitializeTaskArray(file, function(tasks) {
    tasks.push(taskDescription);
    storeTasks(file, tasks);
  });
}
```

## 关系型数据库存储

关系数据库管理系统（RDBMS）可以存储复杂的信息，并且查询起来很容易。  
关系型数据库有很多种，但开发人员一般会选择开源数据库，主要是因为它们有很好的支持，
好用，而且不用花一分钱。
**MySQL**是最流行的SQL数据库，Node社区对它的支持很好。  
为了让Node能跟MySQL交互，会用到Felix Geisendörfer做的[node-mysql 模块](https://github.
com/felixge/node-mysql),当然需要事先安装：
```shell
npm install mysql
```
node-mysql模块连接到数据库(需要事先开启数据库，并创建一个叫mydb的数据库)：
```javascript
var db = mysql.createConnection({
  host:     '127.0.0.1',
  user:     'myuser',
  password: 'mypassword',
  database: 'mydb'
});
```
node-mysql操作数据库：  

```javascript
db.query('database query statement', function(err) { 
    if (err) throw err;)

// demo
db.query(
  "CREATE TABLE IF NOT EXISTS work (" 
  + "id INT(10) NOT NULL AUTO_INCREMENT, " 
  + "hours DECIMAL(5,2) DEFAULT 0, " 
  + "date DATE, " 
  + "archived INT(1) DEFAULT 0, " 
  + "description LONGTEXT,"
  + "PRIMARY KEY(id))",
  function(err) { 
    if (err) throw err;
    console.log('Server started...');
    server.listen(3000, '127.0.0.1'); 
  }
);
```
## NoSQL数据库存储  
“NoSQL”数据库，即“No SQL” 或“Not Only SQL”, 关系型DBMS为可靠性牺牲了性能，但很多NoSQL数据库把性能放在了第一位。  
两个流行的NoSQL数据库：Redis 和 MongoDB。  
MongoDB数据库把文档存在集合（collection）中。集合中的文档，它们不需要相同的schema，每个文档都可以有不同的schema。 这使得MongoDB比传统的RDBMS更灵活，
因为你不用为预先定义schema而操心。
最成熟、维护最活跃的MongoDB API模块是Christian Amor Kvalheim的[node-mongodb-native](https://github.com/mongodb/node-mongodb-native)。需要事先安装：
```shell
npm install mongodb
```
mongodb插入
```javascript
var mongodb = require('mongodb');
var server = new mongodb.Server('127.0.0.1', 27017, {});
var client = new mongodb.Db('mydatabase', server, {w: 1});

client.open(function(err) {
  if (err) throw err;
  client.collection('test_insert', function(err, collection) {
    if (err) throw err;

    collection.insert(
      {
        "title": "I like cake",
        "body": "It is quite good."
      },
      {safe: true},
      function(err, documents) {
        if (err) throw err;
        console.log('Document ID is: ' + documents[0]._id);
      }
    );
  });
});

```
mongodb更新
```javascript
var mongodb = require('mongodb');
var server = new mongodb.Server('127.0.0.1', 27017, {});
var client = new mongodb.Db('mydatabase', server, {w: 1});

client.open(function(err) {
  if (err) throw err;
  client.collection('test_insert', function(err, collection) {
    if (err) throw err;

    var _id = new client.bson_serializer .ObjectID('4e650d344ac74b5a01000001');
    collection.update(
      {_id: _id},
      {$set: {"title": "I ate too much cake"}},
      {safe: true},
      function(err) {
        if (err) throw err;
      }
    );
  });
});

```


