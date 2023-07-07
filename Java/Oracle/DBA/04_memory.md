# memory

- buffer cache: 区域
- cache buffer: 内存单元
- buffer header 存放对应数据块的概要信息，如文件号、块地址、状态等

cache buffer 通过 hash 算法计算分成 hash chain，放在不同 bucket 里

## LRU algorithm

> LRU(least recently used)
- lru 列表
  - 分为热端和冷端
  - 当插入新的 buffer 且列表满了时，移除冷端的 buffer，插入新的 buffer 至冷热分界线
  - 当冷端某数据块访问次数超过一定数量，则移动到最热端，并将访问次数清零
- lruw 列表
  - 用于存放 dirty buffer（等待 dbwr 写入磁盘）

