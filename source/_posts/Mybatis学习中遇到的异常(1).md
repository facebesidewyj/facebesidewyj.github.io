---
title: Mybatis学习中遇到的异常(1)
date: 2017-07-29 11:50:55
categories: 遇到的坑
tags:
     - 填坑经验
description: Mybatis学习中遇到的异常
---
org.apache.ibatis.exceptions.PersistenceException:
### Error building SqlSession.
### The error may exist in /mapper/UserMapper.xml
### Cause: org.apache.ibatis.builder.BuilderException: Error parsing SQL Mapper Configuration. Cause: java.io.IOException: Could not find resource /mapper/UserMapper.xml
**一般是mapper配置文件的问题，请仔细检查配置路径，**
**或配置文件中是否多了或少了什么内容！**