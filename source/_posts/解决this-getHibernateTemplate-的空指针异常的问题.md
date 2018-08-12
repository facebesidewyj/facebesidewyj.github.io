---
title: 解决this.getHibernateTemplate()的空指针异常的问题
date: 2017-07-29 21:49:27
categories: 遇到的坑
tags:
     - 填坑经验
description: 解决this.getHibernateTemplate()的空指针异常的问题
---
刚刚在整合SSH时碰到了这样一个问题：
当我用junit测试时不会报任何异常，数据也都能得到
public class BuyerTest {
@BeforeClass
public static void begin() throws Exception{}

@Test
public void save(){
　　Configuration config=new AnnotationConfiguration().configure();
　　SessionFactory sf=config.buildSessionFactory();
　　Session session=sf.openSession();
　　Transaction tr=session.beginTransaction();
　　try {
　　　　tr.begin();
　　　　Buyer b=(Buyer) session.get(Buyer.class,"zs");
　　　　System.out.println(b.getUserpwd());
　　　　tr.commit();
　　　　} catch (HibernateException e) {
　　　　　　e.printStackTrace();
　　　　　　tr.rollback();
　　　　}
　　}
}
可是当我写个main方法测试dao包时，却出现空指针异常：



我的代码是这样的：
dao包：
public class BuyerDao extends HibernateDaoSupport {
　　public Buyer save() {
　　　　HibernateTemplate template=this.getHibernateTemplate();
　　　　String hql="from Buyer u";
　　　　List<Buyer> list=template.find(hql);
　　　　if(null==list){
　　　　　　return null;
　　　　}
　　　　if(list.size()!=0){
　　　　　　System.out.println(list.get(0).getUserpwd());
　　　　　　return list.get(0);
　　　　}
　　　　return null;
　　}
}
-----------------------------------------------------------------
main方法：
public class main {
　　public static void main(String[] args) {
　　　　BuyerDao udao=new BuyerDao();
　　　　udao.save();
　　　}
}
-----------------------------------------------------------------
搞了好长时间，在网上找了下答案，没怎么看明白，但我发现有人说看看this.getHibernateTemplate()是否为空，我调试了下，发现我的代码中this.getHibernateTemplate()确实为null！！！
ps：我的applicationContext.xml文件没有错误。

这里我们需要想一下为什么要用spring？或者说使用spring要用到它的什么思想等等。
---使用spring是要用到它的依赖注入的，而直接new出来的是没有依赖注入的，故对象没有初始化。
也就是说
BuyerDao udao=new BuyerDao();
udao.save();
这里不存在依赖注入，也就自然而然地报空指针异常。
解决办法：在main方法中，不要对BuyerDao 进行初始化，而是通过配置文件applicationContext.xml来获取。
ClassPathXmlApplicationContext resource  = new  ClassPathXmlApplicationContext("applicationContext.xml");;
BuyerDao dao=(BuyerDao) resource.getBean("userDao");
dao.save();

注：userDao是配置文件applicationContext.xml中bean的id值
<bean id="userDao" class="jnuit.test.BuyerDao">
<property name="sessionFactory" ref="sessionFactory"></property>
</bean>
这样，this.getHibernateTemplate()==null的问题就解决了。
