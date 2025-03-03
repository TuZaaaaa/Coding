# Intro

## 课程内容
- Srping Cloud
  - OpenFeign RPC 远程调用
  - Gateway 网关
- Srping Cloud Alibaba
  - Nacos 注册中心
  - Sentinel 服务熔断
  - Seata 分布式事务

## 分布式基础

### 架构演进

- 单体
  - 用户 -> 域名 -> 节点（应用、数据库）
  - 优点：部署方便
  - 缺点：存在高并发问题
- 集群
  - 用户 -> 域名 -> 网关 -> 节点（应用、数据库）
  - 优点：负载均衡、扩缩容
  - 缺点：无法模块化升级、多语言团队
- 分布式
  - 独立部署、数据隔离、语言无关
  - 注册中心（同时也是配置中心）
    - 服务发现、服务注册
    - 服务-IP 表
  - 通信：RPC(http+json)
  - 服务熔断（引入 fail-fast 解决） -> 服务雪崩
  - 分布式事务

### 案例项目构建

- 框架
  - SpringBoot 3.3.4
  - SpringCloud 2023.0.3
  - SpringCloud Alibaba 2023.0.3.2
- 组件
  - Nacos 2.4.3
  - Sentinel 1.8.8
  - Seata 2.2.0
- 结构
  - cloud-demo
    - serivces
      - service-product
      - service-order
