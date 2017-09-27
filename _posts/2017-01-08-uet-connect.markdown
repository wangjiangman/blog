---
layout:     post
title:      "联教互联JS-SDK的实现"
subtitle:   ""
date:       2017-01-08 12:00:00
author:     "Fiona"
header-img: "img/post-bg-2015.jpg"
tags:
    - 前端实战
    - 原创
---

## 一、移动端H5应用授权流程

采用简化模式（implicit grant type）
![java-javascript](/blog/img/in-post/post-uet-connect/brief.png)

## 二、JS-SDK实现目标

联教互联JS-SDK需要实现以下流程：
> * 第三方H5应用选择联教互联方式登录
> * JS-SDK从联教客户端拉取原生授权页
> * 联教客户端与认证服务器经过两次认证成功后，重定向到第三方H5设置的回调页
> * JS-SDK从回调的URL中获取到access_token和openId，如此，第三方H5应用才能成功获取到联教用户数据。


## 三、接口说明

### 1、第三方H5应用选择联教互联方式登录应用

请求页面引入以下script标签，其中 **APPID** 和 **REDIRECT_URI** 请替换为实际的 appid 和 redirect_uri 。

    <script type="text/javascript" src="../src/ue-connect.js" data-appid="APPID" data-redirecturi="REDIRECT_URI"></script>

然后调用uec.login();

### 2、JS-SDK向客户端提供appid和redirect_uri
参数分别为appId， redirectURI， scope。其中scope为预留的授权列表。

    jumpToAuthPage(appId, redirectURI, scope);
    
**请求参数**

参数|必选|描述
---|---|---
appId|是|第三方配置的appid
redirectURI|是|第三方配置的回调页地址
scope|否|表示授权列表，预留

### 3、重定向到回调页
回调页面需要引入以下script标签

    <script type="text/javascript" src="../src/ue-connect.js" charset="utf-8" data-callback="true"></script>

第三方H5应用可以通过调用接口uec.api(param1, param2,param3)，获取用户数据。

**请求参数**

参数|必 选|描述
---|---|---
param1| 是 |获取接口名称
param2| 是 |自定义参数。预留。默认传{}。JS-SDK补充参数appid，access_token,openid发给认证服务器认证，无需第三方传入。
param3| 是 |获取数据方式，目前皆为'get'方式

**示例**

```
uec.api('get_user_info', {}, 'get').success(function(response) {
	console.log(Object.keys(response));
	console.log('status: ' + response.status);
	console.log('message: ' + response.message);
	console.log('username: ' + response.result.username);
});
```



## 四、参考资料

- [联教互联设计-移动端H5应用授权流程](https://www.zybuluo.com/qiuzj/note/559722)
- [JS SDK登录Demo](http://connect.qq.com/intro/login/jssdk-demo)
- [QQ互联 网站接入流程和OAuth2.0开发说明文档](http://wiki.connect.qq.com/%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C_oauth2-0)
- [QQ互联 JS-SDK源码](http://qzonestyle.gtimg.cn/qzone/openapi/qc-1.0.1.js)





