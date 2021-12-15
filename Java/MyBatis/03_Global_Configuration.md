# Global Configuration
## 配置文件结构
- configuration 配置
    - properties 属性
    - settings 设置
    - typeAliases 类型命名
    - typeHandlers 类型处理器
    - objectFactory 对象工厂
    - plugins 插件
    - environments 环境
        - environment 环境变量
            - transactionManager 事务管理器
            - dataSource 数据源
    - databaseIdProvider 数据库厂商标识
    - mappers 映射器

## 引入 dtd 约束文件，方便编写 xml 提示
```xml
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
```

### properties
> 用来引入 properties 配置文件的内容
- resource: 引入类路径下的资源
- url: 引入网络路径或磁盘路径下的资源

dbConfig.properties:
```properties
    jdbc.driver=com.mysql.cj.jdbc.Driver
    jdbc.url=jdbc:mysql://localhost:3306/mybatis?userUnicode=true&characterEncoding=utf8
    jdbc.username=root
    jdbc.password=root
```

```xml
    <properties resource="dbConfig.properties" />
```

> 如果属性在不只一个地方进行了配置，那么 MyBatis 将按照下面的顺序来加载：
> - 在 properties 元素体内指定的属性首先被读取。
> - 然后根据 properties 元素中的 resource 属性读取类路径下属性文件
>   或根据 url 属性指定的路径读取属性文件，并覆盖已读取的同名属性。
> - 最后读取作为方法参数传递的属性，并覆盖已读取的同名属性。

### settings
> 包含很多重要的设置项，会改变 Mybatis 运行时行为
- setting: 用来设置每一个设置项
    - name: 设置项名称
    - value: 设置项取值

```xml
	<settings>
		<setting name="mapUnderscoreToCamelCase" value="true" />
	</settings>
```

**部分设置项**
| 设置参数 | 描述 | 有效值 | 默认值 |
| --- | --- | --- | --- |
| cacheEnabled | 该设置影响所有映射器中配置**缓存**的全局开关 | true \| false | true |
| lazyLoadingEnabled | 延迟加载的全局开关。**当开启时，所有关联对象都会延迟加载**。特定关联关系中可通过设置 fetchType 属性来覆盖该项的开关状态 | true \| false | false |
| userColumnLabel | **使用列标签代替列名**。不同的驱动在这方面会有不同的表现，具体可参考相关驱动文档或通过测试这两种不同的模式来观察所用驱动的结果 | true \| false | true |
| defaultStatementTimeout | **设置超时时间**，它决定驱动等待数据库响应的秒数 | true \| false | not set(null) |
| mapUndersocreToCamelCase | 是否开启自动驼峰命名规则（camel case）映射，即从经典数据库列名 a_column 到经典 java 属性名 aColumn 的类似映射 | true \| false | false |

### typeAliases
> 类型别名，可以我们的 java 类型起别名
> - 方便我们引用某个类
> - 别名不区分大小写

```xml
	<typeAliases>
		<!--
			1. typeAlias: 为某个 java 类型起别名
				type: 指定要起别名的类型的全类名，默认别名就是类名小写：employee
				alias: 指定新的别名，可省略
        -->
<!--        <typeAlias type="org.rabbit.mybatis.bean.Employee" />-->

		<!--
			2. package: 为某个包下的所有类批量起别名
				name: 指定包名（为当前包以及下面所有后代包的每一个类都起一个默认别名（类名小写））
		-->
		<package name="org.rabbit.mybatis.bean" />
		<!-- 3. 批量起别名的情况下 使用 @Alias 注解为某个类型指定新的类名 -->
		<!-- 4. 不推荐起别名... -->
	</typeAliases>
```

### typeHandlers
> 类型处理器

无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时， 都会用类型处理器将获取的值以合适的方式转换成 Java 类型。

#### 日期类型的处理
- 日期和时间的处理，JDK1.8以前一直是个头疼的问题。我们通常使用JSR310规范领导者Stephen Colebourne创建的Joda-Time来操作。1.8已经实现全部的JSR310规范了。
- 日期时间处理上，我们可以使用MyBatis基于 JSR310（Date and Time API）编写的各种日期时间类型处理器。
- MyBatis3.4以前的版本需要我们手动注册这些处理器，以后的版本都是自动注册的

#### 自定义类型处理器
- 我们可以重写类型处理器或创建自己的类型处理
器来处理不支持的或非标准的类型。
- 步骤：
    1. 实现 org.apache.ibatis.type.TypeHandler 接口或者继承 org.apache.ibatis.type.BaseTypeHandler
    2. 指定其映射某个 JDBC 类型（可选操作）
    3. 在 mybatis 全局配置文件中注册

### plugins
> 插件是MyBatis提供的一个非常强大的机制，我们可以通过插件来修改MyBatis的一些核心行为。插件通过动态代理机制，可以介入四大对象的任何一个方法的执行。

• Executor (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed)
• ParameterHandler (getParameterObject, setParameters) 
• ResultSetHandler (handleResultSets, handleOutputParameters) 
• StatementHandler (prepare, parameterize, batch, update, query) 

### environments
> 环境配置项 可以配置多种环境 default 指定使用某种环境，可以达到快速切换环境

#### environment
- id: 指定当前环境的唯一标识
- 必须有 **transactionManager**, **dataSource**
```xml
	<environments default="dev_mysql">
		<environment id="dev_mysql">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}" />
				<property name="url" value="${jdbc.url}" />
				<property name="username" value="${jdbc.username}" />
				<property name="password" value="${jdbc.password}" />
			</dataSource>
		</environment>
		<environment id="dev_oracle">
			<transactionManager type="JDBC" />
			<dataSource type="POOLED">
				<property name="driver" value="${orcl.driver}" />
				<property name="url" value="${orcl.url}" />
				<property name="username" value="${orcl.username}" />
				<property name="password" value="${orcl.password}" />
			</dataSource>
		</environment>
	</environments>

```

#### transactionManager
- type： JDBC | MANAGED | 自定义
    - JDBC：使用了 JDBC 的提交和回滚设置，依赖于从数
    据源得到的连接来管理事务范围。
    JdbcTransactionFactory
    - MANAGED：不提交或回滚一个连接、让容器来管理
    事务的整个生命周期（比如 JEE 应用服务器的上下
    文） ManagedTransactionFactory
    - 自定义：实现TransactionFactory接口，type=全类名/别名

#### dataSource
- type： UNPOOLED | POOLED | JNDI | 自定义
    - UNPOOLED：不使用连接池，
    UnpooledDataSourceFactory
    - POOLED：使用连接池， PooledDataSourceFactory
    - JNDI： 在EJB 或应用服务器这类容器中查找指定的数
    据源
    - 自定义：实现DataSourceFactory接口，定义数据源的获取方式。


> **实际开发中我们使用Spring管理数据源，并进行事务控制的配置来覆盖上述配置**

### databaseIdProvider
> 作用就是得到数据库厂商的标识（驱动 getDatabaseProductName()），MyBatis 就能根据数据库厂商标识来执行不同的 sql 语句

- type： DB_VENDOR
    - 使用MyBatis提供的VendorDatabaseIdProvider解析数据库厂商标识。也可以实现DatabaseIdProvider接口来自定义。
- Property-name：数据库厂商标识
- Property-value：为标识起一个别名，方便SQL语句使用 databaseId 属性引用

```xml
	<databaseIdProvider type="DB_VENDOR">
	<!-- 为不同的数据库厂商起别名 -->
		<property name="MySQL" value="mysql" />
		<property name="Oracle" value="oracle" />
		<property name="SQL Server" value="sqlserver" />

	</databaseIdProvider>
```

> DB_VENDOR
> - 会通过 DatabaseMetaData#getDatabaseProductName() 返回的字符串进行设置。由于通常情况下这个字符串都非常长而且相同产品的不同版本会返回不同的值，所以最好通过设置属性别名来使其变短
> - MyBatis匹配规则如下：
>     1. 如果没有配置databaseIdProvider标签，那么databaseId=null
>     2. 如果配置了databaseIdProvider标签，使用标签配置的name去匹配数据库信息，匹配上设置databaseId=配置指定的值，否则依旧为 null
>     3. 如果databaseId不为null，他只会找到配置databaseId的sql语句
>     4. MyBatis 会加载不带 databaseId 属性和带有匹配当前数据库
>     databaseId 属性的所有语句。如果同时找到带有 databaseId 和不带 databaseId 的相同语句，则后者会被舍弃

### mappers
> 将 sql 映射注册到全局配置中

- mapper: 注册一个 sql 映射
    - resource: 引用类路径下的 sql 映射文件
    - url: 引用网络路径或磁盘路径下的 sql 映射文件
    - class: 引用（注册）接口

```xml
	<mappers>
		<mapper resource="mybatisMapper/EmployeeMapper.xml" />
	</mappers>
```

- 注册接口：
    - class: 引用（注册）接口
    1. 有 sql 映射文件，映射文件名必须和接口同名，并且放在与接口同一目录下
    2. 没有 sql 映射文件，所有 sql 语句都是利用注解写在接口上
    总结：
        - 比较重要的，复杂的 Dao 接口 -> 写 sql 映射文件
        - 不重要的，简单的 Dao 接口  -> 为了开发快速可以使用注解

```xml
	<mappers>
		<mapper class="org.rabbit.mybatis.dao.EmployeeMapperAnnotation" />
	</mappers>
```

- 批量注册：
```xml
	<mappers>
		<!-- 批量注册 -->
		<package name="org.rabbit.mybatis.dao"/>
	</mappers>
```