### 普通索引和唯一索引，应该怎么选择？

关键词： ***change buffer***

mysql为了节省随机读磁盘的IO消耗将 insert / update / delete 影响的不在 buffer pool 的二级索引页缓存起来

适合写多读少的场景，写完以后不需要马上读

回答:

从性能角度优先考虑非唯一索引，在业务层面进行唯一性校验。原因在于：

1. 对读来说 唯一索引可以找到一个满足条件的记录后就停止查找，但是性能差距不大
2. 对写来说 唯一索引每次都需要校验是否违反唯一性约束，无法使用 change buffer 进行优化



### mysql 为什么有时候会选错索引，怎么解决？

关键词: ***优化器*** ***采样统计*** 

回答：

优化器并不能准确的知道哪个索引区分度比较高，而是通过采用统计来估算，可能是由于统计信息不准确导致的。

解决方案：

1. analyze table 重新统计索引情况
2. 使用force index 强行指定原理
3. 修改语句来引导优化器
4. 增加或者删除索引



### 如何给字符串字段加索引？

关键字： ***前缀索引*** ***索引额外占用的空间*** ***查询效率***

1. 直接创建完整索引 比较占用空间
2. 创建前缀索引 节省空间 但是会增加查询扫描次数 并且不能使用覆盖索引
3. 倒序存储，再创建前缀索引，用于解决字符串本身前缀的区分度不够的问题
4. 创建 hash 字段并添加索引，查询性能稳定，有额外的存储和计算消耗，不支持范围查询


