---
title: SSH做项目时经验总结(1)
date: 2017-07-29 21:43:59
categories: 遇到的坑
tags:
     - 填坑经验
description: SSH做项目时经验总结-1
---
```

1、
    application.xml导入其他子文件
    <import resource="applicationContext-staff.xml"/>
    Struts.xm1导入其他子文件
    <include file="struts/struts-staff.xml"></include>
2、
    统一访问界面的UIAction 
    <action name="uiAction_*_*">
        <result>/WEB-INF/pages/{1}/{2}.jsp</result>
    </action>
例如：<frame src="${pageContext.request.contextPath}/uiAction_fram`这里写代码片`e_left1.action" scrolling="no"/>
3、
登录拦截器
protected String doIntercept(ActionInvocation invocation) throws Exception {
        //获得session中的对象
        Object obj = ActionContext.getContext().getSession().get("staff");
        if(obj == null){
            //获得当前action
            Object action = invocation.getAction();
            //判断是否继承了ActionSupport
            if(action instanceof ActionSupport){
                ActionSupport actionSupport = (ActionSupport) action;
                //使用ActionSupport类添加提示信息
                actionSupport.addFieldError("", "您还未登录，请登录！");
            }
            //未登录，需要登录
            return "login";
        }
        //登录过，直接放行
        return invocation.invoke();
    }
4、
 延长session关闭时间，配置spring的OpenSessionInViewFilter过滤器,并且要在struts核心过滤器前面
    <filter>
        <filter-name>sessionFilter</filter-name>
        <filter-class>org.springframework.orm.hibernate3.support.OpenSessionInViewFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>sessionFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
5、
遍历时偶数判断
<s:iterator value="allStaff" status="vs">
<!-- vs.even表示判断是否为偶数，并把vs记录的遍历信息存入小mapActionContext中 -->
    <tr class="<s:property value="#vs.even ? 'tabtd2':'tabtd1'"/>"> 
6、
以注入方式配置actionName和名称空间
<result name="edit" type="redirectAction">
    <param name="namespace">/</param>
    <param name="actionName">staffAction_findAll</param>
</result>
7、
修饰日期，先用date标签修饰格式，在调用var属性存入ActionContext中 
<s:date name="onDutyDate" format="yyyy-MM-dd" var="dutyDate"/>
            <s:textfield name="onDutyDate" readonly="true" value="%{#dutyDate}" />
8、
select标签的使用及listKey和listValue使用
<td width="10%">所属部门：</td>
<td width="20%">
    <s:select list="findAllDepartment" name="post.department.depId" onchange="" 
    listKey="depId" listValue="depName" headerKey="" headerValue="----请--选--择----">
    </s:select>
</td>
<td width="8%">职务：</td>
<td width="62%">
    <%-- 如果职工表中的数据没有职务，则会报异常，所以要进行判断 --%>
    <s:select list="post != null ? post.department.postSet : {}" name="post.postId" id="selectPostId"
    listKey="postId" listValue="postName" headerKey="" headerValue="----请--选--择----">
    </s:select>
</td>
9、
使用select，需要在action中获得需要的数据，并压入栈
需要手动获得所有部门的集合，因为关联数据中没有部门集合这一字段
List<CrmDepartment> findAllDepartment = departmentService.findAllDepartment();
ActionContext.getContext().getValueStack().set("findAllDepartment", findAllDepartment);
10、
服务器处理ajax数据
public String findAllWithDepartment() throws Exception{
    List<CrmPost> findAllWithDepartment = postService.findAllWithDepartment(crmPost.getDepartment());
    //将java对象装换为json数据
    //1.排除不需要的数据
    JsonConfig jsonConfig = new JsonConfig();
    jsonConfig.setExcludes(new String[]{"department","staffSet"});
    //2.转换
    String jsonData = JSONArray.fromObject(findAllWithDepartment, jsonConfig).toString();
    //3.处理乱码
    ServletActionContext.getResponse().setContentType("text/html;charset=UTF-8");
    //4.发送给浏览器
    ServletActionContext.getResponse().getWriter().print(jsonData);
    return "none";
}
ajax代码
<script type="text/javascript">
        function showPost(obj){
            //选中部门
            var depId = obj.value
            //发送ajax通过部门查询职务
            //1.获得引擎
            var xmlhttp;
            if (window.XMLHttpRequest){
                // code for IE7+, Firefox, Chrome, Opera, Safari
                 xmlhttp=new XMLHttpRequest();
            }
            else{
                // code for IE6, IE5
                 xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
            }
            //2.设置回调函数
            xmlhttp.onreadystatechange() = function(){
                if(xmlhttp.readyState == 4 && xmlhttp.status == 200){
                    //获得返回的数据
                    var text = xmlhttp.responseText;
                    //把数据装换成数组
                    var jsonData = eval("("+text+")");
                    //获得select标签
                    var postSelectElement = document.getElementById("selectPostId"); 
                    postSelectElement.innerHTML = "<option value=''>----请--选--择----</option>";
                    //进行遍历
                    for(i = 0; i < jsonData.length; i++){
                        var post = jsonData[i];
                        //获得Id
                        var postId = post.postId;
                        //获得名称
                        var postName = post.postName;
                        //修改html标签内容
                        postSelectElement.innerHTML += "<option value='"+postId+"'>"+postName+"</option>";
                    }
                }
            };
            //3.创建链接
            var url = "${pageContext.request.contextPath}/postAction_findAllWithDepartment?department.depId="+depId;
            xmlhttp.open("GET", url);
            //4.发送请求,如果使用POST方法，send（depId）应传入值
            xmlhttp.send(null);
        }
    </script>
11、
条件查询拼接语句的思路
@Override
    public List<CrmCourseType> findCourseByCondition(CrmCourseType crmCourseType) {
        // 用于拼接HQL语句
        StringBuilder builder = new StringBuilder();
        // 用来封装参数，list有序，可以重复
        List<Object> list = new ArrayList<Object>();
        builder.append("from CrmCourseType where 1=1");
        // 课程类别
        if (StringUtils.isNotBlank(crmCourseType.getCourseName())) {
            builder.append(" and courseName like ? ");
            list.add("%" + crmCourseType.getCourseName() + "%");
        }
        // 课程简介
        if (StringUtils.isNotBlank(crmCourseType.getRemark())) {
            builder.append(" and remark like ? ");
            list.add("%" + crmCourseType.getRemark() + "%");
        }
        // 学时
        if (StringUtils.isNotBlank(crmCourseType.getTotalStart())) {
            builder.append(" and total >= ? ");
            list.add(Integer.parseInt(crmCourseType.getTotalStart()));
        }
        if (StringUtils.isNotBlank(crmCourseType.getTotalEnd())) {
            builder.append(" and total <= ? ");
            list.add(Integer.parseInt(crmCourseType.getTotalEnd()));
        }
        // 费用
        if (StringUtils.isNotBlank(crmCourseType.getLessonCostStart())) {
            builder.append(" and courseCost >= ? ");
            list.add(Double.parseDouble(crmCourseType.getLessonCostStart()));
        }
        if (StringUtils.isNotBlank(crmCourseType.getLessonCostEnd())) {
            builder.append(" and courseCost <= ? ");
            list.add(Double.parseDouble(crmCourseType.getLessonCostEnd()));
        }
        return coursetypeDao.findCourseByCondition(builder.toString(),
                list.toArray());
    }
12、
更新和添加写在一起时要在页面中判断Id是否应该传入，如果是添加数据，没有id则使用hidden标签传入id会报错
<s:if test="courseTypeId != null">
        <s:hidden name="courseTypeId" value="%{courseTypeId}"/>
</s:if>
13、
标签回显action也要判断Id，如果不判断会报异常
if(StringUtils.isNotBlank(crmCourseType.getCourseTypeId())){
            CrmCourseType findCourse = coursetypeService.findCourseById(crmCourseType);
            ActionContext.getContext().getValueStack().push(findCourse);
}

```

