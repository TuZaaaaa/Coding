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
1. 方案一
    > 在maven工程中引包的时候
    > 1. provided在war包的时候不会生成，但是在测试和编译的时候是通过的
    > 2. log4j要配置才能看到一些错误信息，发现缺少com.mysql.jdbc.Driver，但是明明有，之后去查看pom文件发现将其加了provided，所以在war包中无法引入，而test下却可以使用。

2. 方案二
    > Intellj 自动载入Mave依赖的功能很好用，但有时候会碰到问题，导致pom文件修改却没有触发自动重新载入的动作，此时需要手动强制更新依赖。 
    > 如下：

    > 1. 手动删除 Project Settings 里面的 Libraries 内容
    > 2. 在 Maven Project clean一下，删除之前编译过的文件
    > 3. Maven reload