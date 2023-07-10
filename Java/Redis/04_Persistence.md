# Persistence
> 数据持久化

## RDB
> Redis Database
> snapshot of your dataset specified intervals


### Config

- snapshotting intervals
  - save <seconds> <changes>
- dump file save path
  - dir /my_redis/dumpfiles
  - check dir config
    - `config get dir`
- dump file name
  - dbfilename dump6379.rdb


### Backup and Restore

- resotre: move **dump.rdb** to **dump file save path**
- manually call
  - save
    - 主程序会**阻塞**当前 redis 服务器，**线上禁止使用**
  - bgsave
    - 后台异步进行
  - fundamentals
    - fork child process
    - write dataset to temp rdb file
    - when writing is done, new rdb file replaces the old one

### Snapshotting circumstances
- snapshotting config
- manually save/bgsave
- execute flushall/flushdb
- exexute shuwdown and not enable aof
- 主从复制，主节点自动触发

### Disable rdb
- `redis-cli config set save ""`

## AOF
> Append Only File
> 以日志的方式记录每个写操作， 只能追加文件，不能改写文件
> redis 启动时会读取该文件重新构建数据（将所有命令执行一遍）

### Enable AOF
> Default is disable
```txt
appendonly yes
```


### AOF WorkFlow

1. Client 不断进行请求
2. Redis server 写入 AOF buffer
3. 根据**同步文件的三种写回策略**写入 AOF
4. 随着 AOF 文件内容的增加，会根据规则进行命令的合并（**AOF 重写**），压缩 AOF 文件
5. Redis Server 重启，从 AOF 载入数据


### 三种写回策略
> Default is appendfsync everysec

- Always: 同步写回，每个写命令执行完，立刻同步将日志写回磁盘
- no: 操作系统控制写回，每个写命令执行完，只是先把日志写到 AOF 文件的内容缓存区
- everysec: 每秒写回，每个写命令执行完，只是先把日志写到 AOF 文件的内容缓存区，每隔 1s 将缓存区的内容写入磁盘

 
### AOF file location/name

- file location
  - version 6: 和 rdb 文件夹一致，通过 `dir` 设置
  - version 7: 在配置的 `dir` 下新建目录，通过 `appenddirname` 设置
- file name
  - `appendfilename` 设置
  - version 6: 仅有一个
  - version 7: 分成多个部分
    - BASE: 基础 AOF，由子进程通过重写产生，最多只有一个
    - INCR: 增量 AOF，在 AOFRW 开始执行时被创建，可能存在多个
    - HISTORY: 历史 AOF，由前两部分变化而来，每次 AOFRW 完成时，本次 AOFRW 之前对应的 BASE/INCR 都会变成 HISTORY
      - 会被 Redis 自动删除，看不到文件的存在
    - manifest: 清单文件，跟踪、管理 AOF
     

### AOF Log rewriting
> 压缩 AOF 文件，只保留可以恢复数据的最小指令集

- 自动
  - 当 AOF 文件大小超过所设定的峰值时，自动启动 AOF 文件的内容压缩
  - 同时满足以下两个条件
    - auto-aof-rewrite-percentage 100
    - auto-aof-rewrite-min-size 64mb
- 手动
  - 手动使用命令 `bgrewriteaof` 来重写


## RDB+AOF 混合
> Default is enable, `aof-use-rdb-preamble yes`
- AOF 优先级更高
