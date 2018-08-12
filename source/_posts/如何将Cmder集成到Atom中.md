---
title: 如何将Cmder集成到Atom中
date: 2018-04-17 22:21:18
categories: 团队规范构建
tags:
     - 插件
description: Win10环境下在Atom中集成Cmder
---
# 如何将Cmder集成到Atom中

1. 在Cmder根目录中创建文件atom.bat，将下列命令写入

```
@echo off 
SET CMDER_ROOT=C:\Path\To\cmder #注释（去掉） Cmder安装根目录
%CMDER_ROOT%\vendor\init.bat
```
2. 安装Atom插件 [platformio-ide-terminal](https://atom.io/packages/platformio-ide-terminal)

3. 安装完毕后在插件的setting中的配置如下：

    Auto Run Command:

    **“C:\Path\To\cmder\atom.bat”** atom.bat的路径

    Shell Override:

    **C:\Windows\System32\cmd.exe**

