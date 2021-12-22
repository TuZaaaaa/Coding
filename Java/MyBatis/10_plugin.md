# plugin
> MyBatis在四大对象的创建过程中，都会有插件进行介入
> 插件可以利用动态代理机制一层层的包装目标对象，而实现在目标对象执行目标方法之前进行拦截的效果。 
> MyBatis 允许在已映射语句执行过程中的某一点进行拦截调用

默认情况下，MyBatis 允许使用插件来拦截的方法调用包括：
- Executor (update, query, flushStatements, commit, rollback, getTransaction, close, isClosed) 
- ParameterHandler (getParameterObject, setParameters) 
- ResultSetHandler (handleResultSets, handleOutputParameters) 
- StatementHandler (prepare, parameterize, batch, update, query)

## 插件原理
在四大对象创建的时候
1. 每个创建出来的对象不是直接返回的，而是调用 `interceptorChain.pluginAll(parameterHandler)`
2. 获取到所有 Interceptor（拦截器）（插件需要实现的接口）
    调用 `interceptor.plugin(target);` 返回 target 包装后的对象
3. 插件机制，我们可以使用插件为目标对象创建一个代理对象（AOP）
    - 插件可以为四大对象创建出代理对象
    - 代理对象可以拦截到四大对象的每一个执行
4. 按照插件注解声明，按照插件配置顺序调用插件 plugin 方法，生成被拦截对象的动态代理
5. 多个插件依次生成目标对象的代理对象，层层包裹，先声明的先包裹；形成代理链
6. 目标方法执行时依次从外到内执行插件的 intercept 方法。
7. 多个插件情况下，我们往往需要在某个插件中分离出目标对象
   - 可以借助 MyBatis 提供的 SystemMetaObject 类来进行获取最后一层的 h 以及 target 属性的值

> Interceptor 接口
> - Intercept: 拦截目标方法执行
> - plugin: 生成动态代理对象，可以使用 MyBatis 提供的 Plugin 类的 wrap() 方法
> - setProperties: 注入插件配置时设置的属性

## 插件编写
开发步骤：
- 编写插件实现 Interceptor 接口，并使用 @Intercepts 注解完成插件签名
```java
    @Intercepts(
            {
                @Signature(type = StatementHandler.class, method = "parameterize", args = java.sql.Statement.class)
            })
    public class MyFirstPlugin implements Interceptor {
```
- 在全局配置文件中注册插件
```xml
    <plugin interceptor="org.rabbit.mybatis.dao.MyFirstPlugin">
        <property name="username" value="root"/>
        <property name="password" value="root"/>
    </plugin>
```

## 应用场景
- 从代理链中分离真实被代理对象
```java
    // 1. 分离代理对象。由于会形成多次代理
    // 所以需要通过一个 while 循环分离出最终被代理对象，从而方便提取信息
    MetaObject metaObject = SystemMetaObject.forObject(target);
    while (metaObject.hasGetter("h")) {
        Object h = metaObject.getValue("h");
        metaObject = SystemMetaObject.forObject(h);
    }
    // 2. 获取到代理对象中包含的被代理的真实对象
    Object obj = metaObject.getValue("target");
    // 3. 获取被代理对象的MetaObject方便进行信息提取
    MetaObject forObject = SystemMetaObject.forObject(obj);
```