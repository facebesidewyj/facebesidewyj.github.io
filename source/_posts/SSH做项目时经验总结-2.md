---
title: SSH做项目时经验总结(2)
date: 2017-07-29 21:43:59
categories: 遇到的坑
tags:
     - 填坑经验
description: SSH做项目时经验总结-2
---
```
1、
/**
 * 用于存放分页数据的类
 */
public class PageBean<T> {

    private int pageNum; // 第几页
    private int pageSize; // 每页显示的个数
    private int totalRecord;// 总记录数

    private int startIndex; // 开始索引
    private int totalPage; // 总分页数

    private List<T> data; // 查询到的分页数据集合

    // 动态显示条
    private int start;
    private int end;

    // 生成一个三个参数的构造方法，使对象被创建时，所有参数就都被赋值
    public PageBean(int pageNum, int pageSize, int totalRecord) {
        super();
        this.pageNum = pageNum;
        this.pageSize = pageSize;
        this.totalRecord = totalRecord;

        // 计算开始索引
        this.startIndex = (this.pageNum - 1) * this.pageSize;
        // 计算总分页数
        this.totalPage = (this.totalRecord + this.pageSize - 1) / this.pageSize;

        // 动态显示条的初始值
        this.start = 1;
        this.end = 10;

        // 判断总分页数和动态显示的关系，如果总分页数比10小，则动态显示条最大为总分页数
        if (this.totalPage <= 10) {
            this.end = this.totalPage;
        } else {
            // 如果总分页数比10大，则动态显示的start值应为当前页减去4页，end值为当前页加上5页
            this.start = this.pageNum - 4;
            this.end = this.pageNum + 5;
            // 当前页为前4页时，如果当start的值为负数时，则start恒定为1，end为10
            if (this.start < 1) {
                this.start = 1;
                this.end = 10;
            }
            // 当前页为最后5页，则end值肯定比总分页数大，这时需要使end等于总分页数
            if (this.end > this.totalPage) {
                this.end = this.totalPage;
                this.start = this.end - 9;
            }
        }
    }

2、
    dao操作
    public int getTotalRecord(String condition, Object[] params) {
        List<Long> list = this.getHibernateTemplate().find(condition, params);
        // 长整形转换成整形intValue()
        return list.get(0).intValue();
    }

    @Override
    public List<CrmCourseType> findCourseByCondition(String condition,
            Object[] params, int startIndex, int pageSize) {
        return this.getHibernateTemplate().execute(
                new PageHibernateCallBack<CrmCourseType>().setHql(condition)
                        .setParams(params).setStartIndex(startIndex)
                        .setPageSize(pageSize));
    }

3、
    /**
 * 用于分页的回调函数
 *
 * @author wyj
 *
 * @param <T>
 */
public class PageHibernateCallBack<T> implements HibernateCallback<List<T>> {
    private String hql;  给query对象传入的hql语句
    private Object[] params; 传入参数数组
    private int startIndex; 传入分页用的开始索引
    private int pageSize;   分页用的每页显示个数

    // 链式调用
    public PageHibernateCallBack<T> setHql(String hql) {
        this.hql = hql;
        return this;
    }
    public PageHibernateCallBack<T> setParams(Object[] params) {
        this.params = params;
        return this;
    }
    public PageHibernateCallBack<T> setStartIndex(int startIndex) {
        this.startIndex = startIndex;
        return this;
    }
    public PageHibernateCallBack<T> setPageSize(int pageSize) {
        this.pageSize = pageSize;
        return this;
    }
    @Override
    public List<T> doInHibernate(Session session) throws HibernateException,
            SQLException {
        // 获得query对象
        Query query = session.createQuery(hql);
        // 遍历多个参数
        for (int i = 0; i < params.length; i++) {
            // 给query对象设置参数
            query.setParameter(i, params[i]);
        }
        // 设置分页所用到的数据
        query.setFirstResult(startIndex);
        query.setMaxResults(pageSize);
        // 查询所有
        return query.list();
    }

4、
    Service层中添加的操作
    //获取总记录数
        int totalRecord = this.coursetypeDao.getTotalRecord(condition,params);
        //创建pageBean对象
        PageBean<CrmCourseType> pageBean = new PageBean<CrmCourseType>(pageNum, pageSize, totalRecord);
        //进行分页查询，获得分页数据
        List<CrmCourseType> data = this.coursetypeDao.findCourseByCondition(condition, params, pageBean.getStartIndex(), pageBean.getPageSize());
        pageBean.setData(data);
        return pageBean;

5、
jsp的编写
<span>第<s:property value="pageNum" />/<s:property value="totalPage" />页</span>
        <span>
            <s:if test="pageNum gt 1">
                <a href="javascript:void(0)" onclick="showPage(1)">[首页]</a>&nbsp;&nbsp;
                <a href="javascript:void(0)" onclick="showPage(<s:property value="pageNum - 1" /> )">[上一页]</a>&nbsp;&nbsp;
            </s:if>
          <!--动态滚动条 -->
            <s:iterator begin="start" end="end" var="num">
                <a href="#" onclick="showPage(<s:property value="#num" />)"><s:property value="#num" /></a>
            </s:iterator>

            <s:if test="pageNum lt totalPage">
                <a href="javascript:void(0)" onclick="showPage(<s:property value="pageNum + 1"/> )">[下一页]</a>&nbsp;&nbsp;
                <a href="javascript:void(0)" onclick="showPage(<s:property value="totalPage"/>)">[尾页]</a>
            </s:if>
        </span>

    <script type="text/javascript">
        function showPage(num){
            //修改隐藏域的值
            document.getElementById("pageNum").value = num;
            //提交表单
            document.forms[0].submit();
        }
    </script>
    form表单
        <%-- 添加隐藏域，传入pageNum --%>
        <s:hidden id="pageNum" name="pageNum" value="1"/>

6、Iterator标签消失的问题，传入的集合找不到了

7、this关键字，哪个类被new。this就指哪个类

8、BaseDao
public interface BaseDao<T> {
    /**
     * 添加操作
     *
     * @param t
     */
    public void save(T t);

    /**
     * 删除操作
     *
     * @param t
     */
    public void delete(T t);

    /**
     * 修改操作
     *
     * @param t
     */
    public void update(T t);

    /**
     * 添加或更新操作
     *
     * @param t
     */
    public void saveOrUpdate(T t);

    /**
     * 通过Id查询
     *
     * @param id
     * @return
     */
    public T findById(java.io.Serializable id);

    /**
     * 查询所有
     *
     * @return
     */
    public List<T> findAll();

    /**
     * 通过条件查询所有
     *
     * @param condition
     * @param params
     * @return
     */
    public List<T> findAllByCondition(String condition, Object[] params);

    /**
     * 条件+分页查询
     *
     * @param condition
     * @param params
     * @param startIndex
     * @param pageSize
     * @return
     */
    public List<T> findAllByPage(String condition, Object[] params,
            int startIndex, int pageSize);

    /**
     * 查询总记录数+条件查询
     *
     * @param condition
     * @param params
     * @return
     */
    public int getTotalRecord(String condition, Object[] params);

    /**
     * 离线查询
     *
     * @param criteria
     * @return
     */
    public List<T> findAllByQBC(DetachedCriteria criteria);

    /**
     * 使用QBC进行分页查询
     *
     * @param criteria
     * @param condition
     * @param params
     * @return
     */
    public List<T> findAllByPage(DetachedCriteria criteria, int startIndex,
            int pageSize);

}


9、
BaseDao的实现类
public class BaseDaoImpl<T> extends HibernateDaoSupport implements BaseDao<T> {

    private Class<?> beanClass;
    //泛型<T>在编译时是无类型的，只是变量，T只有在运行以后才可以获得具体类型，子类被new时，父类的构造方法会执行
    public BaseDaoImpl() {
        //ParameterizedType是被参数化了的类型，例如：BaseDaoImpl<CrmClasses>
        ParameterizedType parameterizedType = (ParameterizedType) this
                .getClass().getGenericSuperclass()获得带泛型参数的class对象;
        //getActualTypeArguments()获得所有实际参数值，就是泛型的类型，一般情况下为1个。返回类型为.class的字节码文件
        beanClass = (Class<?>) parameterizedType.getActualTypeArguments()[0];
    }

    public void save(T t) {
        this.getHibernateTemplate().save(t);
    }

    public void delete(T t) {
        this.getHibernateTemplate().delete(t);
    }

    public void update(T t) {
        this.getHibernateTemplate().update(t);
    }

    public T findById(Serializable id) {
        return (T) this.getHibernateTemplate().get(beanClass, id);
    }

    public List<T> findAll() {
        // getName()获得全限定名
        return this.getHibernateTemplate().find("from " + beanClass.getName());
    }

    public List<T> findAllByCondition(String condition, Object[] params) {
        String hql = "from " + beanClass.getName() + " where 1 = 1 "
                + condition;
        return this.getHibernateTemplate().find(hql, params);
    }

    public List<T> findAllByPage(String condition, Object[] params,
            int startIndex, int pageSize) {
        String hql = "from " + beanClass.getName() + " where 1 = 1 "
                + condition;
        return this.getHibernateTemplate().execute(
                new PageHibernateCallBack<T>().setHql(hql).setParams(params)
                        .setStartIndex(startIndex).setPageSize(pageSize));
    }

    public int getTotalRecord(String condition, Object[] params) {
        String hql = "select count(c) from " + beanClass.getName()
                + " as c where 1 = 1 " + condition;
        List<Long> list = this.getHibernateTemplate().find(hql, params);
        return list.get(0).intValue();
    }

    离线查询
    public List<T> findAllByQBC(DetachedCriteria criteria) {
        return this.getHibernateTemplate().findByCriteria(criteria);
    }

    public List<T> findAllByPage(DetachedCriteria criteria, int startIndex,
            int pageSize) {
        return this.getHibernateTemplate().findByCriteria(criteria, startIndex,
                pageSize);
    }

    public void saveOrUpdate(T t) {
        this.getHibernateTemplate().saveOrUpdate(t);
    }
}


10、
BaseAction
public class BaseAction<T> extends ActionSupport implements ModelDriven<T> {

    private T t;

    @Override
    public T getModel() {
        return t;
    }


    /泛型<T>在编译时是无类型的，只是变量，T只有在运行以后才可以获得具体类型，子类被new时，父类的构造方法会执行
    @SuppressWarnings("unchecked")
    public BaseAction() {
        try {
            //ParameterizedType是被参数化了的类型，例如：BaseDaoImpl<CrmClasses>
            ParameterizedType parameterizedType = (ParameterizedType) this
                    .getClass().getGenericSuperclass();
            //getActualTypeArguments()获得所有实际参数值，就是泛型的类型，一般情况下为1个。返回类型为.class的字节码文件
            Class<T> clazz = (Class<T>) parameterizedType
                    .getActualTypeArguments()[0];
            // 反射创建实例
            t = clazz.newInstance();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // 注入service
    private StaffService staffService;
    private PostService postService;
    private DepartmentService departmentService;
    private CoursetypeService coursetypeService;

    public StaffService getStaffService() {
        return staffService;
    }

    public void setStaffService(StaffService staffService) {
        this.staffService = staffService;
    }

    public PostService getPostService() {
        return postService;
    }

    public void setPostService(PostService postService) {
        this.postService = postService;
    }

    public DepartmentService getDepartmentService() {
        return departmentService;
    }

    public void setDepartmentService(DepartmentService departmentService) {
        this.departmentService = departmentService;
    }

    public CoursetypeService getCoursetypeService() {
        return coursetypeService;
    }

    public void setCoursetypeService(CoursetypeService coursetypeService) {
        this.coursetypeService = coursetypeService;
    }

    //分页数据
    private int pageNum = 1;
    private int pageSize = 2;

    public void setPageNum(int pageNum) {
        this.pageNum = pageNum;
    }
    public void setPageSize(int pageSize) {
        this.pageSize = pageSize;
    }
    public int getPageNum() {
        return pageNum;
    }
    public int getPageSize() {
        return pageSize;
    }

    //简化值栈操作
    public void push(Object o){
        ActionContext.getContext().getValueStack().push(o);
    }
    public void put(String key,Object value){
        ActionContext.getContext().put(key, value);
    }
    public void set(String key,Object o){
        ActionContext.getContext().getValueStack().set(key, o);
    }
    public void putSession(String key,Object value){
        ActionContext.getContext().getSession().put(key, value);
    }
    public void putApplication(String key,Object value){
        ActionContext.getContext().getApplication().put(key, value);
    }
}

11、
文件上传
    获取文件上传的三个值
    private File schedule; // 上传内容
    private String scheduleFileName; // 上传的文件名
    private String scheduleContentType; // 上传的类型
    上传操作
    public String upload() throws IOException {
        //获取上传目的地的虚拟路径
        String parentDir = ServletActionContext.getServletContext().getRealPath("/WEB-INF/upload");
        //给上传文件改名
        String fileName = UUID.randomUUID().toString().replace("-", "");
        //保存文件
        FileUtils.copyFile(schedule, new File(parentDir, fileName));
    配置拦截器可通过的扩展名
    <interceptor-ref name="defaultStack">
        <param name="fileUpload.allowedExtensions">xls,xslx</param>
    使用@InputConfig(resultName="uploadInput")修改input视图的name

```
