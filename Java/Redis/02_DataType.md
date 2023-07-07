# DataType

## 1. Strings
> 二进制安全（意味着可以包含任何数据，包括图片和序列化的对象，最多为 512M）

## 2. Lists
> String List，按照插入顺序排序，可以从头/尾添加
> 底层是双端链表 最多可以包含 2^32 - 1 个元素，40亿个左右

## 3. Hashes
> string type field and value map
> 适合用于存储对象 每个 hash 可以存储 2^32 - 1 个元素，40亿个左右

## 4. Sets
> String type 无序集合
> 编码是 intset/hashtable
> 底层实现是哈希表 最多可以包含 2^32 - 1 个元素，40亿个左右

## 5. Sorted sets
> 和 Sets 类型
> 不同点：每个元素关联一个 double type score，sort by score
> 成员唯一，socre 不唯一

## 6. Geospatial
> 存储地理位置信息
> 计算位置之间的距离

## 7. HyperLogLog
> 用来做**基数统计**
> 输入元素数量或者体积很大时，计算基数所需空间总是固定且很小的
> 每个键只需要花费 12KB 内存，就可以计算接近 2^64 个不同元素的基数
> 不会存储输入元素本身，无法返回输入的元素

## 8. Bitmap
> 位图，由 0 和 1 状态表现的二进制位的 bit 数组

## 9. Bitfield
> 位域，一次性操作多个比特位域（连续的多个比特位）

## 10. Streams
> 主要用于 MQ
> 本身有 pub/sub 实现消息队列的功能，但无法持久化，如果出现网络断开、Redis 宕机，消息就会被丢弃
> 而 Streams 提供了消息的持久化和主备复制的功能，可以让任何客户端访问任何时刻数据，并且能记住每一个客户端的访问位置，还能保证消息不丢失
