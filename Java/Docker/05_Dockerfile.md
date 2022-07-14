# Dockerfile
> Dockerfile 是用来构建 Docker 镜像的构建文件，是由一系列命令和参数构成的脚本

如何构建？
- 编写 Dockerfile
- docker build
- docker run

## Dockerfile 编写规范
1. 每条保留字指令都必须为大写字母且后面要跟随至少一个参数
2. 指令按照从上到下，顺序执行
3. `#` 表示注释
4. 每条指令都会创建一个新的镜像层，并对镜像进行提交

## Docker 执行 Dockerfile 大致流程
1. Docker 从基础镜像运行一个容器
2. 执行一条指令并对容器做出修改
3. 执行类似 `docker commit` 的操作提交一个新的镜像层
4. Docker 再基于刚提交的镜像运行一个新容器
5. 执行 Dockerfile 中的下一条指令直到所有指令都执行完成

> Dockerfile - Docker 镜像 - Docker 容器
> - Dockerfile：软件的原材料
>   - 面向开发
> - Docker 镜像：软件的交付品
>   - 交付标准
> - Dockers 容器：可以认为是软件的运行态
>   - 涉及部署与运维

## Dockerfile 保留字指令
- FROM
  - 基础镜像，当前镜像是基于哪个镜像
- MAINTAINER
  - 镜像维护者的姓名和邮箱地址
- RUN
  - 容器构建时需要运行的命令
- WORKDIR
  - 指定在创建容器后，终端默认登录进来的工作目录，一个落脚点
- ENV
  - 用来在构建镜像过程中设置环境变量
- ADD
  - 将宿主机目录下的文件拷贝进镜像且 `ADD` 命令会自动处理 URL 和解压 tar 压缩包
- COPY
  - 类似 `ADD`，拷贝文件和目录到镜像中
  - 将从构建上下文目录中<源路径>的文件/目录复制到新的一层镜像内的<目标路径>位置
    - COPY src dest
    - COPY ["src", "dest"]
- VOLUME
  - 容器数据卷，用于数据保存和持久化工作
- CMD
  - 指定一个容器启动时要运行的命令
  - Dockerfile 中可以有多个 CMD 命令，但只有最后一个生效，CMD 会被 `docker run` 之后的参数替换 
- ENRTYPOINT
  - 指定一个容器启动时要运行的命令
  - ENRTYPOINT 的作用和 CMD 一样，都是在指定容器启动程序及参数
- ONBUILD
  - 当构建一个被继承的 Dockerfile 时运行命令，父镜像在被子镜像继承后，父镜像的 onbuild 被触发

总结：
| BUILD | Both | RUN |
| --- | --- | --- |
| FROM | WORKDIR | CMD |
| MAINTAINER | USER | ENV |
| COPY | | EXPOSE|
| ADD | | VOLUME |
| RUN | | ENTRYPOINT |
| ONBUILD | | |
| .dockerignore | | |

## Dockerfile 案例
### 自定义 mycentos
需要自定义镜像具备的功能
1. 登录后的默认路径
2. vim 编辑器
3. 查看网络配置 ifconfig 支持

#### 实现
1. 编写
   1. 编写 DockerFile

2. 构建
   - `docker build -t 镜像名:TAG`
3. 运行
   - `docker run -it 镜像名:TAG`
3. 列出镜像变更历史
   - `docker history 镜像名`

### CMD&ENTRYPOINT 命令区别
> 都是指定一个容器启动时要运行的指令
- CMD
  - Dockerfile 中可以有多个 CMD 指令，但只有最后一个生效，CMD 会被 `docker run` 之后的参数替换
- ENTRYPOINT
  - `docker run` 之后的参数会被当做参数传递给 ENTRYPOINT，之后形成新的命令组合
  - case
    - 制作 CMD 版可以查询 IP 信息的容器（已失效）

### ONBUILD 命令案例
...

### 自定义 tomcat9
1. `mkdir -p /tuzaaaaa/mydockerfile/tomcat9`
2. 在上述目录下 `touch c.txt` 
3. 将 jdk 和 tomcat 安装的压缩包拷贝进上一步目录
   1. apache-tomcat-9.0.8.tar.gz
   2. jdk-8u171-linux-x64.tar.gz
4. 在 /tuzaaaaa/mydockerfile/tomcat9 目录下新建 Dockerfile 文件
```Dockerfile
  FROM centos
  MAINTAINER tuzaaaaa<2677824148wjt@gmail.com>
  # 把宿主机当前上下文的 c.txt 拷贝到容器 /usr/local/ 路径下
  COPY c.txt /usr/local/cincontainer.txt
  # 把 java 与 tomcat 添加到容器中
  ADD jdk-8u171-linux-x64.tar.gz /usr/local/
  ADD apache-tomcat-9.0.8.tar.gz /usr/local/
  # 安装 vim 编辑器
  RUN yum install vim
  # 设置工作访问时候的 WORKDIR 路径，登录落脚点
  ENV MYPATH /usr/local
  WORKDIR $MYPATH
  # 配置 java 与 tomcat 环境变量
  ENV JAVA_HOME /usr/local/jdk1.8.0_171
  ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
  ENV CATALINA_HOME /usr/local/apache-tomcat9.0.8
  ENV CATALINA_BASE /usr/local/apache-tomcat9.0.8
  ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
  # 容器运行时监听的端口
  EXPOSE 8080
  # 启动时运行 tomcat
  # ENTRYPOINT ["/usr/local/apache-tomcat-9.0.8/bin/startup.sh"]
  # CMD ["/usr/local/apache-9.0.8/bin/catalina.sh", "run"]
  CMD /usr/local/apache-9.0.8/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.8/bin/logs/catalina.out
```
5. 构建
`docker build -t tuzaaaaatomcat9`
6. run
```shell
  docker run -d -p 8080:8080 --name myt9
  -v /tuzaaaaa/mydockerfile/tomcat9/test:/usr/local/apache-tomcat-9.0.8/webapps/test
  -v /tuzaaaaa/mydockerfile/tomcat9/tomcat9logs/:/usr/local/apache-tomcat-9.0.8/logs
  --privileged=true tuzaaaaatomcat9
```
7. 验证
`docker ps`
8. 结合前述的容器卷将测试的 web 服务 test 发布
