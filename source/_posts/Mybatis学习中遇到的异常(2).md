---
title: Mybatis学习中遇到的异常(2)
date: 2017-07-29 11:50:55
categories: 遇到的坑
tags:
     - 填坑经验
description: Mybatis学习中遇到的异常
---
org.apache.ibatis.builder.IncompleteElementException: Could not find parameter map pers.yijin.mapper.UserMapper.int
    at org.apache.ibatis.builder.MapperBuilderAssistant.setStatementParameterMap(MapperBuilderAssistant.java:319)
    at org.apache.ibatis.builder.MapperBuilderAssistant.addMappedStatement(MapperBuilderAssistant.java:283)
    at org.apache.ibatis.builder.xml.XMLStatementBuilder.parseStatementNode(XMLStatementBuilder.java:107)
    at org.apache.ibatis.session.Configuration.buildAllStatements(Configuration.java:698)
    at org.apache.ibatis.session.Configuration.hasStatement(Configuration.java:668)
    at org.apache.ibatis.session.Configuration.hasStatement(Configuration.java:663)
    at org.apache.ibatis.binding.MapperMethod$SqlCommand.<init>(MapperMethod.java:180)
    at org.apache.ibatis.binding.MapperMethod.<init>(MapperMethod.java:43)
    at org.apache.ibatis.binding.MapperProxy.cachedMapperMethod(MapperProxy.java:58)
    at org.apache.ibatis.binding.MapperProxy.invoke(MapperProxy.java:51)
    at $Proxy5.findUsernameById(Unknown Source)
    at pers.yijin.test.UserMapperTest.test(UserMapperTest.java:37)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
    at java.lang.reflect.Method.invoke(Method.java:597)
    at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:44)
    at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:15)
    at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:41)
    at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:20)
    at org.junit.internal.runners.statements.RunBefores.evaluate(RunBefores.java:28)
    at org.junit.runners.BlockJUnit4ClassRunner.runNotIgnored(BlockJUnit4ClassRunner.java:79)
    at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:71)
    at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:49)
    at org.junit.runners.ParentRunner$3.run(ParentRunner.java:193)
    at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:52)
    at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:191)
    at org.junit.runners.ParentRunner.access$000(ParentRunner.java:42)
    at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:184)
    at org.junit.runners.ParentRunner.run(ParentRunner.java:236)
    at org.eclipse.jdt.internal.junit4.runner.JUnit4TestReference.run(JUnit4TestReference.java:50)
    at org.eclipse.jdt.internal.junit.runner.TestExecution.run(TestExecution.java:38)
    at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:467)
    at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.runTests(RemoteTestRunner.java:683)
    at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.run(RemoteTestRunner.java:390)
    at org.eclipse.jdt.internal.junit.runner.RemoteTestRunner.main(RemoteTestRunner.java:197)
Caused by: java.lang.IllegalArgumentException: Parameter Maps collection does not contain value for pers.yijin.mapper.UserMapper.int
    at org.apache.ibatis.session.Configuration$StrictMap.get(Configuration.java:797)
    at org.apache.ibatis.session.Configuration.getParameterMap(Configuration.java:570)
    at org.apache.ibatis.builder.MapperBuilderAssistant.setStatementParameterMap(MapperBuilderAssistant.java:317)
    ... 35 more

***这个原因是把parameterType写成了parameterMap，以后一定注意！***
