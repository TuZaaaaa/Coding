# 工作原理
## SQLSessionFactory 初始化
1. 创建 SQLSessionFactoryBuilder 对象
    - SqlSessionFactoryBuilder
2. build(inputStream)
3. 创建解析器 parser
    - XmlConfigBuilder
4. 解析每一个标签，并将详细信息保存在 Configuration 中
    - Configuration 对象
      - 保存了所有配置文件的详细信息
5. 解析 mapper.xml
    - 每一个元素信息解析出来并保存在全局配置中
    - 将增删改查标签的每一个标签每一个属性都解析出来，封装成一个 MappedStatement
      - MappedStatement: 代表一个增删改查的详细信息
6. 返回 Configuration
7. build(Configuration)
    - DefaultSqlSessionFactory
8. new DefaultSqlSession()
9. 返回创建的 DefaultSqlSession 返回封装了全局配置信息的 Configuration
> 总结：
> - 将配置文件的信息并保存在 Configuration 对象中
> - 返回 DefaultSqlSession 对象

## openSession 获取 SqlSession 对象
1. openSession()
    - DefaultSqlSessionFactory
    - openSessionFromDataSource()
2. 获取一些信息，创建 tx（事务相关）
3. **newExecutor()**
    - 根据 Executor 在全局配置中的类型，创建出 Simple/Reuse/Batch Executor
4. 如果有二级缓存配置开启，创建 CachingExecutor(executor)
5. executor= (Executor)interceptorChain.pluginAll(executor)
    - 使用每一个拦截器重新包装 executor 并返回
6. 创建 DefaultSqlSession 对象，包含 Configuration 和 Executor 对象
7. 返回 DefaultSqlSession 对象
> 总结：
> - 返回 SqlSession 实现类 DefaultSqlSession 对象
>   - 包含 Executor 和 Configuration
> - Executor 会在这一步创建

## getMapper 获取接口的代理对象
1. getMapper(type)
2. MapperRegistry.getMapper(type)
3. 根据接口类型获取 MapperProxyFactory
    - new Instance(sqlSession)
4. 创建 MapperProxy
    - 实现了 InvocationHandler，能够创建动态代理
5. 创建 MapperProxy 的代理对象
6. 返回 MapperProxy 的代理对象
    - 包含了 SqlSession 对象

## 查询实现
1. invoke()
 - MapperMethod
2. 判断增删改查类型
3. 包装参数为一个 map 或直接返回
4. sqlSession.selectOne()
    - selectList()
    1. 获取 MappedStatement
    2. 调用 executor.query()
        1. getBoundSql()
            - BoundSql 封装了 sql 语句的详细信息
        2. 如果开启二级缓存，createrCachekey() CacheKey 缓存 key
            - 方法 id + sql 语句 + 参数信息 xxx
    3. 调用 **SimpleExecutor** executor.query()
    4. 查看本地缓存是否有数据，没有就调用 queryFromDatabase()
        - 查出以后也会保存在本地缓存中
        - 调用 baseExecutor() doQuery()
            - 创建 StatementHandler 对象，用于创建 Statement 对象
              - 创建 PreparedStatementHandler 对象
                - (StatementHandler)interceptorChain.pluginAll(statementHandler)
                - 创建 ParameterHandler interceptorChain.pluginAll(parameterHandler)
                - 创建 ResultsetHandler interceptorChain.pluginAll(resultHandler)
                    - 预编译 sql，产生 PreparedStatement 对象
                        -  调用 ParameterHandler 设置参数
                            - 调用 TypeHandler 为 sql 语句设置预编译参数
                            - 查询到的数据使用 ResultSetHandler 对象处理结果
                                - 使用 TypeHandler 获取 value
    5. 后续的连接关闭等流程
5. 返回 list 的第一个结果
> 查询流程总结：
> 代理对象
>   - DefaultSqlSession
>   - Executor
>   - StatementHandler
>       - 处理 sql 语句预编译，设置参数等相关工作
>       - ParameterHandler
>           - 设置预编译参数
>       - ResultsetHandler 
>           - 处理查询后的结果集
>   - TypeHandler: 在整个过程中，进行数据库类型和 JavaBean 类型的映射
>       - DefaultParameterHandler: 设置参数
>           - typeHandler.setParameter(ps, i + 1, value, jdbcType);
>       - DefaultResultSetHandler: 处理结果
>           - typeHandler.getResult(rs, column);

## 运行流程总结
1. 根据配置文件（全局，sql 映射）初始化 Configuration 对象
2. 创建一个 DefaultSqlSession 对象
    - 包含 Configuration 以及
    - Executor（根据全局配置文件中的 defaultExecutorType 创建出对应的 Executor）
3. DefaultSqlSession.getMapper(): 获得 Mapper 接口对应的 MapperProxy
4. MapperProxy 里面有（DefaultSqlSession）
5. 执行增删改查方法：
    1. 调用 DefaultSqlSession 的增删改查方法（Executor）
    2. 创建一个 Statement 对象
        - 同时也会创建 ParameterHandler 和 ResultSetHandler
    3. 调用 StatementHandler 的预编译参数以及设置参数值
        - 使用 ParameterHandler 来给 sql 设置参数
    4. 调用 StatementHandler 的增删改查方法
    5. ResultSetHandler 封装结果
> 注意：
> 四大对象每次创建的时候都会调用
>   - interceptorChain.pluginAll(parameterHandler)