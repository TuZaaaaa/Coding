# Introduce
> Redis -> Remote Directory Server
> 基于内存的 KV 键值对内存数据库

## 使用场景以及主流应用

- 作为 DB 前的中间件，缓存查询的数据
  - 查询先走 Redis，如果没有数据，则走 DB，然后写到 Redis
- 内存存储和持久化（RDB + AOF），支持异步将内存中的数据写入磁盘
  - AOF: append only file
- 高可用架构
  - 单机
  - 主从
  - 哨兵
  - 集群
- 分布式锁
- 简单的消息队列
- 计数器、排行榜，点赞

## Install

### Install from Source
```bash
wget https://download.redis.io/redis-stable.tar.gz
tar -xzvf redis-stable.tar.gz
cd redis-stable
make && make install
```

- Default install path: `/usr/local/bin`
  - tools:
    - redis-benchmark: 性能测试
    - redis-check-aof: 修复有问题的 AOF 文件
    - redis-check-dump: 修复有问题的 dump.rdb 文件
    - redis-cli: 客户端，操作入口
    - redis-sentinel: 哨兵，redis 集群使用
    - redis-server: Redis 服务端启动命令


## Initial Configuration

- daemonize yes
- protected-mode no
- bind 127.0.0.1 注释掉，或改成本机 IP
- requirepass 设置密码

## Run

- Run with custom Configuration
```bash
redis-server /custom/config/path
```
- check is running
```bash
ps -ef | grep redis
```
- connect with redis-cli
```bash
redis-cli -a 'password' -p 6379
```

## Close
```bash
# 单实例关闭 默认 6379
redis-cli -a password shutdown
# 多实例关闭，指定端口
redis-cli -a password -p port shutdown
```

  
## Uninstall

- shutdown redis service
- 删除 /use/local/lib 下与 redis 相关的文件
  - `ls -l /usr/local/bin/redis-*`
  - `rm -rf /usr/local/bin/redis-*`