# Replication
> 主从复制
> master: 写，slave: 读
> 当 master 数据变化时，自动将新的数据异步同步到其他 slave

## 作用
- 读写分离
- 容灾恢复
- 数据备份
- 水平扩容支撑高并发

## Usage
> 配从库不配主库

- 权限
  - slave 通过 masterauth 设置校验密码

## Command
- info replication: 查看复制节点的主从关系和配置信息
- replicaof masterIP masterPort: 在从库配置文件中设置
- slaveof masterIP masterPort: 每次与 master 断开之后，都需要重新连接
  - 在运行期间修改 slave 节点的信息，如果已经是某个主库的从库，那么会停止和原主库的同步，转而和新主库同步
- slaveof no one: 停止与其它库的同步，转为主库


## 工作流程
1. slave 启动成功连接到 master 后会发送一个 sync 命令
2. salve 首次全新连接 master，将会执行一次完全同步（全量更新）将被自动执行，slave 原数据将会被覆盖
3. master 收到 sync 命令后会开始在后台保存快照（主从复制会触发 RDB），同时收集所有接收到的修改数据集命令缓存起来，master 节点执行 RDB 后，将 rdb 和所有缓存的命令发送到 slave，完成一全量同步
4. slave 在接收到数据库文件数据后，将其存盘并加载到内存中，从而完成复制初始化
5. 心跳包：`repl-ping-replica-period seconds` default period is 10
6. 进入平稳，增量复制，依次将修改的命令传给 slave，完成同步 
7. 从机下线，重连续传：master 检查 backlog 里的 offset，master/slave 都会保存一个复制的 offset 和 masterId，offset 保存在 backlog 里，master 只会把已复制的 offset 后面的数据赋值给 slave，类似断点续传
 
## 痛点和改进需求
- 复制延时，信号衰减
- 默认情况下，slave 挂了之后，不会在 slave 中自动重选一个 master