---
title: Struts2框架的工作流程总结
date: 2017-07-29 14:30:35
categories: struts2框架
tags:
     - 框架
     - struts2
description: Struts2框架的工作流程总结
---
先放一张图片(处理过的)
![这里写图片描述](http://img.blog.csdn.net/20170311210630800?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ2xhZGlhdG9yX3d5ag==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
**1.**
用户使用***客户端***浏览器发送一个***请求***（HttpServletRequest）到***服务器***（Tomcat）
**2.**
这个请求会经过几个***过滤器***（Filter）最后到达Struts2的核心过滤器***FilterDispatcher***
**3.**
FilterDispatcher询问***动作映射ActionMapper***来找到***对应***的Action类
**4.**
接着FilterDispatcher会把请求提交到Action的***代理类ActionProxy***
**5.**
ActionProxy会读取***配置文件struts.xml***来确定执行哪个方法
**6.**
ActionProxy会创建一个***ActionInvocation***，ActionInvocation中持有Action实例和所有与Action相关的拦截器Interceptor
**7.**
ActionInvocation会让Action在执行之前，***执行与Action相关的拦截器***，接着执行Action，***返回Result***结果视图，通常是Jsp页面，最后***倒序***执行与Action相关的拦截器
**8.**
最后Struts2框架把***响应HttpServletResponse***返回给客户端

