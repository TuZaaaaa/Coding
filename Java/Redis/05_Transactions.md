# Transactions
> 一组命令的集合，存入队列中
> 一个事务所有命令序列化后，按顺序地串行化执行


## Command
- MULTI: 标记一个事务的开始
- EXEC: 执行事务块的所有命令
- DISCARD: 取消事务，放弃执行事务块的所有命令
- WATCH key[key ...]: 监视一个（多个） key，如果在事务执行之前这个（多个）key 被其他命令所改动，那么事务将被打断（乐观锁）
- UNWATCH: 取消 WATCH 命令对所有 key 的监视