---
title: SSH做项目时经验总结(3)
date: 2017-07-29 21:43:59
categories: 遇到的坑
tags:
     - 填坑经验
description: SSH做项目时经验总结-3
---
**SSH做项目时经验总结 （3）**
1.IllegalArgumentException occurred while calling setter of pers.yijin.bms.domain.User.messageList
hbm.xml文件与实体类文件还有数据库中的文件不匹配的情况就会出现

2.Write operations are not allowed in read-only mode (FlushMode.MANUAL): Turn your Session into FlushMode.COMMIT/AUTO or remove 'readOnly' marker from transaction definition.
开启OpenSessionInViewFilter来阻止延迟加载的错误,我们开启OpenSessionInViewFilter这个过滤器的时候FlushMode就已经被默认设置为了MANUAL！
如果FlushMode是MANUAL或NEVEL,在操作过程中 hibernate会将事务设置为readonly，解决方法在过滤器中注入：


```
    <init-param>
        <param-name>flushMode</param-name>
        <param-value>AUTO</param-value>
    </init-param>
```

3.hibernate延长session关闭时间后，删除数据需要flush()刷session

4.直接把list压入栈struts2的iterator标签不能取值

