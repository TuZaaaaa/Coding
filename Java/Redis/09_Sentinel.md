# Sentinel
> 哨兵监控
> 监控 master 主机是否故障，如果存在故障，根据投票数自动将某一个从库转换为新主库

## 功能
- 主从监控：监控主从 Redis 库是否运行正常
- 消息通知：哨兵可以将故障转移的结果发送给客户端
- 故障转移：如果 master 异常，将会进行主从切换
- 配置中心：客户端通过连接哨兵，获得当前 Redis 服务的主节点地址

## 配置
> sentinel.conf

```bash
# 设置要监控的 master，quorum 表示最少有几个哨兵认可客观下线，同意故障转移的法定票数
sentinel monitor <master-name> <ip> <redis-port> <quorum>
sentinel auth-pass <master-name> <password>
```

## 运行流程和选举原理
- 当一个 master 失效后，sentinel 选举出新 master，建议 sentinel 个数为奇数
- SDOWN: Subjectively Down 主观不可用，单个 sentinel 检查到 master 不可用
- ODOWN: Objectively Down 客观不可用，多个 sentinel 达成一致意见
- 当主节点被判断客观下线后，各个 sentinel 会进行协商，选举出一个 leader，并由 leader 进行 failover

### Raft 算法
> 先到先得，sentinel A 向 B 发送成为 leader 的申请，若 B 没有同意过其他 sentinel，则会同意 A 成为 leader


### Failover
- 某个 slave 被选中成为 master
  - 在剩余 slave 节点健康前提下：pripority > replication offset > Run ID
- leader 执行 salveof no one 使选出来的 slave to master，并通过 slaveof 让其他节点成为其 slave
- leader 使之前的 master 降级为 slave

## 使用建议
- 应该有多个，保证高可用
- 数量是奇数
- 配置应一致
