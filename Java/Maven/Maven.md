# Q & A
## 不支持发行版本 5
> jdk9 依赖问题

pom.xml:
```xml
    <properties>
        <maven.compiler.source>1.9</maven.compiler.source>
        <maven.compiler.target>1.9</maven.compiler.target>
    </properties>
```

## Class Not Found com.mysql.jdbc.Driver
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

## 配置阿里云镜像
.m2/settings.xml
```xml
    <settings>
        <mirrors>
            <mirror>
                <id>aliyun</id>
                <name>aliyun</name>
                <mirrorOf>central</mirrorOf>
                <!-- 国内推荐阿里云的Maven镜像 -->
                <url>https://maven.aliyun.com/repository/central</url>
            </mirror>
        </mirrors>
    </settings>
```

## Since Maven 3.8.1 http repositories are blocked. 
> 问题：自从 3.8.1 版本以后 Maven 不再支持 htpp 协议的仓库地址

解决：降级 Maven 版本至 3.6.3

## Maven is not working in Java 8 when Javadoc tags are incomplete 

> mvn package 报错
- 取消对 javaDoc 检查

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-javadoc-plugin</artifactId>
    <version>2.9</version>
    <executions>
        <execution>
            <id>attach-javadocs</id>
            <!-- <goals>
                <goal>jar</goal>
            </goals> -->
            <configuration>
              <failOnError>false</failOnError>
            </configuration>
        </execution>
    </executions>
</plugin>
```
