# Download

## 总体步骤
- 搜索镜像
- 拉取镜像
- 查看镜像
- 启动镜像
- 停止镜像
- 移除镜像

## 常用安装
### 安装 mysql
- 搜索镜像
`docker search mysql`
- 拉取镜像
`docker pull mysql:5.6`
- 查看镜像
`docker images`
- 启动镜像
```shell
    docker run -p 12345:3306 --name mysql
    -v /tuzaaaaa/mysql/conf:/etc/mysql/conf.d
    -v /tuzaaaaa/mysql/logs:/logs
    -v /tuzaaaaa/mysql/data:/var/lib/mysql
    -e MYSQL_ROOT_PASSWORD=123456
    -d mysql:5.6
```

### 安装 Redis
- 搜索镜像
`docker search redis`
- 拉取镜像
`docker pull mysql:3.2`
- 查看镜像
`docker images`
- 启动镜像
```shell
    docker run -p 6379:6379
    -v /tuzaaaaa/myredis/data:/data
    -v /tuzaaaaa/myredis/conf/redis.conf/usr/local/etc/redis/redis.conf
    -d redis:3.2 redis-server /usr/local/etc/redis/redis.conf --appendonly yes
```
  - 在主机 /tuzaaaaa/myredis/conf/redis.conf 目录下新建 redis.conf 文件
