---
title: Maven配置环境变量出现的问题
date: 2017-07-29 21:49:27
categories: 遇到的坑
tags:
     - 填坑经验
description: Maven配置环境变量出现的问题
---
出现
**ERROR: JAVA_HOME is set to an invalid directory.
JAVA_HOME = "C:\Program Files\Java\jdk1.8.0_65\bin"
Please set the JAVA_HOME variable in your environment to match the
location of your Java installation**
如何解决？
(本人解决方法)
1、将java的环境变量重新配置一遍
创建JAVA_HOME：本机jdk安装目录
path中： **.;%JAVA_HOME%\bin;**  添加到最前面，命令行输入javac测试
2、在配置maven
创建MAVEN_HOME:本机安装路径
path中：**;%MAVEN_HOME%\bin;** 配置到最后(注意分号不要多了，分号用于分隔)

