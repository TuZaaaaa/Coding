[Toc]
# Transaction
> 事务是数据库操作最基本单元，逻辑上一组操作，
> 要么都成功，如果有一个失败所有操作都失败

## 典型场景：银行转账
- lucy 转账 100 元 给 mary
- lucy 少 100，mary 多 100

## 事务四个特性（ACID）
1. 原子性
2. 一致性
3. 隔离性
4. 持久性

## 搭建事务操作环境
- Service 业务操作
  - 创建转账的方法
    - 调用 dao 两个的方法
- Dao 数据库操作 不写业务操作
  - 创建两个方法
    - 少钱的方法
    - 多钱的方法

### 创建数据库表，添加记录
### 搭建 service，搭建 dao，完成对象创建和注入关系
#### service 注入 dao，在 dao 注入 JdbcTemplate，在 JdbcTemplate 注入 DataSource
```java
@Service
    public class UserService {
        //注入 dao
        @Autowired
        private UserDao userDao;
    }
    @Repository
    public class UserDaoImpl implements UserDao {
        @Autowired
        private JdbcTemplate jdbcTemplate;
    }
```

#### dao 创建两个方法：多钱和少钱的方法，在 service 创建方法（转账的方法）
```java
    @Repository
    public class UserDaoImpl implements UserDao {
        @Autowired
        private JdbcTemplate jdbcTemplate;
        //lucy 转账 100 给 mary
        //少钱
        @Override
        public void reduceMoney() {
            String sql = "update t_account set money=money-? where username=?";
            jdbcTemplate.update(sql,100,"lucy");
        }
        //多钱
        @Override
        public void addMoney() {
            String sql = "update t_account set money=money+? where username=?";
            jdbcTemplate.update(sql,100,"mary");
        }
    }

    @Service
    public class UserService {
        //注入 dao
        @Autowired
        private UserDao userDao;
        //转账的方法
        public void accountMoney() {
        //lucy 少 100
        userDao.reduceMoney();
        //mary 多 100
        userDao.addMoney();
        }
    }
```

4. 上面代码，如果正常执行没有问题的，但是如果代码执行过程中出现异常，有问题
- 用除以 0，模拟异常
- 第一步，开启事务
- 第二步，进行业务操作
- 第三步，没有发生异常，提交事务
- 第四步，出现异常，事务回滚

## Spring 事务管理
1. 事务添加到 JavaEE 三层结构里面 Service 层（业务逻辑层）
2. 在 Spring 进行事务管理操作
    - 有两种方式：编程式事务管理和声明式事务管理（使用）
3. 声明式事务管理
    - 基于注解方式（使用）
    - 基于 xml 配置文件方式
4. 在 Spring 进行声明式事务管理，底层使用 AOP 原理
5. Spring 事务管理 API
    - 提供一个接口，代表事务管理器，这个接口针对不同的框架提供不同的实现类

## 注解声明事务管理
1. 在 spring 配置文件配置事务管理器
```xml
    <!--创建事务管理器-->
    <bean id="transactionManager" 
    class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>
```

2. 在 spring 配置文件，开启事务注解
    1. 在 spring 配置文件引入名称空间 tx
        ```java
            <beans xmlns="http://www.springframework.org/schema/beans" 
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
            xmlns:context="http://www.springframework.org/schema/context" 
            xmlns:aop="http://www.springframework.org/schema/aop" 
            xmlns:tx="http://www.springframework.org/schema/tx" 
            xsi:schemaLocation="http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd 
            http://www.springframework.org/schema/context 
            http://www.springframework.org/schema/context/spring-context.xsd 
            http://www.springframework.org/schema/aop 
            http://www.springframework.org/schema/aop/spring-aop.xsd 
            http://www.springframework.org/schema/tx 
            http://www.springframework.org/schema/tx/spring-tx.xsd">
        ```
    2. 开启事务注解
        ```xml
            <!--开启事务注解-->
            <tx:annotation-driven transactionmanager="transactionManager"></tx:annotation-driven>
        ```
3. 在 service 类上面（或者 service 类里面方法上面）添加事务注解
- @Transactional，这个注解添加到类上面，也可以添加方法上面
- 如果把这个注解添加类上面，这个类里面所有的方法都添加事务
- 如果把这个注解添加方法上面，为这个方法添加事务
```java
    @Service
    @Transactional
    public class UserService {
```

## 声明式事务管理参数配置
1. 在 service 类上面添加注解@Transactional，在这个注解里面可以配置事务相关参数

2. propagation：事务传播行为
- 多事务方法直接进行调用，这个过程中事务 是如何进行管理的
- 事务方法：对数据库表数据进行变化的操作

- Spring 框架事务传播行为有 7 种
...

3. ioslation：事务隔离级别
    1. 事务有特性成为隔离性，多事务操作之间不会产生影响。不考虑隔离性产生很多问题
    2. 有三个读问题：脏读、不可重复读、虚（幻）读
        1. 脏读：一个未提交事务读取到另一个未提交事务的数据
        2. 不可重复读：一个未提交事务读取到另一提交事务修改数据
        3. 虚读：一个未提交事务读取到另一提交事务添加数据

> 解决：通过设置事务隔离级别，解决读问题
...

4. timeout：超时时间
    - 事务需要在一定时间内进行提交，如果不提交进行回滚
    - 默认值是 -1 ，设置时间以秒单位进行计算
5. readOnly：是否只读
    - 读：查询操作，写：添加修改删除操作
    - readOnly 默认值 false，表示可以查询，可以添加修改删除操作
    - 设置 readOnly 值是 true，设置成 true 之后，只能查询
6. rollbackFor：回滚
    - 设置出现哪些异常进行事务回滚
7. noRollbackFor：不回滚
    - 设置出现哪些异常不进行事务回滚

## XML 声明式事务管理
> 在 spring 配置文件中进行配置

- 配置事务管理器
- 配置通知
- 配置切入点和切面
```xml
    <!--1 创建事务管理器-->
    <bean id="transactionManager" 
        class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!--2 配置通知-->
    <tx:advice id="txadvice">
        <!--配置事务参数-->
        <tx:attributes>
        <!--指定哪种规则的方法上面添加事务-->
        <tx:method name="accountMoney" propagation="REQUIRED"/>
        <!--<tx:method name="account*"/>-->
        </tx:attributes>
    </tx:advice>
    <!--3 配置切入点和切面-->
    <aop:config>
        <!--配置切入点-->
        <aop:pointcut id="pt" expression="execution(* 
        com.atguigu.spring5.service.UserService.*(..))"/>
        <!--配置切面-->
        <aop:advisor advice-ref="txadvice" pointcut-ref="pt"/>
    </aop:config>
```