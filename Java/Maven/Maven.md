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