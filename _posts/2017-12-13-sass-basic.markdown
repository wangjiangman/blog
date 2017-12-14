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
Sass就是SCSS的严格模式。SCSS为兼容CSS而生，所以为了兼容CSS写作习惯，强烈建议文件后缀为.scss。

## Why **Sass** but not **Less**
- 强大团队维护，参考资料多
- 大项目的选择，Bootstrap从4.0开始转为Sass，Vue-cli支持Sass
  > Sass基于Ruby， Less的编译简单方便，更适合Node

## Sass功能概览
- 变量（Variables）：复用值
- 引入（Import）: 引入其它样式文件
- 嵌套（Nesting）：使CSS可维护性和可读性增强
- 混合器（mixin , include）：便于重用大段的样式，利于模块化
- 继承（Extend）：实现样式继承，利于模块化

## Sass语法使用

### 变量（Variables）
关键符号：**$**  
1. 任何可以用作css属性值的赋值都可以用作sass的变量值，甚至是以空格分割的多个属性值，如$basic-border: 1px solid black;，或以逗号分割的多个属性值，如$plain-font: "Myriad Pro"、Myriad、"Helvetica Neue"、Helvetica、"Liberation Sans"、Arial和sans-serif; sans-serif;。  
> Sass变量具有括号{}作用域：括号内可以引用括号外的属性，括号内的属性只能在括号内被引用。

    ```css
    $nav-color: #F90;
    nav {
      $width: 100px;
      width: $width;
      color: $nav-color;
    }
    ```
2. 变量可以直接应用变量
```css
$highlight-color: #F90;
$highlight-border: 1px solid $highlight-color;
.selected {
  border: $highlight-border;
}
```  
3. 变量中的中划线和下划线分割不区分，一下引用是合法的
```css
$link-color: blue;
a {
  color: $link_color;
}
```

### 引入（Import）
关键符号:  **@import**
1. 所有在被导入文件中定义的变量和混合器均可在导入文件中使用
2. @import规则并不需要指明被导入文件的后缀名
```css
@import"sidebar"
```
3. Sass允许嵌套导入
```css
.blue-theme {
  @import "blue-theme"
}
```
引入的blue-theme在容器.blue-theme内有效
4. Sass如何引入css文件
将css改名为.scss后缀
5. Sass使用//达到静默注释的目的
编译后生成的css文件看不到静默注释。注释出现在不该出现的地方，Sass也会将其抹掉。
```css
body {
  color: #333; // 这种注释内容不会出现在生成的css文件中
  padding: 0; /* 这种注释内容会出现在生成的css文件中 */
}
body {
  color /* 这块注释内容不会出现在生成的css中 */: #333;
  padding: 1; /* 这块注释内容也不会出现在生成的css中 */ 0;
}
```


### 嵌套（Nesting）
关键符号：**{} &**

主要使得父子选择器更易读
```css
/* 普通嵌套 */
#content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  #content aside { background-color: #EEE }
}

/* 父级引用 */
article a {
  color: blue;
  &:hover { color: red }
}

/* 群组选择器 */
.container {
  h1, h2, h3 {margin-bottom: .8em}
}
nav, aside {
  a {color: blue}
}

/* 子组合选择器和同层组合选择器：>、+和~; */
article {
  ~ article { border-top: 1px dashed #ccc }
  > section { background: #eee }
  dl > {
    dt { color: #333 }
    dd { color: #555 }
  }
  nav + & { margin-top: 0 }
}

/* 属性嵌套 */
nav {
  border: {
  style: solid;
  width: 1px;
  color: #ccc;
  }
}
```

### 混合器（mixin , include）
关键符号：**@mixin @include**

使用混合器来避免重复

```css
@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}

notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}
```

### 继承（Extend）
关键符号：**@extend**

使用继承来精简CSS

```css
.error {
  border: 1px solid red;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

## 参考链接 
[Sass官网](https://www.sass.hk/)