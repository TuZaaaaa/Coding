# Nacos
- Dynamic Naming and Configuration Service

- 服务注册步骤
  - 引入依赖
  - 配置地址
- 服务发现
  - 使用注解：@EnableDiscoveryCient
  - DiscoveryClient
  - NacosServiceDiscovery

## 案例模拟

### 下单
1. 发起下单请求
2. 远程查询订单中的商品
3. 返回商品数据
4. 返回订单数据

## 注册中心
- @LoadBalanced
- 思考题：注册中心宕机，是否还能成功进行远程调用
  - 引入实例缓存
    - 首次调用，需要先请求注册中心
    - 调用过，不再依赖注册中心
## 配置中心
- 配置集中管理
- 配置推送
  - 不停机配置更新
- 思考题：Nacos 中的数据集和 application.yaml 的配置项相同，哪个优先级更高？
  - 先导入优先、外部优先
- 数据隔离
  - 多环境：dev, test, prod
  - namespace(多套环境) -> group(多种微服务) -> Data-id(多种配置)

