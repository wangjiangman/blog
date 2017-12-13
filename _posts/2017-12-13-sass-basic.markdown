---
layout:     post
title:      "Sass-首选的CSS扩展语言"
subtitle:   ""
date:       2017-12-13
author:     "Fiona"
header-img: "img/sass.png"
tags:
    - CSS
    - 原创
---

> 让CSS可编程

## Sass 与 SCSS  
Sass就是SCSS的严格模式。SCSS为兼容CSS而生，所以为了兼容CSS写作习惯，文件强烈建议为.scss。

## Why **Sass** but not **Less**
- 强大团队维护，参考资料多
- 大项目的选择，Bootstrap从4.0开始转为Sass，Vue-cli支持Sass
  > Sass基于Ruby， Less的编译简单方便，更适合Node

## Sass功能概览
- 引入（Import）: 引入其它样式文件
- 变量（Variables）：复用值
- 嵌套（Nesting）：使CSS可维护性和可读性增强
- 混合器（mixin , include）：便于重用大段的样式，利于模块化
- 继承（Extend）：实现样式继承，利于模块化

## Sass语法使用
### 引入（Import）
关键符号:  **@import**

### 变量（Variables）
关键符号：**$**

### 嵌套（Nesting）
关键符号：**{}**

### 混合器（mixin , include）
关键符号：**@mixin @include**

### 继承（Extend）
关键符号：**@extend**


## 参考链接 
[Sass官网](https://www.sass.hk/)