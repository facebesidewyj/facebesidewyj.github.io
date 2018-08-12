---
title: JavaScript高级程序设计读书笔记--第1章
date: 2017-07-30 15:15:52
categories: JS高程笔记
tags:
     - 笔记
     - JavaScript
description: JavaScript高级程序设计读书笔记--第1章JavaScript简介
---
# JavaScript简史
JavaScript诞生于1995年，当时JavaScript的主要是负责一些输入的验证，由Netscape公司着手开发。
1997年，JavaScript1.1被建议提交给了欧洲计算机制造商协会(ECMA)，由ECMA定义了一种名为ECMAScript(发音为"ek-ma-script")的新脚本语言标准。
# JavaScript实现
一个完整的JavaScript实现应由三部分组成：**核心(ECMAScript)、文档对象模型(DOM)、浏览器对象模型(DOM)**。
## (1)ECMAScript
ECMAScirpt是一个语言标准，它定义了这门语言的基础，具体有以下几个组成部分：
    1.语法
    2.类型
    3.语句
    4.关键字
    5.保留字
    6.操作符
    7.对象
脚本语言的执行需要宿主环境，例如Web浏览器、Node(一种服务器JavaScript平台)、AdobeFlash等。
## (2)文档对象模型(DOM)
文档对象模型：Document Object Model，是针对XML但经过扩展用户HTML的应用程序(API)，DOM把整个页面映射为一个多层节点结构，提供了访问和操作网页内容的方法和接口。**注意：DOM并不是针对JavaScript的，很多语言也实现了DOM**。
## (3)浏览器对象模型(BOM)
浏览器对象模型：Browser Object Model，支持访问和**操作浏览器窗口，提供了与浏览器交互的方法和接口**。
# JavaScript版本
JavaScript在浏览器中最后一个版本号是1.3，JavaScript1.4是针对服务器的实现。