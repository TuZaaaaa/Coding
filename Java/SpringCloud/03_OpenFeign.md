# OpenFeign
- 远程调用

## OpenFeign vs RestTempalte
- OpenFeign(声明式 REST 客户端)
- RestTempalte(编程式 REST 客户端)

## Tip
- 业务 API: 直接复制目的 Controller 签名
- 三方 API: 参考接口文档

## 面试题
- 客户端负载均衡 vs 服务端负载均衡

## 进阶配置
- 日志
- 超时控制
  - connectTimeout
  - readTimeout
- 重试机制
  - 退避算法（Backoff Algorithm）：定期重试失败的请求，并不断增加各次请求之间的延迟时间（之前总时间 * 因子）
- 拦截器
  - 请求&响应
- 兜底返回（Fallback）
  - 需要整合 Sentinel
