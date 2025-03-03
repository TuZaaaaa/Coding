# Seata
- 分布式事务

## 架构
- TC(Transaction Coordinator)
  - 维护全局和分支事务的状态，驱动全局事务提交或回滚
- TM(Transaction Manager)
  - 定义全局事务的范围：开始全局事务，提交或回滚全局事务
- RM(Resource Manager)
  - 管理分支事务处理的资源，与 TC 通信以注册分支事务的报告分支事务的状态，并驱动分支事务提交或回滚

## 二阶提交
- 配置 undo_log 机制
