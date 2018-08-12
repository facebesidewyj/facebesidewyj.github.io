---
title: Mybatis学习中遇到的异常(3)
date: 2017-07-29 11:50:55
categories: 遇到的坑
tags:
     - 填坑经验
description: Mybatis学习中遇到的异常
---
Mybatis与Spring整合时遇到异常如下：

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'sqlSessionFactory' defined in class path resource [applicationContext.xml]: Error setting property values; nested exception is org.springframework.beans.PropertyBatchUpdateException; nested PropertyAccessExceptions (1) are:
PropertyAccessException 1: org.springframework.beans.MethodInvocationException: Property 'dataSource' threw exception; nested exception is java.lang.NoClassDefFoundError: org/springframework/jdbc/datasource/TransactionAwareDataSourceProxy
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.applyPropertyValues(AbstractAutowireCapableBeanFactory.java:1427)
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.populateBean(AbstractAutowireCapableBeanFactory.java:1132)
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:522)
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:461)
    at org.springframework.beans.factory.support.AbstractBeanFactory$1.getObject(AbstractBeanFactory.java:295)
    at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:223)
    at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:292)
    at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:194)
    at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:589)
    at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:932)
    at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:479)
    at org.springframework.context.support.ClassPathXmlApplicationContext.<init>(ClassPathXmlApplicationContext.java:139)
    at org.springframework.context.support.ClassPathXmlApplicationContext.<init>(ClassPathXmlApplicationContext.java:83)
    at pers.yijin.test.UserDaoImplTest.setUp(UserDaoImplTest.java:15)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
    at java.lang.reflect.Method.invoke(Method.java:597)
    at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:44)
    at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:15)
    at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:41)
    at org.junit.internal.runners.statements.RunBefores.evaluate(RunBefores.java:27)
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
Caused by: org.springframework.beans.PropertyBatchUpdateException; nested PropertyAccessExceptions (1) are:
PropertyAccessException 1: org.springframework.beans.MethodInvocationException: Property 'dataSource' threw exception; nested exception is java.lang.NoClassDefFoundError: org/springframework/jdbc/datasource/TransactionAwareDataSourceProxy
    at org.springframework.beans.AbstractPropertyAccessor.setPropertyValues(AbstractPropertyAccessor.java:101)
    at org.springframework.beans.AbstractPropertyAccessor.setPropertyValues(AbstractPropertyAccessor.java:57)
    at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.applyPropertyValues(AbstractAutowireCapableBeanFactory.java:1424)
    ... 36 more

出现异常的原因是因为spring-jdbcjar没导入，类找不到了