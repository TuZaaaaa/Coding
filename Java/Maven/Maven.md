# Q & A
- 不支持发行版本 5
> jdk9 依赖问题
pom.xml:
```xml
    <properties>
        <maven.compiler.source>1.9</maven.compiler.source>
        <maven.compiler.target>1.9</maven.compiler.target>
    </properties>
```

- Class Not Found com.mysql.jdbc.Driver
> 在maven工程中引包的时候
> 1. provided在war包的时候不会生成，但是在测试和编译的时候是通过的
> 2. log4j要配置才能看到一些错误信息，发现缺少com.mysql.jdbc.Driver，但是明明有，之后去查看pom文件发现将其加了provided，所以在war包中无法引入，而test下却可以使用。
