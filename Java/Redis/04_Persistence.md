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