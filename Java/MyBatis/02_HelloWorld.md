# Hello World
- HelloWorld简单版
    - 创建一张测试表
    - 创建对应的javaBean
    - 创建mybatis配置文件，sql映射文件
    - 测试

## MyBatis 操作数据库
> 1. 根据 xml 配置文件（全局配置文件），创建一个 SqlSessionFactory 对象，有数据源一些运行环境信息
> 2. sql 映射文件：配置了每一个 sql，以及 sql 的封装规则等
> 3. 将 sql 映射文件注册在全局配置文件中
> 4. 编写程序：
>   - 根据全局配置文件得到 SqlSessionFactory
>   - 使用 sqlSession 工厂，获取到 SqlSession 对象，使用其执行增删改查
>       - 一个 Session 代表和数据库的一次会话，使用完毕需要关闭
>   - 使用 sql 的唯一标识，指定执行哪个 sql 语句（sql 映射文件中）


### 测试
1. 根据全局配置文件，利用 **SqlSessionFactoryBuilder** 创建 **SqlSessionFactory**
```java
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessinoFactory factory = new SqlSessionFActoryBuilder().build(inputStream);
```

2. 使用 **SqlSessionFactory** 获取 **SqlSession** 对象。一个 **SqlSession** 对象代表和数据库的一次会话
```java
    SqlSession openSession = factory.openSession();
```

## 接口式编程（推荐）
- 创建一个 Dao 接口
- 修改 Mapper 文件
- 测试
> 接口式编程
> - 原生       Dao     ->   DaoImpl
> - MyBatis   Mapper  ->   xxMapper.xml
> - mapper 接口没有实现类，但是 MyBatis 会为这个接口生成一个代理对象
>   将接口和 xml 进行绑定
> - 两个重要的配置文件
>    - MyBatis 的全局配置文件：包含数据库连接信息，事务管理信息，系统运行环境信息等
>    - sql 映射文件：保存了每一个 sql 语句的映射信息
>       - 将 sql 抽取出来



### 使用 SqlSession 获取映射器进行操作
```java
    SqlSession sqlSession = factory.openSession();
    try {
        EmployeeMapper mapper = sqlSession.getMapper(EmployeeMapper.class);
        Employee employee = mapper.getEmpById(1);
        System.out.println(employee);
    } finally {
        sqlSession.close();
    }
```

### SqlSession
- SqlSession 的实例不是线程安全的，因此是不能被共享的。
- SqlSession 每次使用完成后需要正确关闭，这个关闭操作是必须的
- SqlSession 可以直接调用方法的id进行数据库操作，但是我们一般还是推荐使用 SqlSession 获取到 Dao 接口的代理类，执行代理对象的方法，可以更安全的进行类型检查操作
