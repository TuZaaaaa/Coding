# cache
> MyBatis 包含一个非常强大的查询缓存特性,它可以非常方便地配置和定制，缓存可以极大的提升查询效率 
> 系统默认定义了两级缓存

## 一级缓存
> 一级缓存（本地缓存） sqlSession 级别的缓存（sqlSession 级别的一个 Map），默认开启
>    与数据库同一次会话期间查询到的数据会放在本地缓存中，以后如果需要获取相同的数据，直接从缓存中获取，不需要查询数据库

### 失效情况
>（没有使用到一级缓存时，需要再次向数据库发送请求查询）
1. sqlSession 不同
```java
    SqlSession sqlSession1 = sqlSessionFactory.openSession();
    EmployeeMapper mapper1 = sqlSession1.getMapper(EmployeeMapper.class);
    Employee empById2 = mapper1.getEmpById(1000);
    System.out.println(empById2);
    System.out.println(empById==empById2);// false
```
2. sqlSession 相同，查询条件不同（当前一级缓存中还没有这个数据）
```java
    Employee empById1 = mapper.getEmpById(3);
    System.out.println(empById1);
    System.out.println(empById==empById1);
```
3. sqlSession 相同，两次查询之间执行了增删改操作（可能会对当前的数据有影响）
```java
    mapper.addEmp(new Employee(null, "snake", "0", "snake@gmail.com"));
    sqlSession.commit();
```
4. sqlSession 相同，手动清除了一级缓存（缓存清空）
```java
    sqlSession.clearCache();
```

## 二级缓存
> 全局缓存 基于 namespace 级别的缓存，一个 namespace 对应一个二级缓存
- 工作机制：
    1. 一个会话，查询一条数据，这个数据会被放在当前会话的一级缓存中
    2. 如果会话关闭，一级缓存的数据会被保存到二级缓存中，新的会话查询信息，就可以参照二级缓存
    1. sqlSession === EmployeeMapper === Employee
                    DepartmentMapper == Department
        不同 namespace 查出的数据会放在自己对应的缓存中（map）
        **效果**：数据会从二级缓存中获取
            查出的数据都会被默认先放在一级缓存中
            只有会话提交或者关闭以后，一级缓存中的数据才会转移到二级缓存中
- 使用：
    1. 开启二级缓存配置
        `<setting name="cacheEnabled" value="true" />`
    2. 去 mappper.xml 中配置使用二级缓存
        `<cache></cache>`
    3. POJO 需要实现序列化接口

### 相关设置
- eviction: 缓存的回收策略
    - LRU 最近最少使用的，移除最长时间不被使用的对象 **默认选择**
    - FIFO 先进先出，按对象进入缓存的顺序来移除它们
    - SOFT 软引用，移除基于垃圾回收器状态和软引用规则的对象
    - WEAK 弱引用，更积极地移除基于垃圾收集器状态和弱引用规则的兑现相关
- flushInterval: 缓存刷新间隔（毫秒）
    - 缓存多长时间清空一次，默认不清空（缓存仅仅对调用语句时刷新）
- readOnly: 是否只读
    - true: 只读缓存
        - 返回调用者对象的相同实例
        - MyBatis 认为所有从缓存中或获取数据的操作都是只读操作，不会修改数据
        - MyBatis 为了加快获取速度，直接就会将数据在缓存中的引用交给用户，不安全，速度快
    - false: 读写缓存
        - 返回缓存对象的拷贝（通过序列化）
        - MyBatis 认为获取的数据可能会被修改
        - MyBatis 会利用序列化 & 反序列化的技术克隆一份新的数据给你，安全，速度慢
- size: 引用数目（正整数）
    - 代表缓存最多可以存储多少个对象，太大容易导致内存溢出
- type: 自定义缓存的全类名
    - 实现 Cache 接口即可

## 缓存相关配置总结
1. cacheEnabled=true false 关闭缓存（二级缓存）
2. select 标签属性：useCache="true"
    false: 不使用缓存（二级缓存）
3. sql 标签属性：flushCache="true" 一级/二级都会清除
    - 增删改默认 true
    - 查询默认 false
    - 增删改执行完成后就会清除缓存（一级/二级）
    - 查询标签：flushCache="false"
        - 如果 flushCache=true 每次查询之后都会清空缓存，缓存是没有被使用的
4. sqlSession.clearCache(); 只是清除当前 session 的一级缓存
5. localCacheScope: 本地缓存作用域（一级缓存 Session） 当前会话的所有数据保存在会话缓存中
    - STATEMENT: 可以禁用一级缓存
    - 当在某一个作用域（一级缓存 Session/二级缓存 namespaces）进行了 C/U/D 操作后，默认**该作用域下所有 select 中的缓存将被 clear**

## 第三方缓存整合
> EhCache 是一个纯 Java 的进程内缓存框架，具有快速、精干等特点，是Hibernate中默认的CacheProvider 
> MyBatis 定义了 Cache 接口方便我们自定义扩展

步骤：
1. 导入 ehcache 包，以及整合包，日志包
```xml
    <!-- https://mvnrepository.com/artifact/net.sf.ehcache/ehcache-core -->
    <dependency>
        <groupId>net.sf.ehcache</groupId>
        <artifactId>ehcache-core</artifactId>
        <version>2.6.11</version>
    </dependency>

    <dependency>
        <groupId>org.mybatis.caches</groupId>
        <artifactId>mybatis-ehcache</artifactId>
        <version>1.2.1</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-api -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-api</artifactId>
        <version>1.7.32</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/org.slf4j/slf4j-log4j12 -->
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.32</version>
        <scope>test</scope>
    </dependency>
```

2. 编写 ehcache.xml 配置文件
3. 配置 cache 标签
   - `<cache type="org.mybatis.caches.ehcache.EhcacheCache" />`

**参照缓存**：若想在命名空间中共享相同的缓存配置和实例，可以使用 cache-ref 元素来引用另外一个缓存
```xml
    <cache-ref namespace="org.rabbit.mybatis.dao.EmployeeMapper" />
```