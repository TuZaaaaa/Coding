# shared_pool
> 共享池，目的是共享 SQL
> 如果没有 shared pool 则执行 n 次，解析 n 次
> 如果有 shared pool 则仅需解析 1 次
> 不过使用共享池的过程中也会有开销（所以 SQL 复用率不高时，可能会成为负担）


## 组成
- library cache 库高速缓存
  - include: sql 语句，sql 解析码，执行计划，procedure, function, 包
  - v$librarycache
- data dictionary cache 数据字典高速缓存，相对稳定
  - include: 表，视图的定义、结构、信息，用户权限等
- result cache memory 11g 新增
  - 直接返回结果集
- chunk 共享池 内存最小单元 16 bytes, 20...
  - 不同大小的 chunk 放在不同的 bucket 中，用于快速定位 buffer


## 定位流程
- 用户提交 SQL
- 将 SQL 进行 ASCII 转码
- 进行 hash 运算
- 定位到某个 bucket

