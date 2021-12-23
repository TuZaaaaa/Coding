# MyBatis 应用场景
## PageHelper
> 官方文档：https://github.com/pagehelper/Mybatis-PageHelper

pom.xml:
```xml
    <dependency>
        <groupId>com.github.pagehelper</groupId>
        <artifactId>pagehelper</artifactId>
        <version>x.x.x</version>
    </dependency>
```

mybatis-config.xml
```xml
    <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
            <!-- config params as the following -->
            <property name="param1" value="value1"/>
        </plugin>
    </plugins>
```

test:
```java
    EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
    Page<Object> page = PageHelper.startPage(5, 1);
    List<Employee> emps = mapper.getEmps();
    // PageInfo<Employee> employeePageInfo = new PageInfo<>(emps);
    // 连续显示 5 页
    PageInfo<Employee> employeePageInfo = new PageInfo<>(emps, 5);
    for (Employee employee : emps) {
        System.out.println(employee);
    }
    // System.out.println("总记录数：" + page.getTotal());
    // System.out.println("每页记录数：" + page.getPageSize());
    // System.out.println("当前页码：" + page.getPageNum());
    // System.out.println("总共页数：" + page.getPages());
    System.out.println("总记录数：" + employeePageInfo.getTotal());
    System.out.println("每页记录数：" + employeePageInfo.getPageSize());
    System.out.println("当前页码：" + employeePageInfo.getPageNum());
    System.out.println("总共页数：" + employeePageInfo.getPages());
    System.out.println("是否是第一页：" + employeePageInfo.isIsFirstPage());
    System.out.println("连续显示的页码：");
    int[] navigatepageNums = employeePageInfo.getNavigatepageNums();
    for (int navigatepageNum : navigatepageNums) {
        System.out.println(navigatepageNum);
    }
```

## BatchExecutor
默认的 openSession() 方法没有参数，有如下特性：
- 会开启一个事务(也就是不自动提交)
- 连接对象会从由活动环境配置的数据源实例得到。
- 事务隔离级别将会使用驱动或数据源的默认设置。
- 预处理语句不会被复用,也不会批量处理更新。

openSession 方法的 ExecutorType 类型的参数，枚举类型:
- ExecutorType.SIMPLE: 这个执行器类型不做特殊的事情（这是默认装配的）
    - 它为每个语句的执行创建一个新的预处理语句
- ExecutorType.REUSE: 这个执行器类型会复用预处理语句
- ExecutorType.BATCH: 这个执行器会批量执行所有更新语句

```java
    public void test01() {
        SqlSession openSession = build.openSession(ExecutorType.BATCH);
        UserDao mapper = openSession.getMapper(UserDao.class);
        long start = System.currentTimeMillis();
        for (int i = 0; i < 1000000; i++) {
            String name = UUID.randomUUID().toString().substring(0, 5);
            mapper.addUser(new User(null, name, 13));
        }
        openSession.commit();
        openSession.close();
        long end = System.currentTimeMillis();
        System.out.println("耗时时间："+(end-start));
    }
```

### Spring 整合
```xml
    <!-- 配置一个可以进行批量进行的 sqlSession -->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg name="sqlSessionFactory" ref="sessionFactoryBean" />
        <constructor-arg name="executorType" value="BATCH" />
    </bean>
```
```java
    @Autowired
    private SqlSession sqlSession;
```
> 注意：
> - 批量操作是在 session.commit() 以后才发送 sql 语句给数据库进行执行的
> - 如果我们想让其提前执行，以方便后续可能的查询操作获取数据
>   - 我们可以使用 sqlSession.flushStatements() 方法，让其直接冲刷到数据库进行执行

## 存储过程
> 在 Oracle 中使用存储过程

## 自定义 TypeHandler 处理枚举类型
> 我们可以通过自定义TypeHandler的形式来在设置参数或者取出结果集的时候自定义参数封装策略 

步骤：
- 实现TypeHandler接口或者继承BaseTypeHandler
- 使用@MappedTypes定义处理的java类型
    - 使用@MappedJdbcTypes定义jdbcType类型
- 在自定义结果集标签或者参数处理的时候声明使用自定义TypeHandler进行处理
    - 或者在全局配置TypeHandler要处理的javaType

enum:
```java
    public enum EmpStatus {
        /**
        * 员工状态
        * 希望数据库保存的是状态码
        */
        LOGIN(100, "用户登录"), LOGOUT(200, "用户登出"), REMOVE(300, "用户不存在");

        private Integer code;
        private String msg;

        EmpStatus(Integer code, String msg) {
            this.code = code;
            this.msg = msg;
        }

        public Integer getCode() {
            return code;
        }

        public void setCode(Integer code) {
            this.code = code;
        }

        public String getMsg() {
            return msg;
        }

        public void setMsg(String msg) {
            this.msg = msg;
        }

        // 按照状态码返回枚举对象
        public static EmpStatus geEmpStatusByCode(Integer code) {
            EmpStatus empStatus = null;
            switch (code) {
                case 100:
                    empStatus = LOGIN;
                    break;
                case 200:
                    empStatus = LOGOUT;
                    break;
                case 300:
                    empStatus = REMOVE;
                    break;
                default:
                    empStatus = LOGOUT;
            }
            return empStatus;
        }
    }
```

mybatis-config.xml:
```xml
    <typeHandlers>
        <!-- 1. 配置我们自定义的 TypeHandler -->
        <typeHandler handler="org.rabbit.mybatis.typeHandler.MyEnumEmpStatusTypeHandler" javaType="org.rabbit.mybatis.bean.EmpStatus" />
        <!-- 2. 在处理某个字段时设置用什么类型处理器 -->
        <!--
            保存：#{empStatus, typeHandler=xxx}
            查询：
                <resultMap id="MyEmp" type="org.rabbit.mybatis.bean.Employee">
                    <id column="id" property="id" />
                    <result column="empStatus" property="empStatus" typeHandler="" />
                </resultMap>
        -->
    <!--		<typeHandler handler="org.apache.ibatis.type.EnumOrdinalTypeHandler" javaType="org.rabbit.mybatis.bean.EmpStatus" />-->
    </typeHandlers>
```

test:
```java
    /**
     * 默认 MyBatis 在处理枚举对象的时候保存到是枚举的名字：EnumTypeHandler
     * 改变使用：EnumOrdinalTypeHandler
     */
    @Test
    public void testEnum() throws IOException {
        SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
        SqlSession sqlSession = sqlSessionFactory.openSession();
        try {
            EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
//            mapper.addEmp(new Employee("test_num", "enum@gmail.com", "1"));
//            sqlSession.commit();
            Employee empById = mapper.getEmpById(1028);
            System.out.println(empById.getEmpStatus());

        } finally {
            sqlSession.close();
        }
    }
```