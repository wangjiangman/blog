---
layout:     post
title:      "使用注册表设置右键打开windows桌面程序"
subtitle:   ""
date:       2017-12-28
author:     "Fiona"
header-img: "img/linux-bg.jpg"
tags:
    - tools
    - 原创
---

> 以右键访问Visual Code为例

### 鼠标右键关联文件
1. Win+R regedit 打开注册表
2. 找到HKEY_CLASSES_ROOT\*\shell分支，新建VisulCode项，值为“使用VSCode打开”
3. 在VSCode下新建command项，值为："D:\Program Files (x86)\Microsoft VS Code/Code.exe" "%1"
    > 其中的%1表示要打开的文件参数
关闭注册表后立即生效。

### 鼠标右键关联文件夹
1. Win+R regedit 打开注册表
2. 找到HKEY_CLASSES_ROOT\Directory\shell分支，新建VisulCode项，值为“使用VSCode打开”
3. 在VSCode下新建command项，值为："D:\Program Files (x86)\Microsoft VS Code/Code.exe" "%1"
    > 其中的%1表示要打开的文件参数
关闭注册表后立即生效。
