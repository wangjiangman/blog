---
layout:     post
title:      "移动web常见方案汇总"
subtitle:   ""
date:       2017-12-28
author:     "Fiona"
header-img: "img/linux-bg.jpg"
tags:
    - mobile
    - web
    - 原创
---

> late is better than never

### 字体相关自适应方案
#### 字体设置最佳实践
[参考链接](https://github.com/AlloyTeam/Mars/blob/master/solutions/font-family.md)
```javascript
body {
    font-family: -apple-system, BlinkMacSystemFont, "PingFang SC","Helvetica Neue",STHeiti,"Microsoft Yahei",Tahoma,Simsun,sans-serif;
}
```

#### rem布局
接触过的UI设计稿单位都是px，有750px和375px两种，都是按设计稿量宽度。  
以下是阿里团队的高清方案布局代码，所谓高清方案就是根据设备屏幕的DPR（设备像素比，又称DPPX，比如dpr=2时，表示1个CSS像素由4个物理像素点组成） 动态设置 html 的font-size, 同时根据设备DPR调整页面的缩放值，进而达到高清效果。[参考链接](http://blog.csdn.net/xuhan21000/article/details/67638159)
方案代码：
```javascript
<script>!function(e){function t(a){if(i[a])return i[a].exports;var n=i[a]={exports:{},id:a,loaded:!1};return e[a].call(n.exports,n,n.exports,t),n.loaded=!0,n.exports}var i={};return t.m=e,t.c=i,t.p="",t(0)}([function(e,t){"use strict";Object.defineProperty(t,"__esModule",{value:!0});var i=window;t["default"]=i.flex=function(e,t){var a=e||100,n=t||1,r=i.document,o=navigator.userAgent,d=o.match(/Android[\S\s]+AppleWebkit\/(\d{3})/i),l=o.match(/U3\/((\d+|\.){5,})/i),c=l&&parseInt(l[1].split(".").join(""),10)>=80,p=navigator.appVersion.match(/(iphone|ipad|ipod)/gi),s=i.devicePixelRatio||1;p||d&&d[1]>534||c||(s=1);var u=1/s,m=r.querySelector('meta[name="viewport"]');m||(m=r.createElement("meta"),m.setAttribute("name","viewport"),r.head.appendChild(m)),m.setAttribute("content","width=device-width,user-scalable=no,initial-scale="+u+",maximum-scale="+u+",minimum-scale="+u),r.documentElement.style.fontSize=a/2*s*n+"px"},e.exports=t["default"]}]);  flex(100, 1);</script>
```
> 其中flex(100, 1)对应dpr=2的情况，若效果图对应设备dpr=3,flex(100, 1)改为flex(66.67, 1)

#### flex布局
[2017年我们是否还有必要使用rem](https://lwdgit.github.io/#!/blog/post/2017-08-27-2017%E5%B9%B4%E6%88%91%E4%BB%AC%E6%98%AF%E5%90%A6%E8%BF%98%E6%9C%89%E5%BF%85%E8%A6%81%E4%BD%BF%E7%94%A8rem.md)  
flex在移动端兼容性越来越好。
![java-javascript](/blog/img/in-post/post-mobile-web-summary/mobile-flex.png)
flex的cheetsheet见下图，详见[flex入门](https://juejin.im/post/58e3a5a0a0bb9f0069fc16bb)
![java-javascript](/blog/img/in-post/post-mobile-web-summary/flex.webp)


