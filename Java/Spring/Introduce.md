# Spring
## 课程内容介绍
1. Spring 框架概述
2. IOC 容器
   - 底层原理
   - IOC 接口（BeanFactory）
   - IOC 操作 Bean 管理（基于 xml）
   - IOC 操作 Bean 管理（基于注解）
3. Aop
4. JdbcTemplate
5. 事务管理
6. Spring5 新特性

## Spring5 框架概述
1. Spring 是轻量级的开源的 JavaEE 框架
2. Spring 可以解决企业应用开发的复杂性
3. Spring 有两个核心部分：IOC 和 Aop
   - IOC：控制反转，把创建对象过程交给 Spring 进行管理
   - Aop：面向切面，不修改源代码进行功能增强
4. Spring 特点
5. 方便解耦，简化开发
6. Aop 编程支持
7. 方便程序测试
8. 方便和其他框架进行整合
9.  方便进行事务操作
10. 降低 API 开发难度

## 简单使用
### 创建普通类，在这个类创建普通方法
```java
    public class User {
        public void add() {
            System.out.println("add.....");
        }
    }
```

### 创建 Spring 配置文件，在配置文件中配置创建的对象
1. Spring 配置文件使用 xml 格式
```xml
    <!--配置 User 对象创建-->
    <bean id="user" class="org.rabbit.User"/>
```

### 进行测试代码编写
```java
    @Test
    public void testAdd() {
        // 1.加载 spring 配置文件
        ApplicationContext context = new ClassPathXmlApplicationContext("bean1.xml");
        // 2.获取配置创建的对象
        User user = context.getBean("user", User.class);
        System.out.println(user);
        user.add();
    }
```

