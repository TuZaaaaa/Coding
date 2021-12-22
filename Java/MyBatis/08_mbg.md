# 逆向工程（MyBatis Generator）
> 是一个专门为MyBatis框架使用者定制的代码生成器，可以快速的根据表生成对应的映射文件，接口，以及bean类
> 支持基本的增删改查，以及QBC风格的条件查询。但是表连接、 存储过程等这些复杂sql的定义需要我们手工编写 
- 官方文档地址
  - http://mybatis.org/generator/index.html 

## 使用步骤：
1. 编写 MBG 的配置文件（重要几处配置）
    1. jdbcConnection: 配置数据库连接信息
        1. javaModelGenerator: 配置 javaBean 的生成策略
        2. sqlMapGenerator: 配置 sql 映射文件生成策略
        3. javaClientGenerator: 配置 Mapper 接口的生成策略
        4. table 配置要逆向解析的数据表
            - tableName: 表名
            - domainObjectName: 对应的 javaBean
```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE generatorConfiguration
            PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
            "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
    <generatorConfiguration>

        <!--
            targetRuntime="MyBatis3Simple":生成简单版的 CRUD
            MyBatis3:豪华版

        -->
        <context id="DB2Tables" targetRuntime="MyBatis3">
            <!-- jdbcConnection：指定如何连接到目标数据库 -->
            <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                            connectionURL="jdbc:mysql://localhost:3306/mybatis?allowMultiQueries=true"
                            userId="root"
                            password="root">
            </jdbcConnection>

            <!-- 类型解析器 -->
            <javaTypeResolver >
                <property name="forceBigDecimals" value="false" />
            </javaTypeResolver>

            <!--
                javaModelGenerator：指定 javaBean 的生成策略
                    targetPackage="test.model"：目标包名
                    targetProject="\MBGTestProject\src"：目标工程
            -->
            <javaModelGenerator targetPackage="org.rabbit.mybatis.bean"
                                targetProject="D:\MyBatisDemo\MyBatis-demo7\src\main\java">
                <property name="enableSubPackages" value="true" />
                <property name="trimStrings" value="true" />
            </javaModelGenerator>

            <!-- sqlMapGenerator: sql 映射生成策略 -->
            <sqlMapGenerator targetPackage="mybatisMapper"
                            targetProject="D:\MyBatisDemo\MyBatis-demo7\src\main\resources">
                <property name="enableSubPackages" value="true" />
            </sqlMapGenerator>

            <!-- javaClientGenerator: 指定 mapper 接口所在的位置 -->
            <javaClientGenerator type="XMLMAPPER" targetPackage="org.rabbit.mybatis.dao"
                                targetProject="D:\MyBatisDemo\MyBatis-demo7\src\main\java">
                <property name="enableSubPackages" value="true" />
            </javaClientGenerator>

            <!-- 指定要逆向分析哪些表：根据表创建 javaBean -->
            <table tableName="tbl_employee" domainObjectName="Employee" />
            <table tableName="tbl_dept" domainObjectName="Department" />
        </context>
    </generatorConfiguration>
```

> **idea 环境中部分路径需要使用绝对路径，否则需要配置文件的目录等**

2. 运行代码生成器生成代码
```java
    List<String> warnings = new ArrayList<>();
    boolean overwrite = true;
    File configFile = new File("src/main/resources/mbg.xml");
    ConfigurationParser cp = new ConfigurationParser(warnings);
    Configuration config = cp.parseConfiguration(configFile);
    DefaultShellCallback callback = new DefaultShellCallback(overwrite);
    MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
    myBatisGenerator.generate(null);
```

> 注意：
> Context标签
> targetRuntime="MyBatis3"可以生成带条件的增删改查
> targetRuntime="MyBatis3Simple"可以生成基本的增删改查
> 如果再次生成，建议将之前生成的数据删除，避免 xml 向后追加内容出现的问题

## QBC(Query By Criteria) 风格查询
```java
    @Test
    public void testMyBatis3() throws IOException {
        SqlSessionFactory sqlSessionFactory = getSqlSessionFactory();
        SqlSession sqlSession = sqlSessionFactory.openSession();
        try {
            EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
            // xxxExample() 封装查询条件
//            List<Employee> employees = mapper.selectByExample(null);
            // 查询员工中名字中带有 e 字母，和员工性别是 1 的
            // 封装员工查询条件
            EmployeeExample employeeExample = new EmployeeExample();
            // Criteria 用于拼装查询条件
            EmployeeExample.Criteria criteria = employeeExample.createCriteria();
            criteria.andLastNameLike("%e%");
            criteria.andGenderEqualTo("1");
            EmployeeExample.Criteria criteria1 = employeeExample.createCriteria();
            criteria1.andEmailLike("%4%");
            employeeExample.or(criteria1);
            List<Employee> employees = mapper.selectByExample(employeeExample);
            for (Employee employee : employees) {
                System.out.println(employee.getId());
            }
        } finally {
            sqlSession.close();
        }
    }
```
