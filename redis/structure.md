### 基本架构 一个键值数据库至少需要哪些模块
回答：

1. 访问框架

   可选方案：

   - 网络访问框架 （Redis, Memcached）
   - 动态库访问 (RocksDB)

2. 索引模块

   可选方案：

   - hash （Redis，Memcached）
   - skiplist （ RocksDB）

3. 操作模块

   - GET
   - SET
   - SCAN
   - DELETE

4. 存储模块

   内存 磁盘

   - 数据类型

     STRING LIST HASH SET 

   - 分配器
   - 持久化

