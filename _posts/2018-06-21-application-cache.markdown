---
layout:     post
title:      "HTML5应用程序缓存"
subtitle:   "Application Cache"
date:       2018-06-21
author:     "Fiona"
header-img: "img/blockchain.jpg"
tags:
    - web
    - 转载
---

> 使用HTML5，通过创建 manifest 文件，可以轻松地创建 web 应用的离线版本。

### Application Cache简介
根据[MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Using_the_application_cache)文档，该特性已经从 Web 标准中删除。推荐使用[Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers)代替，但是目前Service Workers 仍然有[兼容性问题](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers#Browser_compatibility)，所以应用程序缓存（ApplicationCache）仍然是目前进行离线存储的最好的方式。

### 应用程序缓存的优点
HTML5 提供一种应用程序缓存机制，使得基于web的应用程序可以离线运行。开发者可以使用 Application Cache (AppCache) 接口设定浏览器应该缓存的资源并使得离线用户可用。 在处于离线状态时，即使用户点击刷新按钮，应用也能正常加载与工作。 
- 离线浏览: 用户可以在离线状态下浏览网站内容。 
- 更快的速度: 因为数据被存储在本地，所以速度会更快。 
- 减轻服务器的负载: 浏览器只会下载在服务器上发生改变的资源。

### 实例
index.html
```html
<html manifest="cache.appcache"> 
  ...
</html>
```
cache.appcache
```javascript
CACHE MANIFEST
#v1.0.18

/xxx.js
/js/jquery.min.js
/xxxx.js
/xxxxx.js

NETWORK:
*

FALLBACK:
404.html
```
> 注：  
> 1.缓存清单文件可以使用任意扩展名，但传输它的 MIME 类型必须为 text/cache-manifest。  
> 2.manifest 文件的建议的文件扩展名是：”.appcache”.   
> 3.在缓存清单文件中列出的所有记录必须拥有相同的协议、主机名与端口号。  
> 4.不要在清单文件中指定清单文件本身，否则将无法让浏览器得知清单文件有新版本出现。

### 应用程序缓存appcache文件配置
manifest 文件是简单的文本文件，它告知浏览器被缓存的内容（以及不缓存的内容）。   
manifest 文件可分为三个部分：   
CACHE MANIFEST - 在此标题下列出的文件将在首次下载后进行缓存。   
NETWORK - 在此标题下列出的文件需要与服务器的连接，且不会被缓存。可以使用通配符。  
FALLBACK - 在此标题下列出的文件规定当页面无法访问时的回退页面（比如 404 页面）  

> 注：  
1.缓存清单文件的第一行必须包含字符串 CACHE MANIFEST，本行的其他文本会被忽略。   
2.注释只能在所在行起作用，不能追加到其他行上。  
3.CACHE， NETWORK， 和 FALLBACK 段落可以以任意顺序出现在缓存清单文件中，并且每个段落可以在同一清单文件中出现多次。而且段落允许为空。  
4.段落标题指定了缓存文件即将操作的段落。有三个可选的标题: CACHE， NETWORK， 和 FALLBACK   
5.段落标题所在的行可以包含空白字符，段落名后的冒号 (:) 不可省略。       
> 1. 在默认 (CACHE:) 段落，每行都是一个合法的 URI 或 IRI ，与一个要缓存的资源相关联(本段落内不允许通配符)。   
> 2. 在 Network 段落内，每行都是一个合法的 URI 或 IRI，关联一个需要通过网络获取的资源(本段落内可以使用通配符 *)。   
> 3. 在 Fallback 段落内，每行都是一个合法的 URI 或 IRI(与一个资源关联)，紧跟着一个后备资源，用于当无法与服务器建立连接时访问。  
> 4. 相对 URI 是指相对于缓存清单的 URI，而不是包含清单的文档的 URI。

### 使用manifest后加载过程
application cache的使用会修改文档的加载过程： 
1. 如果应用缓存存在，浏览器直接从缓存中加载文档与相关资源，不会访问网络。这会提升文档加载速度。   
2. 浏览器检查清单文件列出的资源是否在服务器上被修改。   
3. 如果清单文件被更新了, 浏览器会下载新的清单文件和相关的资源。 这都是在后台执行的，基本不会影响到webapp的性能。  

下面详细描述了加载文档与更新应用缓存的流程：  
1. 当浏览器访问一个包含 manifest 特性的文档时，如果应用缓存不存在，浏览器会加载文档，然后获取所有在清单文件中列出的文件，生成应用缓存的第一个版本。 
2. 对该文档的后续访问会使浏览器直接从应用缓存(而不是服务器)中加载文档与其他在清单文件中列出的资源。此外，浏览器还会向 window.applicationCache 对象发送一个 checking 事件，在遵循合适的 HTTP 缓存规则前提下，获取清单文件。 
3. 如果当前缓存的清单副本是最新的，浏览器将向 applicationCache 对象发送一个 noupdate 事件，到此，更新过程结束。注意，如果你在服务器修改了任何缓存资源，同时也应该修改清单文件，这样浏览器才能知道它需要重新获取资源。 
4. 如果清单文件已经改变，文件中列出的所有文件—也包括通过调用 applicationCache.add() 方法添加到缓存中的那些文件—会被获取并放到一个临时缓存中，遵循适当的 HTTP 缓存规则。对于每个加入到临时缓存中的文件，浏览器会向 applicationCache 对象发送一个 progress 事件。如果出现任何错误，浏览器会发送一个 error 事件，并暂停更新。 
5. 一旦所有文件都获取成功，它们会自动移送到真正的离线缓存中，并向 applicationCache 对象发送一个 cached 事件。鉴于文档早已经被从缓存加载到浏览器中，所以更新后的文档不会重新渲染，直到页面重新加载(可以手动或通过程序).

### 缓存状态
UNCACHED(未缓存)  
一个特殊的值，用于表明一个应用缓存对象还没有完全初始化。  

IDLE(空闲)  
应用缓存此时未处于更新过程中。  

CHECKING(检查)  
清单已经获取完毕并检查更新。  

DOWNLOADING(下载中)   
下载资源并准备加入到缓存中，这是由于清单变化引起的。  

UPDATEREADY(更新就绪)   
一个新版本的应用缓存可以使用。有一个对应的事件updateready，当下载完毕一个更新，并且还未使用 swapCache() 方法激活更新时，该事件触发，而不会是 cached 事件。  

OBSOLETE(废弃)  
应用缓存现在被废弃。  

**更新缓存的时机**  
一旦应用被缓存，它就会保持缓存直到发生下列情况：  
用户清空浏览器缓存  
manifest 文件被修改（包括更改注释）   
由程序来更新应用缓存  

### 缓存中的事件
已知的applicationCache中的事件如下所示: 
1. oncached	当离线资源存储完成后触发
2. onchecking	当浏览器对离线存储资源进行检查的时候触发
3. ondownloading	当浏览器开始下载离线资源的时候触发
4. onerror	当缓存资源失败的时候触发
5. onnoupdate	浏览器检查manifest文件没有更新时触发
6. onobsolete	过时的
7. onprogress	当浏览器缓存每一个资源的时候都会触发一次
8. onupdateready	浏览器更新离线资源完成的时候触发

实例：
```javascript
// 离线资源存储完成之后触发
window.applicationCache.addEventListener('cached', function () {
    console.log('cached');
});

// 离线存储资源进行更新检查的时候会触发
window.applicationCache.addEventListener('checking', function () {
    console.log('checking');
});

// 开始下载离线资源的时候会触发
window.applicationCache.addEventListener('downloading', function () {
    console.log('downloading');
});

// 下载每一个资源的时候会触发
window.applicationCache.addEventListener('progress', function () {
    console.log('progress');
});

// 离线资源更新完成之后
window.applicationCache.addEventListener('updateready', function () {
    console.log('updateready');
});

// 检查更新之后发现没有资源更新的时候触发
window.applicationCache.addEventListener('noupdate', function () {
    console.log('noupdate');
});

// obsolete
window.applicationCache.addEventListener('obsolete', function () {
    console.log('obsolete');
});

// error
window.applicationCache.addEventListener('error', function () {
    console.log('error');
});
```
结果：
![java-javascript](/blog/img/in-post/post-application-cache/result.png)

上图是chrome浏览器模拟断网时的log，在手机浏览器真实断网的情况下触发以下两个事件：   
1.onchecking  
2.onerror 

>注：  
1. onchecking事件每次刷新页面都会被触发，包括第一次加载。 
2. onnoupdate事件触发的时机应该为manifest文件被更新之后，而不是缓存的资源被更新之后。 
3. 应用缓存可以变成废弃的。如果从服务器上移除一个应用的清单文件，浏览器将会清除所有清单中列出的应用缓存，并向 applicationCache 对象发送一个「obsolete」事件。这将使得应用缓存的状态变为 OBSOLETE。

### 加载完成后刷新
当manifest文件更新之后，浏览器下载完成之后会触发onupdateready事件，但是页面并没有被刷新，即新的文件并没有执行，需要程序控制刷新一下。

```javacript
window.applicationCache.addEventListener('updateready', function () {
    if(window.applicationCache.status === window.applicationCache.UPDATEREADY) {
        window.applicationCache.swapCache();
        window.location.reload();
    }
});
```

### 注意事项
- 在缓存清单文件中列出的所有记录必须拥有相同的协议、主机名与端口号。
- 一旦文件被缓存，浏览器会使用缓存的文件，即使服务器文件被修改也不会重新下载。为了确保浏览器更新缓存，请更新manifest文件。
- 引入manifest的页面，即使不在缓存列表里，也会被缓存，也就是说在服务器修改了这个html页面，刷新浏览器也不会生效，必须更新manifest文件，原因请看上一条。
- 当浏览器打开多个页面被缓存的页面的时候，刷新其中一个页面，其他几个页面也会收到缓存事件（比如说onchecking等），也会更新缓存。
- 站点离线缓存的默认容量限制是5M。（待确认）
- 站点中的所有页面，只会缓存一份资源。
- 站点中的其他页面，即使没有引用manifest文件，如果请求了缓存中的资源，也会从缓存中读取。
- 当manifest文件被更新之后，虽然本次访问下载了资源并触发了onupdateready方法，但是新的资源并不会被执行，需要手动刷新执行新的文件。
- 直接请求被缓存的资源，也会从缓存中读取。
- 该特性已从web标准中废弃，未来浏览器可能不再支持，MDN推荐使用Service Workers代替。（根据调查，截止到目前，浏览器仍未完全支持Service Workers，如果要兼容移动端，暂时还不能使用Service Workers）