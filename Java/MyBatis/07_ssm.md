# Spring 框架的整合
- 查看不同MyBatis版本整合Spring时使用的适配包：http://www.mybatis.org/spring/
- 下载整合适配包
```xml
    <dependencies>
        <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.3.14</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework/spring-web -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>5.3.14</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework/spring-web -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>5.3.14</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.14</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework/spring-beans -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>5.3.14</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.14</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.27</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/com.mchange/c3p0 -->
        <dependency>
            <groupId>com.mchange</groupId>
            <artifactId>c3p0</artifactId>
            <version>0.9.5.5</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.8</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>2.0.6</version>
        </dependency>
    </dependencies>
```
- 官方整合示例，jpetstore https://github.com/mybatis/jpetstore-6

## 整合关键配置
- 创建 SqlSessionFactory 对象
```xml
    <bean id="sessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <!-- configLocation: 指定全局配置文件的位置 -->
        <property name="configLocation" value="classpath:mybatis-config.xml" />
        <!-- mapperLocations: 指定 mapper 文件的位置 -->
        <property name="mapperLocations" value="classpath:mybatisMapper/*.xml" />
    </bean>
```

- mapper 自动注入
```xml
    <!--
        扫描所有的 mapper 接口的实现，让这些 mapper 可以自动注入
            base-package: 指定 mapper 接口的包名
    -->
    <!-- 方式一  -->
    <mybatis-spring:scan base-package="org.rabbit.mybatis.dao" />

    <!-- 方式二  -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="org.rabbit.mybatis.dao" />
    </bean>
```