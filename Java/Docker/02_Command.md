# Command

## 帮助命令
```shell
    docker Version
    docker info
    docker --help
```

## 镜像命令
### docker iamges
> 列出本地主机上的镜像
- option
    - -a 列出本地所有的镜像（含中间映射层）
    - -q 只显示镜像 ID
    - --digests 显示镜像的摘要信息
    - --no-trunc 显示完整的镜像信息

选项说明：
- REPOSITORY
  - 表示镜像的仓库源
- TAG
  - 镜像的标签
- IMAGE ID
  - 镜像 ID
- CREATED
  - 镜像创建时间
- SIZE
  - 镜像大小
> 同一个仓库源可以有多个 TAG，代表这个仓库源的不同版本，我们使用 REPOSITORY:TAG 来定义不同的镜像
> 如果不指定TAG，将默认使用 latest 标签

### docker search
> 查询镜像（来自 https://hub.docker.com）
- option
  - -f stars=30 tomcat 根据 star 进行筛选
  - --no-trunc 完成的镜像信息

### docker pull
> 拉取镜像

### docker rmi
> 删除镜像
> 删除多个镜像使用空格分隔
- option
  - -f 强制删除
删除所有镜像
```shell
  docker rmi -f $(docker images -qa)
```

## 容器命令
### 新建并创建容器
- docker run [options] iamge [command][arg...]
- option
  - --name = “容器新名字”：为容器指定一个名称
  - -d 后台运行容器，并返回容器 ID，即启动守护式容器
  - -i 以交互模式运行容器，通常与 -t 同时使用
  - -t 为容器重新分配一个伪输入终端，通常与 -i 同时使用
  - -it **交互式终端**
  - -P 随机端口映射
  - -p 指定端口映射，有以下四种格式
    - ip:hostPort:containerPort
    - ip:containerPort
    - hostPort:containerPort
    - containerPort

### 列出当前所有正在运行的容器
- docker ps
- option
  - -a 列出当前所有**正在运行**的容器 + **历史上运行过**的
  - -l 显示最近创建的容器
  - -n 显示最近 n 个创建的容器
  - -q 静默模式，只显示容器编号
  - --no-trunc 不截断输出

### 退出容器
- exit 容器停止退出
- crtl+p+q 容器不停止退出

### 启动容器
- docker start 容器 ID 或容器名称

### 重启容器
- docker restart 容器 ID 或容器名称

### 停止容器
- docker stop 容器 ID 或容器名称

### 强制停止容器
- docker kill 容器 ID 或容器名称

### 删除已停止的容器 
- docker rm 容器 ID

### 一次性删除多个容器
- docker rm -f $(docker ps -a -q)
- docker ps -a -q | xargs docker rm

### 启动守护式进程
- docker run -d 容器名称
> 问题：docker ps -a 进行查看时，会发现容器已经退出
> Docker 容器后台运行，就必须有一个前台进程
> 容器运行的命令，如果不是那些**一直挂起的命令**（比如运行 top, tail），就会自动退出
> docker 机制：以 Nginx 为例，正常情况下，我们配置启动服务只需要启动相应的 service 即可，例如 service nginx start
> 但是，这样做，nginx 为后台进程模式运行，就导致 docker 前台没有运行的应用
> 这样的容器后台启动后，会立刻退出因为它觉得它没事做了
> 最佳的解决方案：将要运行的程序以前台进程的形式运行

### 查看容器日志
- docker logs -f -t --tail 容器 ID
- option
  - -t 加入时间戳
  - -f 跟随最新的日志打印
  - --tail 数字 显示最后多少条

### 查看容器内运行的进程
- docker top 容器 ID

### 查看容器内部细节
- docker inspect 容器 ID

### 进入正在运行的容器并以命令行交互
- docker exec -it 容器 ID bash
- docker attach 容器 ID
> 上述两个区别
> - attach
>   - 直接进入容器启动命令的终端，不会启动新的进程
> - exec
>   - execute 是在容器中打开新的终端，并且可以启动新的进程

### 从容器内拷贝文件到主机上
- docker cp 容器 ID：容器内路径 目的主机路径


