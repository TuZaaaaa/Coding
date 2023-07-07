# sql_execute
> 一条 SQL 语句执行过程

- 客户端输入 SQL 语句
- SQL 语句通过网络到达数据库实例
- server process 接收 SQL 语句
  - 解析为执行计划
    - 占用资源：CPU, IO
  - 执行
    - 首先去 buffer cache 进行查找(logical IO)
    - 如果没有则去 dbf 文件中进行查找，结果集放入 buffer cache 之后，再返回给用户(physical IO)
  - (optional) 如果是 DML
    - 将会产生日志，放入 redo log buffer
      - lgwr 将 log 写入磁盘
    - dbwn 进程将修改的内容写入磁盘


