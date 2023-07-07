# Command

- keys *: 查看当前库
- exists key: 判断 key 是否存在
- type key: 查看 key 类型
- del key: 删除指定 key
- unlink key: 非阻塞删除，先将 keys 从 keyspace 元数据中删除，真正删除，在后续异步中操作
- ttl key: time to live 查看还有多少秒过期，-1 永不过期，-2 已过期
- expire key time: 为给定的 key 设置过期时间(s)
- move key dbindex[0-15]: 将当前 db 的 key 移动到给定的 db 当中
- select dbindex[0-15]: 切换 db，default: 0
- dbsize: 查看当前 db key 数量
- flushdb: 清空当前库
- flushall: 通杀所有库


> Tips: redis.conf 可以修改 db 的数量
